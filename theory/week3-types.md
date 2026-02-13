# Week 3: Structs, Enums & Pattern Matching

## Learning Objectives

- Define custom types with structs
- Use enums for types with multiple variants
- Master pattern matching with `match`
- Understand method syntax with `impl` blocks
- Compare to classes in TypeScript/Java/C#

## Structs - Custom Data Types

### Defining Structs

```rust
struct User {
    username: String,
    email: String,
    age: u32,
    active: bool,
}
```

**Comparison:**
- TypeScript: `interface` or `class`
- C#: `class` or `struct`
- Java: `class`
- C: `struct`

Rust structs are like C structs but with methods (through `impl`).

### Creating Instances

```rust
let user1 = User {
    email: String::from("user@example.com"),
    username: String::from("user123"),
    age: 25,
    active: true,
};

// Accessing fields
println!("Username: {}", user1.username);
```

### Mutable Structs

```rust
let mut user1 = User {
    email: String::from("user@example.com"),
    username: String::from("user123"),
    age: 25,
    active: true,
};

user1.email = String::from("newemail@example.com");  // OK
```

**Note:** Entire struct must be mutable, not individual fields.

### Field Init Shorthand

```rust
fn build_user(email: String, username: String) -> User {
    User {
        email,     // Shorthand when variable name matches field
        username,
        age: 0,
        active: true,
    }
}
```

Like JavaScript/TypeScript object shorthand!

### Struct Update Syntax

```rust
let user2 = User {
    email: String::from("another@example.com"),
    ..user1  // Fill remaining fields from user1
};
```

Similar to spread operator: `{ ...user1, email: "..." }` in TypeScript.

**Warning:** This moves non-Copy fields!

### Tuple Structs

```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

let black = Color(0, 0, 0);
let origin = Point(0, 0, 0);

println!("First value: {}", black.0);
```

### Unit-Like Structs

```rust
struct AlwaysEqual;

let subject = AlwaysEqual;
```

Useful for traits (Week 6).

## Methods and Associated Functions

### Method Syntax

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    // Method (takes &self)
    fn area(&self) -> u32 {
        self.width * self.height
    }
    
    // Method with mutable self
    fn scale(&mut self, factor: u32) {
        self.width *= factor;
        self.height *= factor;
    }
    
    // Associated function (no self) - like static method
    fn square(size: u32) -> Rectangle {
        Rectangle {
            width: size,
            height: size,
        }
    }
}

fn main() {
    let mut rect = Rectangle { width: 30, height: 50 };
    println!("Area: {}", rect.area());
    
    rect.scale(2);
    println!("New area: {}", rect.area());
    
    let sq = Rectangle::square(20);
}
```

**Comparison:**
```typescript
// TypeScript
class Rectangle {
    constructor(public width: number, public height: number) {}
    
    area(): number {
        return this.width * this.height;
    }
    
    static square(size: number): Rectangle {
        return new Rectangle(size, size);
    }
}
```

**Key differences:**
- No inheritance in Rust
- Methods defined in separate `impl` blocks
- `&self`, `&mut self`, or `self` for ownership control

## Enums - Types with Variants

### Basic Enums

```rust
enum IpAddrKind {
    V4,
    V6,
}

let four = IpAddrKind::V4;
let six = IpAddrKind::V6;
```

### Enums with Data

```rust
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);
let loopback = IpAddr::V6(String::from("::1"));
```

**Much more powerful than enums in C/Java!**

Comparison to TypeScript:
```typescript
// TypeScript - discriminated unions
type IpAddr = 
    | { kind: 'V4', octets: [number, number, number, number] }
    | { kind: 'V6', address: string };
```

### Complex Enum Variants

```rust
enum Message {
    Quit,                       // No data
    Move { x: i32, y: i32 },   // Named fields (like struct)
    Write(String),              // Single value
    ChangeColor(i32, i32, i32), // Tuple
}
```

### Methods on Enums

```rust
impl Message {
    fn call(&self) {
        // Method body
    }
}

let m = Message::Write(String::from("hello"));
m.call();
```

## The Option Enum

Rust has no `null`. Instead, it has `Option<T>`:

```rust
enum Option<T> {
    None,
    Some(T),
}
```

This is built-in and always in scope.

### Using Option

```rust
let some_number = Some(5);
let some_string = Some("a string");
let absent_number: Option<i32> = None;

// Must handle None case
match some_number {
    Some(value) => println!("Value: {}", value),
    None => println!("No value"),
}
```

**Why better than null?**
- Compiler forces you to handle the None case
- No null pointer exceptions possible
- Type system tracks possibility of absence

**Comparison:**
```typescript
// TypeScript
let value: number | null = null;
// Easy to forget to check

// Rust
let value: Option<i32> = None;
// Compiler forces you to check
```

## Pattern Matching with match

### Basic Match

```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

### Patterns that Bind Values

```rust
enum UsState {
    Alabama,
    Alaska,
    // ...
}

enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("State quarter from {:?}!", state);
            25
        }
    }
}
```

### Matching Option<T>

```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}

let five = Some(5);
let six = plus_one(five);
let none = plus_one(None);
```

### Match Must Be Exhaustive

```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        Some(i) => Some(i + 1),
        // ❌ Error: pattern `None` not covered
    }
}
```

### The _ Placeholder

```rust
let some_value = 0u8;

match some_value {
    1 => println!("one"),
    3 => println!("three"),
    5 => println!("five"),
    7 => println!("seven"),
    _ => (),  // Catch all other values
}
```

## if let - Concise Control Flow

When you care about one pattern:

```rust
let some_value = Some(3);

// Verbose
match some_value {
    Some(3) => println!("three"),
    _ => (),
}

// Concise
if let Some(3) = some_value {
    println!("three");
}
```

### if let with else

```rust
let mut count = 0;
let coin = Coin::Quarter(UsState::Alaska);

if let Coin::Quarter(state) = coin {
    println!("State quarter from {:?}!", state);
} else {
    count += 1;
}
```

## Comparison to Other Languages

### vs TypeScript/JavaScript

```typescript
// TypeScript - discriminated unions
type Shape = 
    | { kind: 'circle', radius: number }
    | { kind: 'rectangle', width: number, height: number };

function area(shape: Shape): number {
    switch (shape.kind) {
        case 'circle':
            return Math.PI * shape.radius ** 2;
        case 'rectangle':
            return shape.width * shape.height;
    }
}
```

Rust enums are similar but more powerful and checked at compile time.

### vs Java

Java enums are more limited:
```java
enum Day { MONDAY, TUESDAY, WEDNESDAY }
```

Rust enums can hold data, Java's cannot (directly).

### vs C# 

C# has structs (value types) and classes (reference types):
```csharp
struct Point { public int X; public int Y; }  // Stack allocated
class Person { public string Name; }          // Heap allocated
```

Rust structs can be stack or heap allocated based on usage.

## Practice

Move to [Week 3 Exercises](../practical/week3-exercises.md) to practice these concepts!

## Summary

- ✅ Structs group related data
- ✅ Methods defined in `impl` blocks
- ✅ Enums represent types with variants
- ✅ Enums can hold data
- ✅ `Option<T>` replaces null
- ✅ `match` provides exhaustive pattern matching
- ✅ `if let` for concise single-pattern matching

---

[← Week 2](week2-ownership.md) | [Home](../README.md) | [Next: Week 4 →](week4-errors.md)
