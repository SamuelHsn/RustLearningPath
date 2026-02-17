# Week 8: Concurrency - Fearless Parallelism

## Learning Objectives

By the end of this week, you will:
- Spawn and manage threads
- Use message passing for thread communication
- Share state safely with Mutex and Arc
- Understand async/await basics
- Compare Rust's concurrency to other languages

## Why Rust for Concurrency?

**Rust's superpower:** The ownership and type systems prevent data races at compile time!

**Other languages:**
- **C/C++:** Manual synchronization, easy to introduce data races
- **Java/C#:** Thread-safe but runtime errors possible (deadlocks, race conditions)
- **TypeScript/JavaScript:** Single-threaded by default, uses async (event loop)
- **Rust:** Compile-time guarantees of thread safety

**Rust's motto:** "Fearless concurrency" - if it compiles, it's thread-safe!

## 1. Threads

### Creating Threads

```rust
use std::thread;
use std::time::Duration;

fn main() {
    thread::spawn(|| {
        for i in 1..10 {
            println!("Number {} from spawned thread!", i);
            thread::sleep(Duration::from_millis(1));
        }
    });
    
    for i in 1..5 {
        println!("Number {} from main thread!", i);
        thread::sleep(Duration::from_millis(1));
    }
}
```

**Output:** Interleaved execution (non-deterministic order).

**Comparison:**
```typescript
// TypeScript (Node.js) - using worker threads
import { Worker } from 'worker_threads';
const worker = new Worker('./worker.js');
```

```java
// Java
new Thread(() -> {
    // thread code
}).start();
```

```csharp
// C#
Thread thread = new Thread(() => {
    // thread code
});
thread.Start();
```

### Waiting for Threads

```rust
use std::thread;

fn main() {
    let handle = thread::spawn(|| {
        for i in 1..10 {
            println!("Number {} from spawned thread!", i);
        }
    });
    
    for i in 1..5 {
        println!("Number {} from main thread!", i);
    }
    
    handle.join().unwrap();  // Wait for thread to finish
}
```

`join()` blocks the current thread until the spawned thread completes.

**Pattern:** Always join threads you care about completing!

### Moving Data into Threads

```rust
use std::thread;

fn main() {
    let v = vec![1, 2, 3];
    
    let handle = thread::spawn(move || {
        println!("Vector: {:?}", v);
    });
    
    // Can't use v here anymore - it was moved!
    
    handle.join().unwrap();
}
```

**Key point:** Use `move` to transfer ownership into the thread closure.

**Why?** The thread might outlive the scope where `v` was created. Rust prevents dangling references!

**Comparison:**
```java
// Java - no explicit move, references shared
List<Integer> v = Arrays.asList(1, 2, 3);
new Thread(() -> {
    System.out.println(v);  // Works, but potential race condition
}).start();
```

Rust forces you to think about ownership!

## 2. Message Passing

Rust's mantra: **"Do not communicate by sharing memory; instead, share memory by communicating."**

### Channels

Channels allow threads to send messages to each other:

```rust
use std::sync::mpsc;  // multiple producer, single consumer
use std::thread;

fn main() {
    let (tx, rx) = mpsc::channel();
    
    thread::spawn(move || {
        let val = String::from("hi");
        tx.send(val).unwrap();
        // val is moved, can't use it here!
    });
    
    let received = rx.recv().unwrap();
    println!("Got: {}", received);
}
```

**How it works:**
- `mpsc::channel()` creates a transmitter (tx) and receiver (rx)
- `send()` sends a value (moves it)
- `recv()` blocks until a value is available
- Values are moved through the channel

**Comparison:**
```typescript
// TypeScript/JavaScript - Promises and async/await
const promise = new Promise((resolve) => {
    resolve("hi");
});
const result = await promise;
```

```java
// Java - BlockingQueue
BlockingQueue<String> queue = new LinkedBlockingQueue<>();
// Producer
queue.put("hi");
// Consumer
String val = queue.take();
```

```csharp
// C# - Channel<T>
var channel = Channel.CreateUnbounded<string>();
await channel.Writer.WriteAsync("hi");
var val = await channel.Reader.ReadAsync();
```

### Sending Multiple Values

```rust
use std::sync::mpsc;
use std::thread;
use std::time::Duration;

fn main() {
    let (tx, rx) = mpsc::channel();
    
    thread::spawn(move || {
        let vals = vec![
            String::from("hi"),
            String::from("from"),
            String::from("the"),
            String::from("thread"),
        ];
        
        for val in vals {
            tx.send(val).unwrap();
            thread::sleep(Duration::from_secs(1));
        }
    });
    
    for received in rx {  // Iterate over received values
        println!("Got: {}", received);
    }
}
```

**Pattern:** Use `for` loop to receive all messages until channel closes.

### Multiple Producers

```rust
use std::sync::mpsc;
use std::thread;

fn main() {
    let (tx, rx) = mpsc::channel();
    
    let tx1 = tx.clone();  // Clone transmitter
    thread::spawn(move || {
        let vals = vec![String::from("hi"), String::from("from"), String::from("thread1")];
        for val in vals {
            tx1.send(val).unwrap();
        }
    });
    
    thread::spawn(move || {
        let vals = vec![String::from("more"), String::from("messages"), String::from("thread2")];
        for val in vals {
            tx.send(val).unwrap();
        }
    });
    
    for received in rx {
        println!("Got: {}", received);
    }
}
```

**mpsc = multiple producer, single consumer**

## 3. Shared State

Sometimes message passing isn't the right model. You need shared mutable state.

### Mutex<T> - Mutual Exclusion

```rust
use std::sync::Mutex;

fn main() {
    let m = Mutex::new(5);
    
    {
        let mut num = m.lock().unwrap();
        *num = 6;
    }  // Lock automatically released here
    
    println!("m = {:?}", m);
}
```

**How it works:**
- `Mutex::new(value)` wraps the value
- `lock()` acquires the lock, blocks if necessary
- Returns `MutexGuard<T>` (smart pointer)
- Lock released when `MutexGuard` goes out of scope

**Comparison:**
```java
// Java - synchronized
synchronized(lock) {
    // critical section
}
```

```csharp
// C# - lock
lock(lockObject) {
    // critical section
}
```

```c
// C - pthread_mutex
pthread_mutex_lock(&mutex);
// critical section
pthread_mutex_unlock(&mutex);
```

**Rust advantage:** Lock release is automatic (RAII pattern), can't forget to unlock!

### Sharing Mutex Between Threads

```rust
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    let counter = Arc::new(Mutex::new(0));
    let mut handles = vec![];
    
    for _ in 0..10 {
        let counter = Arc::clone(&counter);
        let handle = thread::spawn(move || {
            let mut num = counter.lock().unwrap();
            *num += 1;
        });
        handles.push(handle);
    }
    
    for handle in handles {
        handle.join().unwrap();
    }
    
    println!("Result: {}", *counter.lock().unwrap());  // 10
}
```

**Key types:**
- `Arc<T>` - Atomic Reference Counting (thread-safe reference counting)
- `Mutex<T>` - Mutual exclusion (locks)

**Why Arc?**
- `Rc<T>` isn't thread-safe
- `Arc<T>` uses atomic operations
- Slight performance cost, but safe across threads

**Pattern:** `Arc<Mutex<T>>` for shared mutable state across threads.

**Comparison:**
```java
// Java - AtomicInteger
AtomicInteger counter = new AtomicInteger(0);
// Multiple threads can safely increment
counter.incrementAndGet();
```

```csharp
// C# - Interlocked
int counter = 0;
Interlocked.Increment(ref counter);
```

### Multiple Mutexes

```rust
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    let data1 = Arc::new(Mutex::new(0));
    let data2 = Arc::new(Mutex::new(0));
    
    let data1_clone = Arc::clone(&data1);
    let data2_clone = Arc::clone(&data2);
    
    let handle = thread::spawn(move || {
        let mut d1 = data1_clone.lock().unwrap();
        *d1 += 1;
        
        let mut d2 = data2_clone.lock().unwrap();
        *d2 += 1;
    });
    
    handle.join().unwrap();
    
    println!("data1: {}, data2: {}", *data1.lock().unwrap(), *data2.lock().unwrap());
}
```

**Warning:** Deadlocks are still possible if locks are acquired in different orders!

```rust
// Thread 1: lock A, then lock B
// Thread 2: lock B, then lock A
// = Deadlock!
```

**Best practice:** Always acquire locks in the same order.

## 4. Send and Sync Traits

Rust's concurrency safety is enforced by two marker traits:

### Send Trait

**`Send`:** Types that can be transferred across thread boundaries.

```rust
// Most types are Send
let x = 5;
thread::spawn(move || {
    println!("{}", x);  // i32 implements Send
});

// Rc<T> is NOT Send (not thread-safe)
let x = Rc::new(5);
// thread::spawn(move || {
//     println!("{}", x);  // ❌ Error! Rc doesn't implement Send
// });

// Arc<T> IS Send (thread-safe)
let x = Arc::new(5);
thread::spawn(move || {
    println!("{}", x);  // ✅ OK
});
```

**Rule:** Only `Send` types can be moved to other threads.

### Sync Trait

**`Sync`:** Types that are safe to be referenced from multiple threads.

A type `T` is `Sync` if `&T` is `Send`.

```rust
// Most types are Sync
let x = 5;
let y = &x;  // Can share &i32 across threads

// Rc<T> is NOT Sync
// Cell<T> and RefCell<T> are NOT Sync (interior mutability without thread safety)

// Arc<T> IS Sync
// Mutex<T> IS Sync (provides interior mutability with thread safety)
```

**Key insight:** You rarely implement `Send` and `Sync` manually - they're automatically derived!

**Types that are NOT Send/Sync:**
- `Rc<T>` - not thread-safe reference counting
- `RefCell<T>` - not thread-safe interior mutability
- Raw pointers - unsafe, no guarantees

**Thread-safe alternatives:**
- Use `Arc<T>` instead of `Rc<T>`
- Use `Mutex<T>` or `RwLock<T>` instead of `RefCell<T>`

## 5. RwLock - Readers-Writer Lock

For multiple readers or single writer:

```rust
use std::sync::{Arc, RwLock};
use std::thread;

fn main() {
    let lock = Arc::new(RwLock::new(5));
    let mut handles = vec![];
    
    // Multiple readers
    for _ in 0..5 {
        let lock = Arc::clone(&lock);
        handles.push(thread::spawn(move || {
            let num = lock.read().unwrap();
            println!("Read: {}", *num);
        }));
    }
    
    // Single writer
    let lock_clone = Arc::clone(&lock);
    handles.push(thread::spawn(move || {
        let mut num = lock_clone.write().unwrap();
        *num += 1;
        println!("Wrote: {}", *num);
    }));
    
    for handle in handles {
        handle.join().unwrap();
    }
}
```

**When to use:**
- Many readers, few writers
- Read operations are expensive
- Better performance than `Mutex` in read-heavy scenarios

**Comparison:**
```java
// Java - ReentrantReadWriteLock
ReadWriteLock rwLock = new ReentrantReadWriteLock();
rwLock.readLock().lock();
rwLock.writeLock().lock();
```

```csharp
// C# - ReaderWriterLockSlim
ReaderWriterLockSlim rwLock = new ReaderWriterLockSlim();
rwLock.EnterReadLock();
rwLock.EnterWriteLock();
```

## 6. Async/Await - Asynchronous Programming

For I/O-bound tasks, async/await is more efficient than threads.

### Basic Async

```rust
async fn hello_world() {
    println!("Hello, world!");
}
```

**Note:** Async functions return `Future` that must be `.await`ed or run by an executor.

### Using Tokio Runtime

```rust
use tokio;

#[tokio::main]
async fn main() {
    println!("Hello");
    
    let task = tokio::spawn(async {
        println!("From async task");
    });
    
    task.await.unwrap();
    
    println!("Done");
}
```

**Requires:** `tokio = { version = "1", features = ["full"] }` in Cargo.toml

### Awaiting Futures

```rust
async fn fetch_data() -> String {
    // Simulating async I/O
    tokio::time::sleep(tokio::time::Duration::from_secs(1)).await;
    String::from("data")
}

#[tokio::main]
async fn main() {
    let data = fetch_data().await;
    println!("Got: {}", data);
}
```

**Comparison:**
```typescript
// TypeScript - very similar!
async function fetchData(): Promise<string> {
    await sleep(1000);
    return "data";
}

const data = await fetchData();
```

```csharp
// C# - also similar
async Task<string> FetchData() {
    await Task.Delay(1000);
    return "data";
}

var data = await FetchData();
```

```java
// Java - CompletableFuture (more verbose)
CompletableFuture<String> fetchData() {
    return CompletableFuture.supplyAsync(() -> "data");
}
```

### Concurrent Async Tasks

```rust
use tokio;

async fn task1() -> i32 {
    tokio::time::sleep(tokio::time::Duration::from_secs(1)).await;
    1
}

async fn task2() -> i32 {
    tokio::time::sleep(tokio::time::Duration::from_secs(1)).await;
    2
}

#[tokio::main]
async fn main() {
    // Sequential (2 seconds total)
    let a = task1().await;
    let b = task2().await;
    println!("Sequential: {} + {} = {}", a, b, a + b);
    
    // Concurrent (1 second total)
    let (a, b) = tokio::join!(task1(), task2());
    println!("Concurrent: {} + {} = {}", a, b, a + b);
}
```

**`tokio::join!`:** Wait for multiple futures concurrently.

**Comparison:**
```typescript
// TypeScript
const [a, b] = await Promise.all([task1(), task2()]);
```

```csharp
// C#
var tasks = new[] { Task1(), Task2() };
await Task.WhenAll(tasks);
```

### Select! - First to Complete

```rust
use tokio::time::{sleep, Duration};

#[tokio::main]
async fn main() {
    tokio::select! {
        _ = sleep(Duration::from_secs(1)) => {
            println!("Timeout!");
        }
        _ = sleep(Duration::from_secs(2)) => {
            println!("Long task");
        }
    }  // Prints "Timeout!" after 1 second
}
```

**Pattern:** Race multiple futures, respond to first one.

## 7. Threads vs Async

### When to Use Threads

- CPU-bound tasks (computation)
- Blocking operations
- Need OS-level parallelism
- Few concurrent tasks

```rust
use std::thread;

thread::spawn(|| {
    // Heavy computation
    expensive_calculation();
});
```

### When to Use Async

- I/O-bound tasks (network, file system)
- Many concurrent operations
- Need to scale to thousands of tasks
- Resource-efficient

```rust
#[tokio::main]
async fn main() {
    // Many concurrent HTTP requests
    let tasks: Vec<_> = (0..1000)
        .map(|i| tokio::spawn(async move {
            fetch_url(i).await
        }))
        .collect();
    
    for task in tasks {
        task.await.unwrap();
    }
}
```

**Rule of thumb:**
- CPU-bound → Threads
- I/O-bound → Async

## 8. Common Patterns

### Pattern 1: Thread Pool

```rust
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    let jobs = Arc::new(Mutex::new(vec![1, 2, 3, 4, 5]));
    let mut handles = vec![];
    
    for _ in 0..3 {
        let jobs = Arc::clone(&jobs);
        handles.push(thread::spawn(move || {
            loop {
                let job = {
                    let mut jobs = jobs.lock().unwrap();
                    jobs.pop()
                };
                
                match job {
                    Some(j) => println!("Processing job {}", j),
                    None => break,
                }
            }
        }));
    }
    
    for handle in handles {
        handle.join().unwrap();
    }
}
```

**Better:** Use `rayon` crate for parallel iterators!

### Pattern 2: Producer-Consumer

```rust
use std::sync::mpsc;
use std::thread;

fn main() {
    let (tx, rx) = mpsc::channel();
    
    // Producer
    thread::spawn(move || {
        for i in 0..10 {
            tx.send(i).unwrap();
        }
    });
    
    // Consumer
    for received in rx {
        println!("Processing: {}", received);
    }
}
```

### Pattern 3: Shared State with Arc<Mutex>

```rust
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    let data = Arc::new(Mutex::new(Vec::new()));
    let mut handles = vec![];
    
    for i in 0..10 {
        let data = Arc::clone(&data);
        handles.push(thread::spawn(move || {
            let mut data = data.lock().unwrap();
            data.push(i);
        }));
    }
    
    for handle in handles {
        handle.join().unwrap();
    }
    
    println!("Result: {:?}", *data.lock().unwrap());
}
```

## 9. Comparison Summary

| Feature | Rust | C/C++ | Java | C# | TypeScript |
|---------|------|-------|------|----|-----------| 
| Data races | Prevented at compile time | Runtime error | Runtime error | Runtime error | Single-threaded* |
| Threads | `std::thread` | `pthread`, `std::thread` | `Thread` | `Thread` | Worker threads |
| Async/await | `async/await` | Callbacks/Coroutines | CompletableFuture | `async/await` | `async/await` |
| Message passing | `mpsc::channel` | Manual | `BlockingQueue` | `Channel<T>` | Promises |
| Mutex | `Mutex<T>` | `pthread_mutex` | `synchronized` | `lock` | N/A* |
| Safety | Compile-time | Manual | Runtime | Runtime | Single-threaded* |

*JavaScript/TypeScript is single-threaded in browsers, Worker threads in Node.js

**Rust's unique advantage:** Fearless concurrency - if it compiles, it's thread-safe!

## Practice Time

Concurrency is powerful but complex. Practice with [Week 8 Exercises](../practical/week8-exercises.md)!

## Summary

- ✅ `thread::spawn` creates new threads
- ✅ `mpsc::channel` enables message passing
- ✅ `Arc<Mutex<T>>` for shared mutable state
- ✅ `Send` and `Sync` traits ensure thread safety
- ✅ `async/await` for efficient I/O-bound tasks
- ✅ Compiler prevents data races
- ✅ Choose threads for CPU-bound, async for I/O-bound

**Key insight:** Rust's type system makes concurrent programming safe and accessible!

---

[← Week 7](week7-lifetimes.md) | [Home](../README.md)
