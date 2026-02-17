# Rust Quick Reference Cheat Sheet

A quick reference for developers coming from TypeScript, Java, C#, and C. Keep this handy while learning!

## Variables & Types

```rust
// Immutable by default
let x = 5;

// Mutable variable
let mut y = 5;
y = 6;

// Constant (compile-time, always immutable)
const MAX: i32 = 100;

// Type annotation
let x: i32 = 5;

// Shadowing (creates new variable)
let x = 5;
let x = x + 1;
```

## Common Types

```rust
// Integers: i8, i16, i32, i64, i128, isize
// Unsigned: u8, u16, u32, u64, u128, usize
let int: i32 = 42;

// Floats: f32, f64
let float: f64 = 3.14;

// Boolean
let bool_val: bool = true;

// Character (Unicode, 4 bytes)
let c: char = 'z';

// String types
let s1: &str = "string slice";       // Immutable, fixed size
let s2: String = String::from("owned string");  // Growable, heap-allocated

// Tuples
let tup: (i32, f64, char) = (500, 6.4, 'z');
let (x, y, z) = tup;  // Destructuring

// Arrays (fixed size)
let arr: [i32; 5] = [1, 2, 3, 4, 5];
```

## Functions

```rust
// Basic function
fn function_name(param: i32) -> i32 {
    param + 1  // No semicolon = return value
}

// Explicit return
fn add(a: i32, b: i32) -> i32 {
    return a + b;
}

// No return value (unit type ())
fn print_value(x: i32) {
    println!("{}", x);
}
```

## Ownership Rules

1. Each value has one owner
2. Only one owner at a time
3. When owner goes out of scope, value is dropped

```rust
// Move
let s1 = String::from("hello");
let s2 = s1;  // s1 is moved, no longer valid

// Clone (deep copy)
let s1 = String::from("hello");
let s2 = s1.clone();  // Both valid

// Copy types (stack only)
let x = 5;
let y = x;  // Both valid (Copy trait)
```

## References & Borrowing

```rust
// Immutable reference
let s = String::from("hello");
let len = calculate_length(&s);  // Borrow s

fn calculate_length(s: &String) -> usize {
    s.len()
}

// Mutable reference
let mut s = String::from("hello");
change(&mut s);

fn change(s: &mut String) {
    s.push_str(", world");
}

// Rules:
// - One mutable reference OR many immutable references
// - References must always be valid
```

## Structs

```rust
// Define struct
struct User {
    username: String,
    email: String,
    age: u32,
}

// Create instance
let user = User {
    username: String::from("user"),
    email: String::from("email@example.com"),
    age: 30,
};

// Methods
impl User {
    // Method (borrows self)
    fn display(&self) {
        println!("{}", self.username);
    }
    
    // Associated function (no self)
    fn new(username: String) -> User {
        User {
            username,
            email: String::new(),
            age: 0,
        }
    }
}

// Usage
user.display();
let new_user = User::new(String::from("alice"));
```

## Enums

```rust
// Simple enum
enum IpAddrKind {
    V4,
    V6,
}

// Enum with data
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

// Option<T> - instead of null
let some: Option<i32> = Some(5);
let none: Option<i32> = None;

// Result<T, E> - for errors
let result: Result<i32, String> = Ok(5);
let error: Result<i32, String> = Err(String::from("error"));
```

## Pattern Matching

```rust
// Match expression (exhaustive)
match value {
    1 => println!("one"),
    2 => println!("two"),
    _ => println!("other"),
}

// Match with Option
match some_option {
    Some(x) => println!("Value: {}", x),
    None => println!("No value"),
}

// Match with Result
match result {
    Ok(val) => println!("Success: {}", val),
    Err(e) => println!("Error: {}", e),
}

// if let (single pattern)
if let Some(x) = some_option {
    println!("Value: {}", x);
}
```

## Error Handling

```rust
// Panic (unrecoverable)
panic!("Something went wrong!");

// Result (recoverable)
fn divide(a: i32, b: i32) -> Result<i32, String> {
    if b == 0 {
        Err(String::from("Division by zero"))
    } else {
        Ok(a / b)
    }
}

// ? operator (propagate errors)
fn read_file() -> Result<String, std::io::Error> {
    let content = std::fs::read_to_string("file.txt")?;
    Ok(content)
}

// unwrap (panic on error)
let value = result.unwrap();

// expect (panic with message)
let value = result.expect("Failed to get value");

// unwrap_or (provide default)
let value = result.unwrap_or(0);
```

## Collections

```rust
// Vec<T> - growable array
let mut vec = Vec::new();
vec.push(1);
vec.push(2);

let vec = vec![1, 2, 3];  // Macro
let first = vec[0];

// String
let mut s = String::from("hello");
s.push_str(" world");
s.push('!');

// HashMap<K, V>
use std::collections::HashMap;

let mut map = HashMap::new();
map.insert(String::from("key"), 10);

let value = map.get("key");  // Returns Option<&V>
```

## Iterators

```rust
let vec = vec![1, 2, 3, 4, 5];

// for loop
for item in &vec {
    println!("{}", item);
}

// Iterator methods
let sum: i32 = vec.iter().sum();
let doubled: Vec<i32> = vec.iter().map(|x| x * 2).collect();
let evens: Vec<i32> = vec.iter().filter(|&x| x % 2 == 0).cloned().collect();

// Common iterator methods:
// .map() - transform
// .filter() - select
// .fold() - reduce
// .collect() - gather
// .sum(), .count(), .min(), .max()
```

## Traits

```rust
// Define trait (like interface)
trait Summary {
    fn summarize(&self) -> String;
}

// Implement trait
struct Article {
    title: String,
}

impl Summary for Article {
    fn summarize(&self) -> String {
        self.title.clone()
    }
}

// Trait bounds
fn notify<T: Summary>(item: &T) {
    println!("{}", item.summarize());
}

// Multiple bounds
fn process<T: Summary + Display>(item: &T) {
    // ...
}
```

## Common Traits

```rust
// Debug - for {:?} formatting
#[derive(Debug)]
struct Point { x: i32, y: i32 }

// Clone - for .clone()
#[derive(Clone)]
struct MyStruct { data: String }

// Copy - for stack-only types
#[derive(Copy, Clone)]
struct Point { x: i32, y: i32 }

// PartialEq, Eq - for ==
#[derive(PartialEq, Eq)]
struct Id(u32);
```

## Lifetimes

```rust
// Lifetime annotation
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}

// Struct with lifetime
struct ImportantExcerpt<'a> {
    part: &'a str,
}

// Static lifetime (lives forever)
let s: &'static str = "I have a static lifetime";
```

## Concurrency

```rust
use std::thread;
use std::sync::{Arc, Mutex};

// Spawn thread
let handle = thread::spawn(|| {
    println!("Hello from thread!");
});
handle.join().unwrap();

// Shared state
let counter = Arc::new(Mutex::new(0));
let counter_clone = Arc::clone(&counter);

let handle = thread::spawn(move || {
    let mut num = counter_clone.lock().unwrap();
    *num += 1;
});

handle.join().unwrap();
```

## Async/Await

```rust
// Async function
async fn fetch_data() -> Result<String, Error> {
    let response = reqwest::get("https://example.com").await?;
    let text = response.text().await?;
    Ok(text)
}

// Runtime (tokio)
#[tokio::main]
async fn main() {
    let data = fetch_data().await.unwrap();
    println!("{}", data);
}
```

## Macros

```rust
// println! - formatted output
println!("Hello, {}!", name);
println!("Debug: {:?}", value);

// vec! - create Vec
let v = vec![1, 2, 3];

// format! - create String
let s = format!("Hello, {}!", name);

// panic! - panic with message
panic!("Something went wrong!");

// assert! - check condition
assert!(x > 0, "x must be positive");
```

## Common Commands

```bash
# Create new project
cargo new project_name
cargo new --lib library_name

# Build & run
cargo build           # Debug build
cargo build --release # Optimized build
cargo run            # Build and run

# Testing & quality
cargo test           # Run tests
cargo clippy         # Linting
cargo fmt            # Format code

# Documentation
cargo doc --open     # Generate docs

# Dependencies
cargo add crate_name  # Add dependency (requires cargo-edit)
# Or edit Cargo.toml manually
```

## Comparison Quick Reference

| Rust | TypeScript | Java | C# | C |
|------|------------|------|----|---|
| `let x = 5` | `const x = 5` | `final int x = 5` | `const int x = 5` | `const int x = 5` |
| `let mut x = 5` | `let x = 5` | `int x = 5` | `int x = 5` | `int x = 5` |
| `Option<T>` | `T \| null` | `Optional<T>` | `T?` | `T*` (null) |
| `Result<T, E>` | try/catch | try/catch | try/catch | return codes |
| `&T` | — | — | — | `const T*` |
| `&mut T` | — | — | — | `T*` |
| `Vec<T>` | `T[]` | `ArrayList<T>` | `List<T>` | — |
| `String` | `string` | `String` | `string` | `char*` |
| Traits | Interfaces | Interfaces | Interfaces | — |

---

**Keep this handy!** Print it out or bookmark it while learning Rust.

[← Back to Main](README.md)
