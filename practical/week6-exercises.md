# Week 6: Traits and Generics Exercises

These exercises focus on Rust's trait system and generic programming.

## Exercise 1: Implementing Standard Traits

Implement common traits for custom types.

**Requirements:**

```rust
#[derive(Debug)]
struct Book {
    title: String,
    author: String,
    pages: u32,
    year: u32,
}

// Implement these traits:
// 1. Display - format as "Title by Author (Year)"
// 2. PartialEq - two books equal if same title and author
// 3. PartialOrd - order by year, then by title
// 4. Clone - deep copy

impl std::fmt::Display for Book {
    // Your implementation
}

// Also implement: PartialEq, PartialOrd, Clone
```

**Test in main:**
```rust
let book1 = Book { /* ... */ };
let book2 = book1.clone();
println!("{}", book1); // Uses Display
if book1 == book2 { /* Uses PartialEq */ }
if book1 < book2 { /* Uses PartialOrd */ }
```

**Hints:**
- `impl Trait for Type { ... }`
- Display uses `write!(f, "format", args)`
- PartialOrd requires PartialEq
- Compare tuples: `(self.year, &self.title).cmp(&(other.year, &other.title))`

## Exercise 2: Custom Trait Definition

Create your own trait and implement it for multiple types.

**Requirements:**

```rust
// Define a trait for things that can be described
trait Describable {
    fn describe(&self) -> String;
    fn short_description(&self) -> String {
        // Default implementation: first 50 chars
    }
}

// Implement for these types:
struct Person {
    name: String,
    age: u32,
    occupation: String,
}

struct Product {
    name: String,
    price: f64,
    category: String,
}

struct Movie {
    title: String,
    director: String,
    year: u32,
    rating: f64,
}

impl Describable for Person {
    // Your implementation
}

// Implement for Product and Movie too
```

**Create a function:**
```rust
fn print_description<T: Describable>(item: &T) {
    println!("{}", item.describe());
}
```

**Hints:**
- Traits define shared behavior
- Default methods can be overridden
- Use trait bounds: `<T: Trait>`
- Call trait methods like normal methods

## Exercise 3: Trait Objects and Dynamic Dispatch

Work with trait objects for runtime polymorphism.

**Requirements:**

```rust
trait Shape {
    fn area(&self) -> f64;
    fn perimeter(&self) -> f64;
    fn name(&self) -> &str;
}

struct Circle {
    radius: f64,
}

struct Rectangle {
    width: f64,
    height: f64,
}

struct Triangle {
    side_a: f64,
    side_b: f64,
    side_c: f64,
}

// Implement Shape for all three types

fn total_area(shapes: &[Box<dyn Shape>]) -> f64 {
    // Sum areas of all shapes
}

fn largest_shape(shapes: &[Box<dyn Shape>]) -> Option<&Box<dyn Shape>> {
    // Return shape with largest area
}

fn print_all_shapes(shapes: &[Box<dyn Shape>]) {
    // Print name and area of each shape
}
```

**Test with:**
```rust
let shapes: Vec<Box<dyn Shape>> = vec![
    Box::new(Circle { radius: 5.0 }),
    Box::new(Rectangle { width: 4.0, height: 6.0 }),
    Box::new(Triangle { side_a: 3.0, side_b: 4.0, side_c: 5.0 }),
];
```

**Hints:**
- `Box<dyn Trait>` for trait objects
- Use Heron's formula for triangle area
- `.iter().max_by()` for finding largest
- Trait objects enable heterogeneous collections

## Exercise 4: Generic Data Structures

Create generic types and implement them.

**Requirements:**

```rust
// Generic pair that holds two values of same type
struct Pair<T> {
    first: T,
    second: T,
}

impl<T> Pair<T> {
    fn new(first: T, second: T) -> Self {
        // Constructor
    }
    
    fn swap(self) -> Self {
        // Return new pair with swapped values
    }
}

// Add methods only for comparable types
impl<T: PartialOrd> Pair<T> {
    fn max(&self) -> &T {
        // Return reference to larger value
    }
    
    fn min(&self) -> &T {
        // Return reference to smaller value
    }
}

// Generic stack
struct Stack<T> {
    items: Vec<T>,
}

impl<T> Stack<T> {
    fn new() -> Self {
        // Create empty stack
    }
    
    fn push(&mut self, item: T) {
        // Add item to stack
    }
    
    fn pop(&mut self) -> Option<T> {
        // Remove and return top item
    }
    
    fn peek(&self) -> Option<&T> {
        // Return reference to top without removing
    }
    
    fn is_empty(&self) -> bool {
        // Check if stack is empty
    }
    
    fn size(&self) -> usize {
        // Return number of items
    }
}
```

**Hints:**
- `impl<T>` for generic implementation
- Trait bounds: `<T: Trait>` or `where T: Trait`
- Use Vec methods for stack operations
- `self` consumes, `&self` borrows

## Exercise 5: Multiple Trait Bounds

Work with functions requiring multiple traits.

**Requirements:**

```rust
use std::fmt::{Display, Debug};

fn print_and_debug<T: Display + Debug>(item: &T) {
    println!("Display: {}", item);
    println!("Debug: {:?}", item);
}

fn compare_and_print<T>(a: &T, b: &T) -> &T 
where
    T: PartialOrd + Display
{
    // Return and print the larger value
}

fn clone_and_modify<T>(item: &T) -> T 
where
    T: Clone + Default
{
    // Clone the item, or return default if cloning fails somehow
}

fn stringify<T: Display>(items: &[T]) -> String {
    // Convert slice to comma-separated string
    // [1, 2, 3] -> "1, 2, 3"
}

fn find_max<T: PartialOrd + Clone>(items: &[T]) -> Option<T> {
    // Find and return clone of maximum item
}
```

**Hints:**
- Multiple bounds: `T: Trait1 + Trait2`
- Alternative syntax: `where T: Trait1, T: Trait2`
- Use `.iter().map().collect()` for stringify
- Clone max to return owned value

## Exercise 6: Trait Inheritance and Associated Types

Explore advanced trait features.

**Requirements:**

```rust
// Base trait
trait Animal {
    fn name(&self) -> &str;
    fn sound(&self) -> &str;
    
    fn make_sound(&self) {
        println!("{} says {}", self.name(), self.sound());
    }
}

// Trait inheritance
trait Pet: Animal {
    fn owner(&self) -> &str;
    fn play(&self) {
        println!("{} is playing with {}", self.name(), self.owner());
    }
}

// Implement for concrete types
struct Dog {
    name: String,
    owner: String,
}

struct Cat {
    name: String,
    owner: String,
}

// Implement Animal and Pet for both

// Associated types
trait Container {
    type Item;
    
    fn add(&mut self, item: Self::Item);
    fn remove(&mut self) -> Option<Self::Item>;
    fn get(&self, index: usize) -> Option<&Self::Item>;
    fn len(&self) -> usize;
}

struct NumberContainer {
    numbers: Vec<i32>,
}

// Implement Container for NumberContainer with Item = i32
```

**Hints:**
- Subtrait requires supertrait implementation
- Associated types: `type Item = ConcreteType;`
- Access with `Self::Item` in implementations
- Trait inheritance: `trait Sub: Super { }`

## Exercise 7: Generic Algorithms

Implement generic algorithms that work with many types.

**Requirements:**

```rust
fn find<T: PartialEq>(slice: &[T], target: &T) -> Option<usize> {
    // Return index of first occurrence
}

fn count<T: PartialEq>(slice: &[T], target: &T) -> usize {
    // Count occurrences of target
}

fn filter_by<T, F>(vec: Vec<T>, predicate: F) -> Vec<T>
where
    F: Fn(&T) -> bool
{
    // Return new vec with elements matching predicate
}

fn map_values<T, U, F>(vec: Vec<T>, f: F) -> Vec<U>
where
    F: Fn(T) -> U
{
    // Transform each element using function f
}

fn reduce<T, F>(slice: &[T], initial: T, f: F) -> T
where
    T: Clone,
    F: Fn(T, &T) -> T
{
    // Reduce slice to single value using function f
}

fn partition<T, F>(vec: Vec<T>, predicate: F) -> (Vec<T>, Vec<T>)
where
    F: Fn(&T) -> bool
{
    // Split into (matching, not matching)
}
```

**Test with:**
```rust
let numbers = vec![1, 2, 3, 4, 5, 6];
let evens = filter_by(numbers, |n| n % 2 == 0);
let doubled = map_values(vec![1,2,3], |n| n * 2);
```

**Hints:**
- Closures implement `Fn`, `FnMut`, or `FnOnce`
- Use `.iter()`, `.filter()`, `.collect()` internally
- `Clone` bound needed for initial value
- Return tuple for partition

## Exercise 8: Trait Defaults and Overriding

Work with default implementations and selective overriding.

**Requirements:**

```rust
trait Summary {
    fn summarize_author(&self) -> String;
    
    fn summarize(&self) -> String {
        format!("(Read more from {}...)", self.summarize_author())
    }
}

struct NewsArticle {
    headline: String,
    location: String,
    author: String,
    content: String,
}

struct Tweet {
    username: String,
    content: String,
    reply: bool,
    retweet: bool,
}

struct BlogPost {
    title: String,
    author: String,
    content: String,
    tags: Vec<String>,
}

// Implement Summary for all three:
// - NewsArticle: Override summarize() with headline and location
// - Tweet: Use default summarize(), just implement summarize_author()
// - BlogPost: Override both methods

fn notify(item: &impl Summary) {
    println!("Breaking news! {}", item.summarize());
}
```

**Hints:**
- Default methods can call other trait methods
- Override by providing own implementation
- `impl Trait` syntax for parameters
- Can choose which methods to override

## Exercise 9: Generic Constraints and Marker Traits

Explore trait bounds and special traits.

**Requirements:**

```rust
use std::ops::Add;

// Generic function that only works with addable types
fn sum_vec<T>(vec: &[T]) -> T 
where
    T: Add<Output = T> + Default + Copy
{
    // Sum all elements
}

// Generic pair arithmetic
#[derive(Debug, Clone, Copy)]
struct Point<T> {
    x: T,
    y: T,
}

impl<T: Add<Output = T>> Add for Point<T> {
    type Output = Point<T>;
    
    fn add(self, other: Point<T>) -> Point<T> {
        // Add corresponding coordinates
    }
}

// Make it work with multiplication too
impl<T: std::ops::Mul<Output = T>> Point<T> {
    fn scale(self, factor: T) -> Point<T> {
        // Multiply both coordinates by factor
    }
}

// Constrain to numeric types
fn distance_squared<T>(p1: Point<T>, p2: Point<T>) -> T
where
    T: Copy + std::ops::Sub<Output = T> + std::ops::Mul<Output = T> + Add<Output = T>
{
    // Calculate (x2-x1)² + (y2-y1)²
}
```

**Hints:**
- `Add` trait from `std::ops`
- `Output` is associated type
- Multiple operator traits can be combined
- Copy needed to use values multiple times

## Exercise 10: Builder Pattern with Traits

Implement the builder pattern using traits.

**Requirements:**

```rust
// Generic builder trait
trait Builder {
    type Output;
    fn build(self) -> Self::Output;
}

struct Person {
    name: String,
    age: u32,
    email: Option<String>,
    phone: Option<String>,
    address: Option<String>,
}

struct PersonBuilder {
    name: Option<String>,
    age: Option<u32>,
    email: Option<String>,
    phone: Option<String>,
    address: Option<String>,
}

impl PersonBuilder {
    fn new() -> Self {
        // Create empty builder
    }
    
    fn name(mut self, name: String) -> Self {
        self.name = Some(name);
        self
    }
    
    fn age(mut self, age: u32) -> Self {
        self.age = Some(age);
        self
    }
    
    fn email(mut self, email: String) -> Self {
        // Set email and return self
    }
    
    fn phone(mut self, phone: String) -> Self {
        // Set phone and return self
    }
    
    fn address(mut self, address: String) -> Self {
        // Set address and return self
    }
}

impl Builder for PersonBuilder {
    type Output = Result<Person, String>;
    
    fn build(self) -> Self::Output {
        // Validate required fields (name, age)
        // Build Person or return error
    }
}

// Use like: 
// PersonBuilder::new().name("Alice".into()).age(30).email("alice@example.com".into()).build()
```

**Hints:**
- Builder pattern uses method chaining
- Return `self` to enable chaining
- Validate in `build()`
- Use `Option` for optional fields
- Return `Result` for error handling

## Testing Your Solutions

For each exercise:

1. Create project: `cargo new exercise_name`
2. Implement traits and generic functions
3. Test with multiple types
4. Verify trait bounds work correctly
5. Run `cargo clippy` for suggestions

## Key Concepts Reference

**Trait Basics:**
- Define behavior: `trait MyTrait { fn method(&self); }`
- Implement: `impl MyTrait for MyType { }`
- Default methods: provide body in trait definition

**Generics:**
- Generic function: `fn func<T>(item: T)`
- Generic struct: `struct Wrapper<T> { value: T }`
- Generic impl: `impl<T> Wrapper<T> { }`

**Trait Bounds:**
- Inline: `fn func<T: Trait>(item: T)`
- Where clause: `fn func<T>(item: T) where T: Trait`
- Multiple: `T: Trait1 + Trait2`

**Advanced:**
- Trait objects: `Box<dyn Trait>`
- Associated types: `type Item;`
- Trait inheritance: `trait Sub: Super`

## What You're Practicing

These exercises help you master:
- ✅ Implementing standard traits (Display, Debug, Clone, etc.)
- ✅ Creating custom traits
- ✅ Generic functions and types
- ✅ Trait bounds and constraints
- ✅ Trait objects and dynamic dispatch
- ✅ Associated types
- ✅ Default implementations
- ✅ Operator overloading
- ✅ Builder pattern
- ✅ Generic algorithms

**When you're done**, move on to [Week 7 Theory: Lifetimes](../theory/week7-lifetimes.md)!

---

[← Theory](../theory/week6-traits-generics.md) | [Home](../README.md) | [Next: Week 7 Theory →](../theory/week7-lifetimes.md)
