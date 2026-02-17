# Week 6: Traits - Rust's Superpower

## Learning Objectives

By the end of this week, you will:
- Define and implement traits
- Use traits as function parameters and return types
- Understand trait bounds and generics
- Compare traits to interfaces in TypeScript, Java, and C#
- Use common standard library traits

## What Are Traits?

**Traits** define shared behavior. They're similar to interfaces in TypeScript/Java/C#, but more powerful.

**Key insight:** Traits let you define functionality a type must provide, without caring about the type's implementation details.

## 1. Defining Traits

```rust
pub trait Summary {
    fn summarize(&self) -> String;
}
```

This defines a trait called `Summary` with one method: `summarize`.

**Comparison:**
```typescript
// TypeScript
interface Summary {
    summarize(): string;
}
```

```java
// Java
public interface Summary {
    String summarize();
}
```

```csharp
// C#
public interface ISummary {
    string Summarize();
}
```

**Key difference:** Traits are more flexible - they can have default implementations and be implemented for types you don't own.

## 2. Implementing Traits

```rust
pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
}

impl Summary for NewsArticle {
    fn summarize(&self) -> String {
        format!("{}, by {} ({})", self.headline, self.author, self.location)
    }
}

pub struct Tweet {
    pub username: String,
    pub content: String,
    pub reply: bool,
    pub retweet: bool,
}

impl Summary for Tweet {
    fn summarize(&self) -> String {
        format!("{}: {}", self.username, self.content)
    }
}
```

Now we can call `summarize()` on both types:

```rust
let tweet = Tweet {
    username: String::from("horse_ebooks"),
    content: String::from("of course, as you probably already know, people"),
    reply: false,
    retweet: false,
};

println!("1 new tweet: {}", tweet.summarize());
```

## 3. Default Implementations

Traits can provide default implementations:

```rust
pub trait Summary {
    fn summarize(&self) -> String {
        String::from("(Read more...)")
    }
}

// Use default implementation
impl Summary for NewsArticle {}

// Or override it
impl Summary for Tweet {
    fn summarize(&self) -> String {
        format!("{}: {}", self.username, self.content)
    }
}
```

**Pattern:** Provide sensible defaults, allow overriding when needed.

### Default Implementations Can Call Other Methods

```rust
pub trait Summary {
    fn summarize_author(&self) -> String;
    
    fn summarize(&self) -> String {
        format!("(Read more from {}...)", self.summarize_author())
    }
}

impl Summary for Tweet {
    fn summarize_author(&self) -> String {
        format!("@{}", self.username)
    }
    // Uses default summarize() which calls our summarize_author()
}
```

**This is powerful!** Default methods can build on required methods.

## 4. Traits as Parameters

### Using Trait Bounds

```rust
pub fn notify(item: &impl Summary) {
    println!("Breaking news! {}", item.summarize());
}
```

`impl Summary` means "any type that implements Summary".

**This works with any type that implements Summary:**

```rust
notify(&tweet);
notify(&article);
```

**Comparison:**
```typescript
// TypeScript
function notify(item: Summary) {
    console.log(`Breaking news! ${item.summarize()}`);
}
```

### Trait Bound Syntax

The `impl Trait` syntax is sugar for a longer form:

```rust
pub fn notify<T: Summary>(item: &T) {
    println!("Breaking news! {}", item.summarize());
}
```

This is called a **trait bound**. It's more verbose but more flexible.

**When to use which:**

```rust
// Simple case - impl Trait is cleaner
fn notify(item: &impl Summary) { }

// Multiple parameters of same type - trait bound is better
fn notify<T: Summary>(item1: &T, item2: &T) { }

// vs (these can be different types!)
fn notify(item1: &impl Summary, item2: &impl Summary) { }
```

### Multiple Trait Bounds

```rust
fn notify(item: &(impl Summary + Display)) {
    // item must implement both Summary and Display
}

// Or with trait bound syntax
fn notify<T: Summary + Display>(item: &T) {
    // Same thing
}
```

**Comparison:**
```typescript
// TypeScript
function notify(item: Summary & Display) { }
```

```java
// Java - interfaces can extend multiple interfaces
interface SummaryDisplay extends Summary, Display { }
```

### Where Clauses

For complex trait bounds, use `where` clause:

```rust
// Hard to read
fn some_function<T: Display + Clone, U: Clone + Debug>(t: &T, u: &U) -> i32 {
    // ...
}

// Much clearer!
fn some_function<T, U>(t: &T, u: &U) -> i32
where
    T: Display + Clone,
    U: Clone + Debug,
{
    // ...
}
```

**Pattern:** Use `where` clauses for readability when you have multiple bounds.

## 5. Returning Traits

```rust
fn returns_summarizable() -> impl Summary {
    Tweet {
        username: String::from("horse_ebooks"),
        content: String::from("of course, as you probably already know, people"),
        reply: false,
        retweet: false,
    }
}
```

**Important limitation:** You can only return ONE concrete type:

```rust
// ❌ This won't compile!
fn returns_summarizable(switch: bool) -> impl Summary {
    if switch {
        NewsArticle { /* ... */ }
    } else {
        Tweet { /* ... */ }  // Error! Can't return different types
    }
}
```

**Solution:** Use trait objects (covered later) or enums.

## 6. Generics with Trait Bounds

Traits shine when combined with generics:

```rust
fn largest<T: PartialOrd>(list: &[T]) -> &T {
    let mut largest = &list[0];
    
    for item in list {
        if item > largest {
            largest = item;
        }
    }
    
    largest
}

let numbers = vec![34, 50, 25, 100, 65];
let result = largest(&numbers);
println!("Largest: {}", result);

let chars = vec!['y', 'm', 'a', 'q'];
let result = largest(&chars);
println!("Largest: {}", result);
```

**How it works:**
- `T: PartialOrd` means T must implement PartialOrd trait (comparison)
- Both `i32` and `char` implement PartialOrd
- Same function works for both!

**Comparison:**
```typescript
// TypeScript
function largest<T>(list: T[]): T {
    // TypeScript can't enforce comparison capability
}
```

```java
// Java
public static <T extends Comparable<T>> T largest(List<T> list) {
    // Similar concept with Comparable interface
}
```

```csharp
// C#
public static T Largest<T>(List<T> list) where T : IComparable<T> {
    // Similar concept with IComparable interface
}
```

## 7. Common Standard Library Traits

### Debug

For printing with `{:?}`:

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

let rect = Rectangle { width: 30, height: 50 };
println!("{:?}", rect);  // Rectangle { width: 30, height: 50 }
```

**Pattern:** Almost always derive Debug for your types!

### Clone

For explicit deep copying:

```rust
#[derive(Clone)]
struct MyStruct {
    data: String,
}

let s1 = MyStruct { data: String::from("hello") };
let s2 = s1.clone();  // Explicit deep copy
```

### Copy

For implicit copying (like integers):

```rust
#[derive(Copy, Clone)]  // Copy requires Clone
struct Point {
    x: i32,
    y: i32,
}

let p1 = Point { x: 0, y: 0 };
let p2 = p1;  // Copied, not moved!
println!("{}", p1.x);  // ✅ p1 still valid
```

**Important:** Can only derive Copy if all fields implement Copy. No heap data allowed!

### PartialEq and Eq

For equality comparison:

```rust
#[derive(PartialEq, Eq)]
struct Person {
    name: String,
    age: u32,
}

let p1 = Person { name: String::from("Alice"), age: 30 };
let p2 = Person { name: String::from("Alice"), age: 30 };
println!("{}", p1 == p2);  // true
```

**PartialEq vs Eq:**
- `PartialEq`: Allows for values that can't be equal to themselves (like `NaN`)
- `Eq`: Reflexive equality (x == x always true)

### PartialOrd and Ord

For ordering comparison:

```rust
#[derive(PartialEq, Eq, PartialOrd, Ord)]
struct Person {
    age: u32,
    name: String,
}

let p1 = Person { age: 30, name: String::from("Alice") };
let p2 = Person { age: 25, name: String::from("Bob") };
println!("{}", p1 > p2);  // true (30 > 25)
```

### Display

For user-facing output with `{}`:

```rust
use std::fmt;

struct Point {
    x: i32,
    y: i32,
}

impl fmt::Display for Point {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "({}, {})", self.x, self.y)
    }
}

let p = Point { x: 1, y: 2 };
println!("{}", p);  // (1, 2)
```

**Note:** Display must be implemented manually (can't derive).

### Default

For default values:

```rust
#[derive(Default)]
struct Config {
    host: String,  // Default: ""
    port: u32,     // Default: 0
}

let config = Config::default();
```

### Iterator

For custom iteration:

```rust
struct Counter {
    count: u32,
}

impl Iterator for Counter {
    type Item = u32;
    
    fn next(&mut self) -> Option<Self::Item> {
        if self.count < 5 {
            self.count += 1;
            Some(self.count)
        } else {
            None
        }
    }
}

let mut counter = Counter { count: 0 };
for n in counter {
    println!("{}", n);  // 1, 2, 3, 4, 5
}
```

## 8. Trait Objects - Dynamic Dispatch

Sometimes you need to work with multiple types through a trait at runtime:

```rust
pub trait Draw {
    fn draw(&self);
}

struct Button {
    width: u32,
    height: u32,
}

impl Draw for Button {
    fn draw(&self) {
        // Draw button
    }
}

struct TextField {
    text: String,
}

impl Draw for TextField {
    fn draw(&self) {
        // Draw text field
    }
}

// Vec of trait objects
let components: Vec<Box<dyn Draw>> = vec![
    Box::new(Button { width: 50, height: 10 }),
    Box::new(TextField { text: String::from("Hello") }),
];

for component in components {
    component.draw();
}
```

**Key points:**
- `dyn Draw` is a trait object (dynamic dispatch)
- Must be behind a pointer (`Box`, `&`, `Rc`, etc.)
- Runtime cost (virtual function call)
- Allows heterogeneous collections

**Comparison:**
```typescript
// TypeScript
const components: Draw[] = [
    new Button(50, 10),
    new TextField("Hello")
];
```

```java
// Java
List<Draw> components = new ArrayList<>();
components.add(new Button(50, 10));
```

### Static vs Dynamic Dispatch

```rust
// Static dispatch (monomorphization) - faster, larger binary
fn draw_static<T: Draw>(item: &T) {
    item.draw();
}

// Dynamic dispatch (trait object) - slower, smaller binary
fn draw_dynamic(item: &dyn Draw) {
    item.draw();
}
```

**Rule of thumb:** Use static dispatch (generics) unless you need trait objects.

## 9. Associated Types

Traits can have associated types:

```rust
pub trait Iterator {
    type Item;
    
    fn next(&mut self) -> Option<Self::Item>;
}
```

**Using associated types:**

```rust
impl Iterator for Counter {
    type Item = u32;
    
    fn next(&mut self) -> Option<Self::Item> {
        // ...
    }
}
```

**Associated types vs generic traits:**

```rust
// With associated type
trait Iterator {
    type Item;
    fn next(&mut self) -> Option<Self::Item>;
}

// With generic (hypothetical)
trait Iterator<Item> {
    fn next(&mut self) -> Option<Item>;
}
```

**When to use associated types:**
- Only one implementation makes sense for a type
- Cleaner API (don't need to specify type every time)

## 10. Supertraits

Traits can require other traits:

```rust
use std::fmt;

trait OutlinePrint: fmt::Display {
    fn outline_print(&self) {
        let output = self.to_string();  // from Display
        let len = output.len();
        println!("{}", "*".repeat(len + 4));
        println!("*{}*", " ".repeat(len + 2));
        println!("* {} *", output);
        println!("*{}*", " ".repeat(len + 2));
        println!("{}", "*".repeat(len + 4));
    }
}
```

To implement `OutlinePrint`, you must also implement `Display`.

**Comparison:** Similar to interface inheritance in TypeScript/Java/C#.

## 11. Orphan Rule

You can implement:
- Your trait on any type
- Any trait on your type
- **But not:** external trait on external type

```rust
// ✅ OK - our trait, external type
impl Summary for Vec<i32> { }

// ✅ OK - external trait, our type
impl Display for MyStruct { }

// ❌ Error! - external trait, external type
impl Display for Vec<i32> { }
```

**Why?** Prevents conflicts if two crates implement the same trait for the same type.

**Workaround:** Newtype pattern (Week 7).

## 12. Derive Macros

Common traits can be automatically implemented:

```rust
#[derive(Debug, Clone, PartialEq, Eq, PartialOrd, Ord, Hash)]
struct Point {
    x: i32,
    y: i32,
}
```

**Derivable traits:**
- `Debug`, `Clone`, `Copy`
- `PartialEq`, `Eq`
- `PartialOrd`, `Ord`
- `Hash`
- `Default`

**Pattern:** Derive as many as make sense for your type!

## Trait Design Patterns

### Pattern 1: Builder Pattern

```rust
trait Builder {
    type Output;
    fn build(self) -> Self::Output;
}

struct ConfigBuilder {
    host: Option<String>,
    port: Option<u32>,
}

impl ConfigBuilder {
    fn host(mut self, host: String) -> Self {
        self.host = Some(host);
        self
    }
    
    fn port(mut self, port: u32) -> Self {
        self.port = Some(port);
        self
    }
}

impl Builder for ConfigBuilder {
    type Output = Config;
    
    fn build(self) -> Config {
        Config {
            host: self.host.unwrap_or_default(),
            port: self.port.unwrap_or(8080),
        }
    }
}
```

### Pattern 2: Extension Trait

Add methods to existing types:

```rust
trait StringExt {
    fn word_count(&self) -> usize;
}

impl StringExt for str {
    fn word_count(&self) -> usize {
        self.split_whitespace().count()
    }
}

let s = "hello world rust";
println!("{}", s.word_count());  // 3
```

## Comparison Summary

| Rust Traits | TypeScript Interfaces | Java Interfaces | C# Interfaces |
|-------------|----------------------|-----------------|---------------|
| Can have default methods | ✅ | ✅ (Java 8+) | ✅ |
| Can implement for external types | ✅ | ❌ | ❌ |
| Associated types | ✅ | ❌ | ❌ |
| Used for generics | ✅ | ✅ | ✅ |
| Can have fields | ❌ | ❌ | ❌ | ❌ |
| Multiple inheritance | ✅ | ✅ | ✅ | ✅ |

**Rust advantage:** More powerful and flexible than traditional interfaces!

## Practice Time

Traits are essential to idiomatic Rust. Practice with [Week 6 Exercises](../practical/week6-exercises.md)!

## Summary

- ✅ Traits define shared behavior (like interfaces)
- ✅ Implement traits with `impl TraitName for Type`
- ✅ Use traits as bounds on generics
- ✅ Derive common traits with `#[derive(...)]`
- ✅ Trait objects (`dyn Trait`) enable dynamic dispatch
- ✅ Associated types simplify trait APIs
- ✅ Traits are more powerful than traditional interfaces

**Next:** [Week 7: Lifetimes](week7-lifetimes.md) - Mastering Rust's reference safety!

---

[← Week 5](week5-collections.md) | [Home](../README.md) | [Next: Week 7 →](week7-lifetimes.md)
