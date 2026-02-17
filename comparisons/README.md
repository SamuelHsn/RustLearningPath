# Rust Language Comparisons

This guide helps you leverage your knowledge of **TypeScript**, **C#/.NET**, **Java**, and **C** to learn Rust faster.

## Quick Reference Table

| Concept | TypeScript | C# | Java | C | Rust |
|---------|-----------|----|----- |---|------|
| Variables | `let`/`const` | `var`/`const` | `var`/`final` | `type var` | `let`/`const` |
| Mutability | All mutable by default | Mutable by default | Mutable by default | Mutable by default | **Immutable by default** |
| Null Safety | `undefined`/`null` | Nullable types | `null` | `NULL` | `Option<T>` |
| Error Handling | `try/catch` | `try/catch` | `try/catch` | Return codes | `Result<T, E>` |
| Generics | `<T>` | `<T>` | `<T>` | ❌ (macros only) | `<T>` |
| Memory Management | GC | GC | GC | Manual | **Ownership system** |
| Classes | ✅ | ✅ | ✅ | ❌ (structs) | ❌ (structs + traits) |
| Inheritance | ✅ | ✅ | ✅ | ❌ | ❌ (composition) |
| Interfaces | ✅ | ✅ | ✅ | ❌ | Traits |
| Pattern Matching | ❌ (limited) | ✅ (C# 7+) | ✅ (Java 17+) | ❌ (switch) | ✅ (powerful) |

## Key Concepts Mapped

### 1. Variables and Mutability

**TypeScript:**
```typescript
const immutable = 5;        // Cannot reassign
let mutable = 5;            // Can reassign
```

**C#:**
```csharp
const int Immutable = 5;    // Compile-time constant
readonly int readOnly = 5;  // Runtime constant
int mutable = 5;            // Can reassign
```

**Java:**
```java
final int immutable = 5;    // Cannot reassign
int mutable = 5;            // Can reassign
```

**Rust:**
```rust
let immutable = 5;          // Cannot reassign (default!)
let mut mutable = 5;        // Can reassign
const CONSTANT: i32 = 5;    // Compile-time constant
```

**Key Difference**: In Rust, immutability is the default. You must explicitly use `mut` for mutable variables.

### 2. Null Safety

**TypeScript:**
```typescript
let value: string | null = null;
if (value !== null) {
    console.log(value.length);
}
```

**C#:**
```csharp
string? value = null;
if (value != null) {
    Console.WriteLine(value.Length);
}
```

**Java:**
```java
String value = null;
if (value != null) {
    System.out.println(value.length());
}
```

**Rust:**
```rust
let value: Option<String> = None;
match value {
    Some(s) => println!("{}", s.len()),
    None => println!("No value"),
}
// Or using if let
if let Some(s) = value {
    println!("{}", s.len());
}
```

**Key Difference**: Rust has no `null`. Instead, it uses `Option<T>` which forces you to handle the "no value" case.

### 3. Error Handling

**TypeScript:**
```typescript
try {
    const result = riskyOperation();
    console.log(result);
} catch (error) {
    console.error(error);
}
```

**C#:**
```csharp
try {
    var result = RiskyOperation();
    Console.WriteLine(result);
} catch (Exception ex) {
    Console.WriteLine(ex.Message);
}
```

**Java:**
```java
try {
    String result = riskyOperation();
    System.out.println(result);
} catch (Exception ex) {
    System.out.println(ex.getMessage());
}
```

**C:**
```c
int result = risky_operation();
if (result < 0) {
    fprintf(stderr, "Error: %d\n", result);
}
```

**Rust:**
```rust
match risky_operation() {
    Ok(result) => println!("{}", result),
    Err(e) => eprintln!("Error: {}", e),
}
// Or using ? operator
let result = risky_operation()?;  // Propagates error
```

**Key Difference**: Rust uses `Result<T, E>` instead of exceptions. Errors are values, not control flow.

### 4. Memory Management

**TypeScript/C#/Java:**
- Garbage Collected
- You allocate, GC deallocates
- Runtime overhead
- Potential pause times

**C:**
- Manual memory management
- You call `malloc()`, you call `free()`
- Easy to create memory leaks or use-after-free bugs
- No runtime overhead

**Rust:**
- **Ownership system** (unique to Rust!)
- Compiler enforces memory safety rules at compile time
- No GC, no manual `free()`
- Memory is freed when owner goes out of scope
- Zero runtime overhead
- Prevents memory leaks and data races at compile time

This is Rust's superpower and the hardest concept to learn.

### 5. Type System

**TypeScript (Structural Typing):**
```typescript
interface Person {
    name: string;
    age: number;
}
// Any object with these fields works
```

**C#/Java (Nominal Typing):**
```csharp
class Person {
    public string Name { get; set; }
    public int Age { get; set; }
}
// Must explicitly be a Person or inherit from it
```

**Rust (Mixed):**
```rust
struct Person {
    name: String,
    age: u32,
}
// Nominal for structs, structural for traits
```

### 6. Object-Oriented vs Composition

**TypeScript/C#/Java:**
```typescript
class Animal {
    makeSound() { }
}
class Dog extends Animal {
    makeSound() { console.log("Woof"); }
}
```

**Rust:**
```rust
trait Animal {
    fn make_sound(&self);
}

struct Dog;

impl Animal for Dog {
    fn make_sound(&self) {
        println!("Woof");
    }
}
```

**Key Difference**: Rust has no inheritance. Use composition and traits instead.

### 7. Async Programming

**TypeScript:**
```typescript
async function fetchData(): Promise<string> {
    const response = await fetch(url);
    return await response.text();
}
```

**C#:**
```csharp
async Task<string> FetchDataAsync() {
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

**Rust:**
```rust
async fn fetch_data() -> Result<String, Error> {
    let response = reqwest::get(url).await?;
    response.text().await
}
```

**Similarity**: Rust's async/await is very similar to TypeScript and C#!

## Common "Gotchas" for Developers

### Coming from TypeScript

1. **No `any` type**: Rust has strict typing, no escape hatch
2. **No `undefined` vs `null`**: Just `Option<T>`
3. **Explicit type conversions**: Can't mix `i32` and `i64`
4. **No automatic coercion**: `if condition { }` requires bool, not truthy values

### Coming from C#

1. **No reference types**: Everything is moved or borrowed
2. **No null reference exceptions**: Compiler prevents them
3. **No inheritance**: Use traits and composition
4. **Explicit error handling**: No unchecked exceptions
5. **No LINQ**: But iterators are very powerful

### Coming from Java

1. **No `null`**: Use `Option<T>`
2. **No inheritance**: Use traits
3. **Value semantics by default**: Not reference semantics
4. **No reflection**: Macros provide some metaprogramming

### Coming from C

1. **No manual `free()`**: Ownership handles it
2. **No null pointers**: Use `Option<T>`
3. **No void pointers**: Use generics
4. **Safe by default**: Unsafe operations require `unsafe` block
5. **Richer type system**: Enums, pattern matching, traits

## Mental Model Shift

The biggest mental shift for Rust is understanding **ownership**:

1. Each value has one owner
2. When owner goes out of scope, value is dropped
3. Values can be borrowed (referenced) with rules:
   - Many immutable borrows OR one mutable borrow
   - No dangling references

Think of it as:
- **C**: You manage memory manually
- **Java/C#/TypeScript**: GC manages memory for you
- **Rust**: The compiler proves your memory management is correct

## Next Steps

Now that you understand how Rust relates to your existing knowledge:

1. Start with [Week 1 Theory](../theory/week1-fundamentals.md)
2. Reference this guide when confused
3. Don't worry if ownership doesn't click immediately - it takes time!

---

[← Back: Setup](../setup/installation.md) | [Home](../README.md) | [Next: Week 1 Theory →](../theory/week1-fundamentals.md)
