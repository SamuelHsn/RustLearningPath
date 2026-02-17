# Week 1: Rust Fundamentals

## Learning Objectives

By the end of this week, you will:
- Understand Rust's basic syntax and data types
- Write simple programs with variables and functions
- Use control flow structures
- Understand the basics of Rust's type system

## 1. Variables and Mutability

### Immutable by Default

Unlike TypeScript, Java, and C#, Rust variables are **immutable by default**:

```rust
let x = 5;
// x = 6;  // ❌ Compile error!

let mut y = 5;
y = 6;  // ✅ OK
```

**Why?** Immutability helps prevent bugs and makes concurrent programming safer.

### Constants

```rust
const MAX_POINTS: u32 = 100_000;
```

Constants are:
- Always immutable (no `mut`)
- Must have type annotation
- Can be declared in any scope
- Must be compile-time constant

Similar to: `const` in TypeScript, `const` in C, `const` in C#

### Shadowing

Rust allows variable shadowing:

```rust
let x = 5;
let x = x + 1;  // Creates new variable
let x = x * 2;  // Creates another new variable
```

This is different from mutation - you're creating new variables with the same name.

## 2. Data Types

Rust is **statically typed** (like TypeScript with strict mode, C#, Java, and C).

### Scalar Types

#### Integers

| Length | Signed | Unsigned |
|--------|--------|----------|
| 8-bit | `i8` | `u8` |
| 16-bit | `i16` | `u16` |
| 32-bit | `i32` | `u32` |
| 64-bit | `i64` | `u64` |
| 128-bit | `i128` | `u128` |
| arch | `isize` | `usize` |

**Comparison:**
- TypeScript: `number` (always float64)
- C#: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`
- Java: `byte`, `short`, `int`, `long` (no unsigned in standard Java)
- C: `char`, `short`, `int`, `long`, `unsigned` variants

**Default**: `i32` is the default integer type (like `int` in C#/Java).

**Number literals:**
```rust
let decimal = 98_222;       // Underscores for readability
let hex = 0xff;
let octal = 0o77;
let binary = 0b1111_0000;
let byte = b'A';            // u8 only
```

#### Floating-Point

```rust
let x = 2.0;      // f64 (default)
let y: f32 = 3.0; // f32
```

Like `double` and `float` in C#/Java/C, but Rust uses IEEE-754 standard.

#### Boolean

```rust
let t = true;
let f: bool = false;
```

Like TypeScript, C#, Java (but NOT like C where 0/1 are used).

#### Character

```rust
let c = 'z';
let z = 'ℤ';
let heart = '❤';
```

**Important difference**: Rust's `char` is 4 bytes (Unicode scalar value), not 1 byte like C or 2 bytes like Java/C#.

### Compound Types

#### Tuples

```rust
let tup: (i32, f64, u8) = (500, 6.4, 1);

// Destructuring
let (x, y, z) = tup;

// Access by index
let five_hundred = tup.0;
let six_point_four = tup.1;
```

Similar to: TypeScript tuples, C# tuples (ValueTuple), Java records (Java 14+)

#### Arrays

```rust
let a = [1, 2, 3, 4, 5];
let first = a[0];

// Type annotation
let a: [i32; 5] = [1, 2, 3, 4, 5];

// Initialize with same value
let a = [3; 5];  // [3, 3, 3, 3, 3]
```

**Key difference**: Arrays have **fixed size** known at compile time.

Like: C arrays, but with bounds checking
Unlike: TypeScript arrays (dynamic), C# arrays (dynamic), Java arrays (dynamic)

For dynamic arrays, Rust uses `Vec<T>` (covered in Week 5).

## 3. Functions

```rust
fn main() {
    println!("Hello, world!");
    
    another_function(5);
    
    let sum = add(5, 3);
    println!("Sum: {}", sum);
}

fn another_function(x: i32) {
    println!("The value is: {}", x);
}

fn add(x: i32, y: i32) -> i32 {
    x + y  // Note: no semicolon = return value
}
```

### Key Points:

1. **Function parameters must have type annotations**
   - Unlike TypeScript (can infer), C# (can infer in some cases)
   - Like Java, C

2. **Return types specified with `-> Type`**
   - Similar to TypeScript's `: Type` after params

3. **Last expression without semicolon is the return value**
   ```rust
   fn add(x: i32, y: i32) -> i32 {
       x + y  // Implicit return
   }
   
   // Equivalent to:
   fn add(x: i32, y: i32) -> i32 {
       return x + y;  // Explicit return
   }
   ```

4. **Statements vs Expressions**
   - Statements do things, don't return values
   - Expressions evaluate to values
   ```rust
   let x = 5;  // Statement
   
   let y = {
       let x = 3;
       x + 1  // Expression (no semicolon)
   };  // y = 4
   ```

## 4. Control Flow

### If Expressions

```rust
let number = 6;

if number % 4 == 0 {
    println!("divisible by 4");
} else if number % 3 == 0 {
    println!("divisible by 3");
} else {
    println!("not divisible by 4 or 3");
}
```

**Important**: Condition must be `bool`, no truthy/falsy like TypeScript or C:

```rust
// ❌ Won't compile
if number {
    println!("number was something");
}

// ✅ Must be explicit
if number != 0 {
    println!("number was something");
}
```

### If as Expression

```rust
let condition = true;
let number = if condition { 5 } else { 6 };
```

Similar to: ternary operator in other languages
```typescript
const number = condition ? 5 : 6;  // TypeScript
```

### Loops

#### `loop` - Infinite Loop

```rust
loop {
    println!("again!");
    // break; to exit
}
```

Like `while (true)` in Java/C#/C.

#### Returning from Loops

```rust
let mut counter = 0;

let result = loop {
    counter += 1;
    
    if counter == 10 {
        break counter * 2;  // Return value from loop
    }
};  // result = 20
```

#### `while` Loop

```rust
let mut number = 3;

while number != 0 {
    println!("{}!", number);
    number -= 1;
}
```

Standard `while` loop like all other languages.

#### `for` Loop

```rust
let a = [10, 20, 30, 40, 50];

for element in a {
    println!("value: {}", element);
}

// Range
for number in 1..4 {  // 1, 2, 3 (excludes 4)
    println!("{}!", number);
}

// Inclusive range
for number in 1..=4 {  // 1, 2, 3, 4
    println!("{}!", number);
}
```

**Comparison:**
```typescript
// TypeScript
for (const element of array) { }
```

```csharp
// C#
foreach (var element in array) { }
```

```java
// Java
for (int element : array) { }
```

```c
// C
for (int i = 0; i < len; i++) { }
```

Rust's `for` is most similar to TypeScript's `for...of`, C#'s `foreach`, and Java's enhanced for loop.

## 5. Printing and Formatting

```rust
println!("Hello, world!");                    // Print line
print!("No newline");                         // No newline

let x = 5;
println!("x = {}", x);                        // Format with {}
println!("x = {x}");                          // Direct variable (Rust 1.58+)
println!("x = {}, y = {}", x, y);            // Multiple values
println!("x = {0}, y = {1}, x again = {0}", x, y);  // Positional

// Debug formatting
println!("Debug: {:?}", x);                   // Debug print
println!("Pretty debug: {:#?}", complex);     // Pretty-print
```

**Note**: `println!` is a **macro** (note the `!`), not a function. More on macros later.

## 6. Comments

```rust
// Single-line comment

/* 
   Multi-line comment
*/

/// Documentation comment (for items below)
/// Supports Markdown
fn documented_function() {}

//! Documentation comment (for containing item)
//! Often used at top of modules
```

Similar to: TypeScript, C#, Java, C (but with doc comments like C#'s XML comments or Java's JavaDoc)

## Practice Time

Now that you've learned the fundamentals, move on to [Week 1 Exercises](../practical/week1-exercises.md) to practice these concepts!

## Summary

You've learned:
- ✅ Variables are immutable by default (use `mut` for mutable)
- ✅ Rust has rich type system with integers, floats, bools, chars
- ✅ Tuples and arrays for compound data
- ✅ Functions with explicit types
- ✅ Expressions vs statements
- ✅ Control flow: if, loop, while, for
- ✅ No truthy/falsy - conditions must be `bool`

**Next:** [Week 2: Ownership & Borrowing](week2-ownership.md) - The most important concept in Rust!

---

[← Comparisons](../comparisons/README.md) | [Home](../README.md) | [Next: Week 2 →](week2-ownership.md)
