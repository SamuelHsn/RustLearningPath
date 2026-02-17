# Week 8: Concurrency & Async Exercises

These exercises focus on multi-threading, concurrency patterns, and async/await.

## Exercise 1: Basic Threading

Create and manage multiple threads.

**Requirements:**

```rust
use std::thread;
use std::time::Duration;

// Spawn threads that count to N
fn spawn_counters(n_threads: usize, count_to: u32) {
    // Spawn n_threads threads
    // Each thread counts from 0 to count_to
    // Join all threads before returning
}

// Parallel computation
fn parallel_sum(numbers: Vec<i32>, n_threads: usize) -> i32 {
    // Split numbers into chunks
    // Process each chunk in separate thread
    // Combine results
}

// Thread with return value
fn compute_in_thread(x: i32) -> thread::JoinHandle<i32> {
    // Spawn thread that computes x * x + x
    // Return join handle
}
```

**Example output:**
```
Thread 0: counting to 5
Thread 1: counting to 5
Thread 0: 1
Thread 1: 1
...
```

**Hints:**
- `thread::spawn(|| { ... })` creates thread
- Use `move` to transfer ownership
- `.join()` waits for thread completion
- Store join handles in a vector
- Split vec with `.chunks()`

## Exercise 2: Shared State with Mutex

Use Mutex for thread-safe shared state.

**Requirements:**

```rust
use std::sync::{Arc, Mutex};
use std::thread;

// Shared counter incremented by multiple threads
fn concurrent_counter(n_threads: usize, increments: usize) -> i32 {
    // Create shared counter protected by Mutex
    // Spawn threads that each increment counter 'increments' times
    // Return final count
}

// Thread-safe queue
struct SafeQueue<T> {
    items: Arc<Mutex<Vec<T>>>,
}

impl<T> SafeQueue<T> {
    fn new() -> Self {
        // Create empty queue
    }
    
    fn push(&self, item: T) {
        // Add item to queue (thread-safe)
    }
    
    fn pop(&self) -> Option<T> {
        // Remove and return item (thread-safe)
    }
    
    fn len(&self) -> usize {
        // Return queue length
    }
}

// Test with multiple producer threads
fn test_queue() {
    let queue = SafeQueue::new();
    // Spawn threads that push items
    // Main thread pops items
}
```

**Hints:**
- `Arc` for shared ownership across threads
- `Mutex` for interior mutability
- `.lock()` acquires mutex, returns guard
- Clone `Arc` to share across threads
- MutexGuard automatically releases on drop

## Exercise 3: Message Passing with Channels

Use channels for thread communication.

**Requirements:**

```rust
use std::sync::mpsc;
use std::thread;
use std::time::Duration;

// Producer-consumer pattern
fn producer_consumer(n_producers: usize, items_per_producer: usize) {
    // Create channel
    // Spawn producer threads that send items
    // Main thread receives and processes all items
}

// Parallel map using channels
fn parallel_map<F>(data: Vec<i32>, f: F) -> Vec<i32>
where
    F: Fn(i32) -> i32 + Send + 'static + Copy,
{
    // Send items to worker threads via channel
    // Workers apply function and send results back
    // Collect results in order
}

// Fan-out/fan-in pattern
fn fan_out_fan_in(numbers: Vec<i32>) -> i32 {
    // Send each number to separate thread for processing
    // Each thread computes square
    // Sum all results
}
```

**Example:**
```rust
let data = vec![1, 2, 3, 4, 5];
let results = parallel_map(data, |x| x * 2);
// results: [2, 4, 6, 8, 10]
```

**Hints:**
- `mpsc::channel()` creates (sender, receiver)
- Clone sender for multiple producers
- Receiver loop: `for item in rx { ... }`
- Channel closed when all senders dropped
- Use second channel for results

## Exercise 4: Thread Pool

Implement a basic thread pool.

**Requirements:**

```rust
use std::sync::{Arc, Mutex, mpsc};
use std::thread;

type Job = Box<dyn FnOnce() + Send + 'static>;

struct ThreadPool {
    workers: Vec<Worker>,
    sender: mpsc::Sender<Job>,
}

struct Worker {
    id: usize,
    thread: thread::JoinHandle<()>,
}

impl ThreadPool {
    fn new(size: usize) -> Self {
        // Create pool with 'size' worker threads
        // Workers wait for jobs on channel
    }
    
    fn execute<F>(&self, f: F)
    where
        F: FnOnce() + Send + 'static,
    {
        // Send job to worker thread
    }
}

impl Drop for ThreadPool {
    fn drop(&mut self) {
        // Clean shutdown: join all threads
    }
}

// Test the pool
fn test_thread_pool() {
    let pool = ThreadPool::new(4);
    
    for i in 0..10 {
        pool.execute(move || {
            println!("Job {} running", i);
            thread::sleep(Duration::from_millis(100));
        });
    }
}
```

**Hints:**
- Workers share receiver via Arc<Mutex<>>
- Each worker loops receiving jobs
- Box jobs as trait objects
- Signal shutdown via special message or separate channel

## Exercise 5: Read-Write Lock

Use RwLock for concurrent reads.

**Requirements:**

```rust
use std::sync::{Arc, RwLock};
use std::thread;

// Shared configuration that's read often, written rarely
struct Config {
    data: Arc<RwLock<Vec<String>>>,
}

impl Config {
    fn new() -> Self {
        // Create with empty vec
    }
    
    fn get(&self, index: usize) -> Option<String> {
        // Read value (use read lock)
    }
    
    fn set(&self, index: usize, value: String) {
        // Write value (use write lock)
    }
    
    fn append(&self, value: String) {
        // Add value (use write lock)
    }
    
    fn len(&self) -> usize {
        // Get length (use read lock)
    }
}

// Test with many readers, few writers
fn test_rwlock() {
    let config = Arc::new(Config::new());
    
    // Spawn reader threads
    // Spawn fewer writer threads
    // Readers should not block each other
}
```

**Hints:**
- `RwLock` allows multiple readers OR one writer
- `.read()` for read access
- `.write()` for write access
- Guards released on drop
- Prefer RwLock when reads >> writes

## Exercise 6: Atomic Operations

Use atomic types for lock-free operations.

**Requirements:**

```rust
use std::sync::atomic::{AtomicUsize, AtomicBool, Ordering};
use std::sync::Arc;
use std::thread;

// Lock-free counter
struct AtomicCounter {
    count: AtomicUsize,
}

impl AtomicCounter {
    fn new() -> Self {
        Self { count: AtomicUsize::new(0) }
    }
    
    fn increment(&self) {
        // Atomically increment
    }
    
    fn get(&self) -> usize {
        // Atomically read
    }
    
    fn compare_and_swap(&self, expected: usize, new: usize) -> bool {
        // CAS operation
    }
}

// Spinlock using atomics
struct SpinLock {
    locked: AtomicBool,
}

impl SpinLock {
    fn new() -> Self {
        Self { locked: AtomicBool::new(false) }
    }
    
    fn lock(&self) {
        // Spin until we acquire lock
        while self.locked.swap(true, Ordering::Acquire) {
            std::hint::spin_loop();
        }
    }
    
    fn unlock(&self) {
        // Release lock
        self.locked.store(false, Ordering::Release);
    }
}

// Test atomics vs mutex performance
fn benchmark_atomics_vs_mutex();
```

**Hints:**
- `Ordering::SeqCst` is safest (sequential consistency)
- `Ordering::Acquire` for loads
- `Ordering::Release` for stores
- `.fetch_add()` for increment
- `.compare_exchange()` for CAS

## Exercise 7: Async/Await Basics

Introduction to async programming.

**Requirements:**

```rust
use tokio; // Add tokio = { version = "1", features = ["full"] }

// Basic async function
async fn fetch_data(id: u32) -> String {
    // Simulate async work
    tokio::time::sleep(tokio::time::Duration::from_millis(100)).await;
    format!("Data for id: {}", id)
}

// Run multiple async tasks concurrently
#[tokio::main]
async fn main() {
    // Fetch multiple items concurrently
    let handles = (0..5).map(|i| {
        tokio::spawn(fetch_data(i))
    });
    
    // Wait for all to complete
}

// Async function with error handling
async fn fetch_with_retry(id: u32, max_retries: u32) -> Result<String, String> {
    // Try to fetch, retry on failure
}

// Timeout wrapper
async fn with_timeout<F, T>(future: F, timeout: Duration) -> Result<T, String>
where
    F: Future<Output = T>,
{
    // Use tokio::time::timeout
}
```

**Hints:**
- `async fn` returns Future
- `.await` executes future
- `tokio::spawn` for concurrent tasks
- Join handles with `futures::future::join_all`
- Add tokio dependency to Cargo.toml

## Exercise 8: Concurrent Web Scraper

Build async web scraper simulation.

**Requirements:**

```rust
use tokio;
use std::collections::HashMap;

// Simulated async HTTP client
async fn fetch_url(url: &str) -> Result<String, String> {
    // Simulate network delay
    tokio::time::sleep(tokio::time::Duration::from_millis(100)).await;
    Ok(format!("Content from {}", url))
}

// Scrape multiple URLs concurrently
async fn scrape_urls(urls: Vec<String>) -> HashMap<String, Result<String, String>> {
    // Fetch all URLs concurrently
    // Return map of url -> result
}

// Rate-limited scraper
struct RateLimitedScraper {
    max_concurrent: usize,
}

impl RateLimitedScraper {
    fn new(max_concurrent: usize) -> Self {
        Self { max_concurrent }
    }
    
    async fn scrape(&self, urls: Vec<String>) -> Vec<Result<String, String>> {
        // Limit concurrent requests
        // Use semaphore or buffered stream
    }
}

// Parse and extract links (simulate)
async fn extract_links(html: &str) -> Vec<String> {
    // Extract URLs from HTML
    // Return list of found URLs
}

// Recursive crawler (limited depth)
async fn crawl(start_url: String, max_depth: usize) -> HashMap<String, Vec<String>> {
    // Crawl starting from start_url
    // Return map of url -> links found on that page
    // Don't revisit URLs
}
```

**Hints:**
- Use `tokio::spawn` for concurrent tasks
- `futures::future::join_all` to wait for all
- `tokio::sync::Semaphore` for rate limiting
- Use HashSet to track visited URLs
- Recursive async requires Box<Pin<>>

## Exercise 9: Async Channels and Streams

Work with async message passing.

**Requirements:**

```rust
use tokio::sync::mpsc;
use tokio_stream::StreamExt;

// Async producer-consumer
async fn async_producer_consumer() {
    // Create async channel
    // Spawn producer tasks
    // Consume messages asynchronously
}

// Transform stream of data
async fn process_stream(input: Vec<i32>) -> Vec<i32> {
    // Convert to stream
    // Apply async transformations
    // Collect results
}

// Merge multiple streams
async fn merge_streams(
    stream1: impl Stream<Item = i32>,
    stream2: impl Stream<Item = i32>,
) -> Vec<i32> {
    // Merge streams and collect
}

// Buffered processing
async fn buffered_process<F, T, U>(
    items: Vec<T>,
    buffer_size: usize,
    process: F,
) -> Vec<U>
where
    F: Fn(T) -> std::pin::Pin<Box<dyn Future<Output = U> + Send>> + Send + Sync,
    T: Send,
    U: Send,
{
    // Process items with max buffer_size concurrent operations
}
```

**Hints:**
- `tokio::sync::mpsc` for async channels
- `tokio_stream` for stream utilities
- `.buffer_unordered()` for concurrent processing
- `StreamExt` trait for combinators
- Add tokio-stream dependency

## Exercise 10: Real-World Async Application

Build a complete async application.

**Requirements:**

Build a simple async job queue system:

```rust
use tokio::sync::{mpsc, Mutex};
use std::sync::Arc;
use std::collections::VecDeque;

// Job to be executed
struct Job {
    id: u64,
    data: String,
    priority: u8,
}

// Job queue with priority
struct JobQueue {
    queue: Arc<Mutex<VecDeque<Job>>>,
    notify: Arc<tokio::sync::Notify>,
}

impl JobQueue {
    fn new() -> Self {
        // Create queue and notification
    }
    
    async fn submit(&self, job: Job) {
        // Add job to queue (sorted by priority)
        // Notify workers
    }
    
    async fn pop(&self) -> Option<Job> {
        // Remove highest priority job
        // Wait if queue empty
    }
}

// Worker that processes jobs
struct Worker {
    id: usize,
    queue: Arc<JobQueue>,
}

impl Worker {
    fn new(id: usize, queue: Arc<JobQueue>) -> Self {
        Self { id, queue }
    }
    
    async fn run(&self) {
        // Loop: pop job, process it
        loop {
            if let Some(job) = self.queue.pop().await {
                self.process(job).await;
            }
        }
    }
    
    async fn process(&self, job: Job) {
        // Simulate job processing
        println!("Worker {} processing job {}", self.id, job.id);
        tokio::time::sleep(tokio::time::Duration::from_millis(100)).await;
    }
}

// Coordinator that manages workers
struct Coordinator {
    queue: Arc<JobQueue>,
    workers: Vec<tokio::task::JoinHandle<()>>,
}

impl Coordinator {
    fn new(n_workers: usize) -> Self {
        // Create queue and spawn workers
    }
    
    async fn submit_job(&self, job: Job) {
        // Submit to queue
    }
    
    async fn shutdown(self) {
        // Graceful shutdown
    }
}

#[tokio::main]
async fn main() {
    // Create coordinator with 4 workers
    // Submit various jobs
    // Wait for completion
    // Shutdown
}
```

**Hints:**
- Use `Notify` to wake waiting workers
- Priority queue: keep sorted or use BinaryHeap
- `.await` on Notify when queue empty
- Store JoinHandles for shutdown
- Use cancellation token for graceful shutdown

## Testing Your Solutions

For each exercise:

1. Add dependencies to Cargo.toml:
   ```toml
   [dependencies]
   tokio = { version = "1", features = ["full"] }
   tokio-stream = "0.1"
   futures = "0.3"
   ```

2. Test thread safety with many threads
3. Check for race conditions
4. Measure performance differences
5. Run with `cargo run --release` for async code

## Key Concepts Reference

**Threading:**
- `thread::spawn(|| { ... })` - Create thread
- `.join()` - Wait for completion
- `move` - Transfer ownership

**Synchronization:**
- `Mutex<T>` - Mutual exclusion
- `RwLock<T>` - Reader-writer lock
- `Arc<T>` - Atomic reference counting

**Channels:**
- `mpsc::channel()` - Multiple producer, single consumer
- `mpsc::Sender` / `mpsc::Receiver`
- Closed when all senders dropped

**Atomic:**
- `AtomicUsize`, `AtomicBool`, etc.
- Lock-free operations
- Memory ordering

**Async:**
- `async fn` / `.await`
- `tokio::spawn()` - Async task
- `tokio::join!()` - Wait for multiple
- `tokio::select!()` - Wait for first

## What You're Practicing

These exercises help you master:
- ✅ Creating and managing threads
- ✅ Shared state with Mutex and Arc
- ✅ Message passing with channels
- ✅ Thread pools and worker patterns
- ✅ Read-write locks
- ✅ Atomic operations
- ✅ Async/await syntax
- ✅ Concurrent async tasks
- ✅ Async channels and streams
- ✅ Building real async applications

**Congratulations!** You've completed Week 8 and the core Rust learning path! Continue with advanced topics or real-world projects.

---

[← Theory](../theory/week8-concurrency.md) | [Home](../README.md) | [Next: Advanced Projects →](../projects/README.md)
