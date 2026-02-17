# Week 2: Understanding Ownership

## Introduction

**Ownership is Rust's most unique feature.** It's what makes Rust memory-safe without a garbage collector. This week requires patience - ownership is the hardest concept to learn, but once it clicks, everything else becomes easier.

## Why Ownership Matters

**Problem in different languages:**

- **C**: Manual memory management → memory leaks, use-after-free, double-free
- **Java/C#/TypeScript**: Garbage collection → runtime overhead, pause times, less control
- **Rust**: Ownership system → compile-time guarantees, no runtime overhead, memory safety

Rust gets the best of both worlds: memory safety + performance.

## The Three Rules of Ownership

1. Each value in Rust has an **owner**
2. There can only be **one owner** at a time
3. When the owner goes out of scope, the value is **dropped** (freed)

## Ownership Basics

### Stack vs Heap

**Stack:**
- Fixed size, known at compile time
- Fast allocation/deallocation
- Examples: integers, floats, bools, fixed-size arrays

**Heap:**
- Dynamic size, determined at runtime
- Slower allocation/deallocation
- Examples: `String`, `Vec<T>`, Box<T>

Similar to value types vs reference types in C#, primitives vs objects in Java, stack vs heap in C.

### Move Semantics

```rust
let s1 = String::from("hello");
let s2 = s1;  // s1 is MOVED to s2

// println!("{}", s1);  // ❌ Error! s1 is no longer valid
println!("{}", s2);  // ✅ OK
```

**What happened?**
- `String` data is on the heap
- `s1` owned the data
- Assignment **moved** ownership to `s2`
- `s1` is now invalid (compiler enforces this)

**Comparison with other languages:**

```typescript
// TypeScript - both reference same object
let s1 = "hello";
let s2 = s1;
console.log(s1);  // ✅ Still works
```

```java
// Java - both reference same object
String s1 = "hello";
String s2 = s1;
System.out.println(s1);  // ✅ Still works
```

```csharp
// C# - both reference same object
string s1 = "hello";
string s2 = s1;
Console.WriteLine(s1);  // ✅ Still works
```

```c
// C - need to manually copy
char* s1 = malloc(6);
strcpy(s1, "hello");
char* s2 = s1;  // Just copied pointer
free(s1);
// s2 is now dangling! Undefined behavior
```

Rust prevents the problems from C while being more explicit than Java/C#/TypeScript.

### Copy vs Move

**Types that implement `Copy` trait** are copied, not moved:

```rust
let x = 5;
let y = x;  // x is COPIED to y

println!("{}", x);  // ✅ OK - x is still valid
println!("{}", y);  // ✅ OK
```

**Types that are `Copy`:**
- All integer types: `i32`, `u64`, etc.
- Boolean: `bool`
- Floating point: `f32`, `f64`
- Character: `char`
- Tuples of `Copy` types: `(i32, i32)`

**Types that are NOT `Copy`:**
- `String`
- `Vec<T>`
- Any type that owns heap data

**Rule of thumb:** If a type needs to allocate memory or owns resources, it's not `Copy`.

### Clone

To make an explicit deep copy:

```rust
let s1 = String::from("hello");
let s2 = s1.clone();  // Explicit deep copy

println!("{}", s1);  // ✅ OK
println!("{}", s2);  // ✅ OK
```

Similar to: `.clone()` in Java, `Clone()` in C#, spread operator in TypeScript

**Important:** `clone()` can be expensive - it copies heap data.

## Functions and Ownership

### Passing to Functions

```rust
fn main() {
    let s = String::from("hello");
    
    takes_ownership(s);  // s is moved into function
    
    // println!("{}", s);  // ❌ Error! s is no longer valid
    
    let x = 5;
    makes_copy(x);  // x is copied
    
    println!("{}", x);  // ✅ OK - x is still valid
}

fn takes_ownership(some_string: String) {
    println!("{}", some_string);
}  // some_string is dropped here

fn makes_copy(some_integer: i32) {
    println!("{}", some_integer);
}
```

**Key point:** Passing a value to a function **moves** or **copies** it, just like assignment.

### Returning Values

```rust
fn main() {
    let s1 = gives_ownership();  // Function returns and moves value to s1
    
    let s2 = String::from("hello");
    let s3 = takes_and_gives_back(s2);  // s2 is moved in, return value moved to s3
    
    // s2 no longer valid, s1 and s3 are
}

fn gives_ownership() -> String {
    let some_string = String::from("yours");
    some_string  // Returned and moves out
}

fn takes_and_gives_back(a_string: String) -> String {
    a_string  // Returned and moves out
}
```

**Pattern:** You can return ownership to the caller.

## References and Borrowing

Having to return ownership every time is tedious. **References** solve this!

### Immutable References (Borrowing)

```rust
fn main() {
    let s1 = String::from("hello");
    
    let len = calculate_length(&s1);  // Borrow s1
    
    println!("Length of '{}' is {}", s1, len);  // ✅ s1 still valid!
}

fn calculate_length(s: &String) -> usize {
    s.len()
}  // s goes out of scope, but doesn't drop the String (doesn't own it)
```

The `&` creates a **reference** - it lets you refer to a value without taking ownership.

**Comparison:**
- C: Pointer (`char*`) - can be null, can be dangling
- Java/C#: Reference - garbage collected
- Rust: Reference - guaranteed valid, no null

### References Cannot Modify

```rust
fn main() {
    let s = String::from("hello");
    change(&s);
}

fn change(some_string: &String) {
    // some_string.push_str(", world");  // ❌ Error! Cannot modify through & reference
}
```

References are immutable by default!

### Mutable References

```rust
fn main() {
    let mut s = String::from("hello");
    
    change(&mut s);  // Mutable borrow
    
    println!("{}", s);  // "hello, world"
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

**Mutable reference rules:**

1. **Only ONE mutable reference at a time:**
```rust
let mut s = String::from("hello");

let r1 = &mut s;
// let r2 = &mut s;  // ❌ Error! Cannot have two mutable references

println!("{}", r1);
```

2. **Cannot mix mutable and immutable references:**
```rust
let mut s = String::from("hello");

let r1 = &s;     // ✅ OK
let r2 = &s;     // ✅ OK - multiple immutable references are fine
// let r3 = &mut s;  // ❌ Error! Cannot have mutable ref while immutable refs exist

println!("{} {}", r1, r2);
```

**Why these rules?** They prevent data races at compile time!

A data race occurs when:
1. Two or more pointers access the same data
2. At least one writes to the data
3. No synchronization mechanism

Rust prevents this entirely at compile time.

### Reference Scope

```rust
let mut s = String::from("hello");

let r1 = &s;
let r2 = &s;
println!("{} {}", r1, r2);
// r1 and r2 are no longer used after this point

let r3 = &mut s;  // ✅ OK - no conflict with r1, r2
println!("{}", r3);
```

A reference's scope starts where it's introduced and continues through its **last usage**.

## Dangling References

Rust prevents dangling references:

```rust
fn dangle() -> &String {  // ❌ Compiler error!
    let s = String::from("hello");
    &s  // We return a reference to s
}  // s goes out of scope and is dropped
   // Reference would be dangling!
```

**Solution:** Return the value itself:
```rust
fn no_dangle() -> String {
    let s = String::from("hello");
    s  // Ownership is moved out
}
```

**Comparison:**
- C: Dangling pointers are a major source of bugs
- Java/C#: GC prevents this (but at runtime cost)
- Rust: Prevented at compile time

## The Rules of References

1. At any given time, you can have **either**:
   - ONE mutable reference
   - OR any number of immutable references
2. References must **always be valid** (no dangling references)

These rules enable "fearless concurrency" - the compiler prevents data races.

## Slices

A slice is a reference to a contiguous sequence of elements in a collection.

### String Slices

```rust
let s = String::from("hello world");

let hello = &s[0..5];   // "hello"
let world = &s[6..11];  // "world"

// Shortcuts
let hello = &s[..5];    // Same as [0..5]
let world = &s[6..];    // Same as [6..len]
let whole = &s[..];     // Entire string
```

**Type:** `&str` (string slice)

```rust
fn first_word(s: &String) -> &str {
    let bytes = s.as_bytes();
    
    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }
    
    &s[..]
}
```

**String literals are slices:**
```rust
let s = "Hello, world!";  // Type: &str
```

This is why string literals are immutable!

### Array Slices

```rust
let a = [1, 2, 3, 4, 5];

let slice = &a[1..3];  // Type: &[i32]

for element in slice {
    println!("{}", element);  // 2, 3
}
```

## Mental Model

Think of ownership like real-world ownership:

1. **Ownership:** You own a book
   - When you move away, the book goes with you
   - When you throw away your stuff, the book is discarded

2. **Borrowing (immutable):** Someone borrows your book to read
   - Multiple people can read at once
   - They can't modify it
   - You get it back when they're done

3. **Mutable borrowing:** Someone borrows your book to annotate
   - Only one person can annotate at a time
   - You can't read it while they're annotating
   - You get it back when they're done

## Comparison Summary

| Language | Memory Model | Safety | Runtime Cost |
|----------|--------------|--------|--------------|
| C | Manual | Unsafe | Zero |
| C++ | Manual (RAII) | Unsafe (unless careful) | Zero |
| Java/C# | GC | Safe | GC pauses |
| TypeScript | GC | Safe | GC pauses |
| **Rust** | **Ownership** | **Safe** | **Zero** |

Rust gives you C/C++ performance with Java/C#/TypeScript safety!

## Practice Time

The ownership system is complex. Practice is essential!

Move on to [Week 2 Exercises](../practical/week2-exercises.md) to solidify your understanding.

## Summary

- ✅ Each value has one owner
- ✅ Values are moved or copied on assignment
- ✅ Use `&` to borrow (immutable reference)
- ✅ Use `&mut` to mutably borrow
- ✅ One mutable borrow OR many immutable borrows
- ✅ References must always be valid
- ✅ Slices reference part of a collection

**Don't worry if this doesn't fully click yet** - it takes time and practice. The compiler will guide you!

---

[← Week 1](week1-fundamentals.md) | [Home](../README.md) | [Next: Week 3 →](week3-types.md)
