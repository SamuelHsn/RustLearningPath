# Week 4: Error Handling

## Learning Objectives

- Understand Rust's error handling philosophy
- Master the `Result<T, E>` type
- Use the `?` operator for error propagation
- Create custom error types
- Compare to exceptions in other languages

## Rust's Error Handling Philosophy

**Two categories of errors:**

1. **Recoverable errors**: Missing file, network timeout, invalid input
   - Use `Result<T, E>`
   
2. **Unrecoverable errors**: Array out of bounds, assertion failures
   - Use `panic!`

**No exceptions!** Errors are values that must be handled.

## Unrecoverable Errors with panic!

```rust
fn main() {
    panic!("crash and burn");
}
```

**When to use:**
- Unrecoverable errors
- Examples, prototypes, tests
- When continuing is dangerous

**Comparison:**
- Java/C#: `throw new RuntimeException()`
- TypeScript: `throw new Error()`
- C: `abort()` or `exit()`

## Recoverable Errors with Result

### The Result Enum

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

Built-in, always in scope.

### Using Result

```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt");
    
    let f = match f {
        Ok(file) => file,
        Err(error) => {
            panic!("Problem opening file: {:?}", error);
        }
    };
}
```

**Comparison to other languages:**

```typescript
// TypeScript - try/catch
try {
    const file = fs.readFileSync("hello.txt");
} catch (error) {
    console.error("Problem opening file:", error);
}
```

```csharp
// C#
try {
    var file = File.Open("hello.txt");
} catch (Exception ex) {
    Console.WriteLine($"Problem: {ex.Message}");
}
```

```java
// Java
try {
    File file = new File("hello.txt");
} catch (IOException e) {
    System.out.println("Problem: " + e.getMessage());
}
```

Rust: **Errors are values**, not control flow!

### Matching Different Errors

```rust
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let f = File::open("hello.txt");
    
    let f = match f {
        Ok(file) => file,
        Err(error) => match error.kind() {
            ErrorKind::NotFound => match File::create("hello.txt") {
                Ok(fc) => fc,
                Err(e) => panic!("Problem creating file: {:?}", e),
            },
            other_error => {
                panic!("Problem opening file: {:?}", other_error);
            }
        },
    };
}
```

### Shortcuts: unwrap and expect

**unwrap:** Panic on error
```rust
let f = File::open("hello.txt").unwrap();
// Panics with generic message if Err
```

**expect:** Panic with custom message
```rust
let f = File::open("hello.txt").expect("Failed to open hello.txt");
// Panics with your message if Err
```

**Use in:**
- Examples
- Prototypes
- When you're certain it won't fail
- Tests

**Comparison:**
```typescript
// TypeScript - ! operator (non-null assertion)
const file = fs.readFileSync("hello.txt")!;
```

## Propagating Errors

### Manual Propagation

```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    let f = File::open("hello.txt");
    
    let mut f = match f {
        Ok(file) => file,
        Err(e) => return Err(e),  // Return error to caller
    };
    
    let mut s = String::new();
    
    match f.read_to_string(&mut s) {
        Ok(_) => Ok(s),
        Err(e) => Err(e),  // Return error to caller
    }
}
```

### The ? Operator

Shortcut for error propagation:

```rust
fn read_username_from_file() -> Result<String, io::Error> {
    let mut f = File::open("hello.txt")?;  // If Err, return it
    let mut s = String::new();
    f.read_to_string(&mut s)?;  // If Err, return it
    Ok(s)  // If Ok, return the value
}
```

Even shorter:
```rust
fn read_username_from_file() -> Result<String, io::Error> {
    let mut s = String::new();
    File::open("hello.txt")?.read_to_string(&mut s)?;
    Ok(s)
}
```

**The `?` operator:**
- Works on `Result` and `Option`
- If `Ok`, unwraps the value
- If `Err`, returns early with the error
- Can only be used in functions returning `Result` or `Option`

**Comparison:**
```csharp
// C# - similar to ?. operator for null checking
var length = person?.Address?.City?.Length;
```

Java and TypeScript don't have equivalent - must use try/catch.

### ? with Option

```rust
fn last_char_of_first_line(text: &str) -> Option<char> {
    text.lines().next()?.chars().last()
}
```

## Error Conversion

The `?` operator automatically converts errors:

```rust
use std::fs::File;
use std::io;

fn read_file() -> Result<String, io::Error> {
    let mut s = String::new();
    File::open("file.txt")?.read_to_string(&mut s)?;
    Ok(s)
}
```

Uses the `From` trait (Week 6) to convert error types.

## Custom Error Types

### Simple Enum

```rust
#[derive(Debug)]
enum MyError {
    IoError(std::io::Error),
    ParseError(std::num::ParseIntError),
    Custom(String),
}

fn do_something() -> Result<i32, MyError> {
    let contents = std::fs::read_to_string("file.txt")
        .map_err(MyError::IoError)?;
    
    let num: i32 = contents.trim()
        .parse()
        .map_err(MyError::ParseError)?;
    
    if num < 0 {
        return Err(MyError::Custom("Number must be positive".to_string()));
    }
    
    Ok(num)
}
```

### Using thiserror crate (Week 6+)

```rust
use thiserror::Error;

#[derive(Error, Debug)]
enum MyError {
    #[error("IO error: {0}")]
    Io(#[from] std::io::Error),
    
    #[error("Parse error: {0}")]
    Parse(#[from] std::num::ParseIntError),
    
    #[error("Custom error: {0}")]
    Custom(String),
}
```

## Best Practices

### When to use panic!

- Tests
- Examples/prototypes
- Situations where continuing is worse than crashing
- Invalid state that shouldn't be possible

### When to use Result

- Expected failure cases
- I/O operations
- Parsing user input
- Network operations
- Anything that can fail in normal operation

### unwrap vs expect

```rust
// ❌ Poor - generic message
let config = read_config().unwrap();

// ✅ Better - explains why it should succeed
let config = read_config()
    .expect("Config file must exist in current directory");
```

### Returning Results from main

```rust
use std::error::Error;
use std::fs::File;

fn main() -> Result<(), Box<dyn Error>> {
    let f = File::open("hello.txt")?;
    Ok(())
}
```

`Box<dyn Error>` means "any error type" (Week 6).

## Comparison Summary

| Feature | Rust | TypeScript | C# | Java | C |
|---------|------|------------|----|----- |---|
| Mechanism | `Result<T, E>` | Exceptions | Exceptions | Checked/Unchecked Exceptions | Return codes/errno |
| Checked at | Compile time | Runtime | Compile time (checked) | Compile time (checked) | Not checked |
| Must handle? | Yes (compiler enforces) | No | Sometimes | Sometimes | No |
| Performance | Zero cost | Overhead | Overhead | Overhead | Zero cost |

**Rust advantage:** Compiler forces error handling without runtime overhead.

## Error Handling Patterns

### Early Return Pattern

```rust
fn process_data(input: &str) -> Result<i32, String> {
    if input.is_empty() {
        return Err("Input cannot be empty".to_string());
    }
    
    let num: i32 = input.parse()
        .map_err(|_| "Invalid number".to_string())?;
    
    if num < 0 {
        return Err("Number must be positive".to_string());
    }
    
    Ok(num * 2)
}
```

### Fallback Pattern

```rust
fn get_config() -> Config {
    read_config_file()
        .unwrap_or_else(|_| default_config())
}
```

### Logging Errors

```rust
fn process() -> Result<(), Error> {
    do_something().map_err(|e| {
        eprintln!("Warning: {}", e);  // Log error
        e  // Propagate error
    })?;
    Ok(())
}
```

## Practice

Move to [Week 4 Exercises](../practical/week4-exercises.md) to practice error handling!

## Summary

- ✅ `panic!` for unrecoverable errors
- ✅ `Result<T, E>` for recoverable errors
- ✅ `?` operator for error propagation
- ✅ `unwrap`/`expect` for prototypes and cases where failure is impossible
- ✅ Custom error types with enums
- ✅ Errors are values, not exceptions
- ✅ Compiler enforces error handling

---

[← Week 3](week3-types.md) | [Home](../README.md) | [Next: Week 5 →](week5-collections.md)
