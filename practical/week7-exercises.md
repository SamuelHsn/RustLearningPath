# Week 7: Lifetimes Exercises

These exercises focus on understanding and working with Rust's lifetime system.

## Exercise 1: Basic Lifetime Annotations

Practice adding lifetime annotations to functions.

**Requirements:**

```rust
// Fix the lifetime annotations
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}

// Return the first word of a string
fn first_word(s: &str) -> &str {
    // Find first space and return slice up to it
    // If no space, return whole string
}

// Return reference to the longer of two slices
fn longer_slice<'a>(x: &'a [i32], y: &'a [i32]) -> &'a [i32] {
    // Implementation
}

// Return reference to largest element
fn find_max<'a>(slice: &'a [i32]) -> &'a i32 {
    // Return reference to maximum value
}
```

**Test in main:**
```rust
let s1 = "hello";
let s2 = "world!";
let result = longest(s1, s2);
println!("Longest: {}", result);
```

**Hints:**
- Lifetime `'a` indicates references have same lifetime
- Return value lifetime tied to input parameters
- Split string with `.split_whitespace().next()`
- Use `.iter().max()` for finding maximum

## Exercise 2: Structs with Lifetimes

Create structs that hold references.

**Requirements:**

```rust
// A struct that holds a string slice
struct Article<'a> {
    title: &'a str,
    author: &'a str,
    content: &'a str,
}

impl<'a> Article<'a> {
    fn new(title: &'a str, author: &'a str, content: &'a str) -> Self {
        // Constructor
    }
    
    fn headline(&self) -> String {
        // Return formatted headline: "Title by Author"
    }
    
    fn preview(&self, length: usize) -> &str {
        // Return first 'length' characters of content
    }
    
    fn word_count(&self) -> usize {
        // Count words in content
    }
}

// A struct that holds the best element
struct Best<'a, T> {
    value: &'a T,
}

impl<'a, T: PartialOrd> Best<'a, T> {
    fn new(slice: &'a [T]) -> Option<Self> {
        // Find and store reference to max element
    }
    
    fn get(&self) -> &T {
        // Return reference to best value
    }
}
```

**Test with:**
```rust
let title = "Rust Lifetimes";
let author = "Rustacean";
let content = "Lifetimes are a powerful feature...";
let article = Article::new(title, author, content);
```

**Hints:**
- Struct lifetime: `struct Name<'a>`
- Impl lifetime: `impl<'a> Name<'a>`
- Methods don't need explicit lifetimes if only `&self`
- Use `.get(..length)` for slicing

## Exercise 3: Multiple Lifetimes

Work with functions that have multiple lifetime parameters.

**Requirements:**

```rust
// Return first string, but type-checked against second
fn choose_first<'a, 'b>(first: &'a str, _second: &'b str) -> &'a str {
    first
}

// More complex: return a slice based on two inputs
struct Context<'s> {
    text: &'s str,
}

impl<'s> Context<'s> {
    fn new(text: &'s str) -> Self {
        Self { text }
    }
    
    // Takes a pattern with different lifetime
    fn find_pattern<'p>(&self, pattern: &'p str) -> Option<&'s str> {
        // Find pattern in text and return the found slice
        // The returned slice has lifetime 's, not 'p
    }
    
    // Takes a separator and returns part of text
    fn split_at_pattern<'p>(&self, sep: &'p str) -> (&'s str, &'s str) {
        // Split self.text at first occurrence of sep
        // Return (before, after)
    }
}
```

**Hints:**
- Different lifetimes for independent references
- Return lifetime matches the source lifetime
- Use `.find()` to locate substring
- Use `.split_once()` or manual indexing

## Exercise 4: Lifetime Elision Understanding

Understand when lifetimes can be omitted.

**Requirements:**

Write these functions WITHOUT explicit lifetime annotations (using elision rules):

```rust
// Rule 1: Each input parameter gets its own lifetime
fn first_element(slice: &[i32]) -> &i32 {
    &slice[0]
}

// Rule 2: If one input lifetime, output gets same lifetime
fn last_element(slice: &[i32]) -> &i32 {
    // Return reference to last element
}

// Rule 3: If &self, output gets self's lifetime
struct StringWrapper {
    s: String,
}

impl StringWrapper {
    fn get_slice(&self) -> &str {
        // Return &self.s
    }
    
    fn first_char(&self) -> Option<char> {
        // Return first character
    }
}

// Now write these that NEED explicit lifetimes:

// Multiple inputs, specific output lifetime needed
fn select_slice<'a>(first: &'a [i32], second: &'a [i32], use_first: bool) -> &'a [i32] {
    // Return first or second based on flag
}
```

**Hints:**
- Elision rules apply when lifetimes are obvious
- Single input → output has same lifetime
- `&self` methods → return has same lifetime as self
- Multiple inputs need explicit annotation

## Exercise 5: Lifetime Bounds

Work with generic types and lifetime bounds.

**Requirements:**

```rust
use std::fmt::Display;

// Generic reference holder
struct Ref<'a, T: 'a> {
    value: &'a T,
}

impl<'a, T> Ref<'a, T> {
    fn new(value: &'a T) -> Self {
        Self { value }
    }
    
    fn get(&self) -> &T {
        self.value
    }
}

// Function with lifetime and trait bounds
fn print_ref<'a, T: 'a + Display>(r: Ref<'a, T>) {
    println!("Value: {}", r.get());
}

// Cache that holds computed results
struct Cache<'a, T: 'a> {
    results: Vec<&'a T>,
}

impl<'a, T> Cache<'a, T> {
    fn new() -> Self {
        Self { results: Vec::new() }
    }
    
    fn add(&mut self, item: &'a T) {
        self.results.push(item);
    }
    
    fn get_all(&self) -> &[&'a T] {
        &self.results
    }
}

// Function that uses cache
fn cache_max_values<'a>(numbers: &'a [i32], cache: &mut Cache<'a, i32>, threshold: i32) {
    // Add references to numbers > threshold to cache
}
```

**Hints:**
- `T: 'a` means T must live at least as long as 'a
- Needed when storing references in generic types
- Cache stores references to external data
- Filter and add matching elements

## Exercise 6: Static Lifetime

Work with the 'static lifetime.

**Requirements:**

```rust
// Return a string slice with 'static lifetime
fn get_static_str() -> &'static str {
    "This string is embedded in the binary"
}

// Store either borrowed or static string
enum StringRef<'a> {
    Borrowed(&'a str),
    Static(&'static str),
}

impl<'a> StringRef<'a> {
    fn as_str(&self) -> &str {
        match self {
            StringRef::Borrowed(s) => s,
            StringRef::Static(s) => s,
        }
    }
    
    fn is_static(&self) -> bool {
        // Return true if Static variant
    }
}

// Function that might return static or borrowed
fn get_string_ref(use_static: bool, borrowed: &str) -> StringRef {
    if use_static {
        StringRef::Static("static string")
    } else {
        StringRef::Borrowed(borrowed)
    }
}

// Global constants have static lifetime
static GLOBAL_DATA: &str = "Global constant";

fn use_global() -> &'static str {
    GLOBAL_DATA
}
```

**Hints:**
- `'static` lives for entire program
- String literals are `&'static str`
- Static variables are `'static`
- 'static is subset of any lifetime 'a

## Exercise 7: Lifetime in Iterators

Create iterators with lifetime constraints.

**Requirements:**

```rust
// Iterator that yields references to elements
struct SliceIter<'a, T> {
    slice: &'a [T],
    index: usize,
}

impl<'a, T> SliceIter<'a, T> {
    fn new(slice: &'a [T]) -> Self {
        Self { slice, index: 0 }
    }
}

impl<'a, T> Iterator for SliceIter<'a, T> {
    type Item = &'a T;
    
    fn next(&mut self) -> Option<Self::Item> {
        // Return next element reference
    }
}

// Iterator over windows of fixed size
struct WindowIter<'a, T> {
    slice: &'a [T],
    window_size: usize,
    position: usize,
}

impl<'a, T> WindowIter<'a, T> {
    fn new(slice: &'a [T], window_size: usize) -> Self {
        // Initialize
    }
}

impl<'a, T> Iterator for WindowIter<'a, T> {
    type Item = &'a [T];
    
    fn next(&mut self) -> Option<Self::Item> {
        // Return window slice
    }
}
```

**Test with:**
```rust
let data = vec![1, 2, 3, 4, 5];
for item in SliceIter::new(&data) {
    println!("{}", item);
}
```

**Hints:**
- Iterator yields references with lifetime 'a
- Check bounds before returning slice
- Increment position after returning
- Window ends when position + size > length

## Exercise 8: Complex Borrowing Scenarios

Handle complex lifetime relationships.

**Requirements:**

```rust
// Parser that holds reference to input
struct Parser<'a> {
    input: &'a str,
    position: usize,
}

impl<'a> Parser<'a> {
    fn new(input: &'a str) -> Self {
        Self { input, position: 0 }
    }
    
    fn current_char(&self) -> Option<char> {
        // Return char at current position
    }
    
    fn advance(&mut self) {
        // Move position forward
    }
    
    fn peek(&self, offset: usize) -> Option<char> {
        // Look ahead without moving position
    }
    
    fn slice_from_start(&self) -> &'a str {
        // Return slice from 0 to current position
    }
    
    fn remaining(&self) -> &'a str {
        // Return slice from current position to end
    }
}

// Token that references original input
struct Token<'a> {
    kind: TokenKind,
    text: &'a str,
}

#[derive(Debug, PartialEq)]
enum TokenKind {
    Word,
    Number,
    Whitespace,
}

impl<'a> Parser<'a> {
    fn next_token(&mut self) -> Option<Token<'a>> {
        // Parse and return next token
        // Token's text should reference self.input
    }
}
```

**Hints:**
- Parser and Token share same lifetime
- Use `.chars().nth()` for character access
- Skip whitespace before parsing token
- Token references original input buffer

## Exercise 9: Lifetime in Closures

Work with closures that capture references.

**Requirements:**

```rust
// Function that returns closure with lifetime
fn make_adder<'a>(x: &'a i32) -> impl Fn(i32) -> i32 + 'a {
    move |y| x + y
}

// Function that filters using closure
fn filter_by_prefix<'a>(
    strings: &'a [String],
    prefix: &'a str,
) -> Vec<&'a str> {
    // Return strings starting with prefix
    // Use closure with .filter()
}

// Higher-order function with lifetime
fn apply_to_each<'a, F>(slice: &'a [i32], f: F) -> Vec<i32>
where
    F: Fn(&'a i32) -> i32,
{
    // Apply function to each element
}

// Create closure that captures reference
fn create_checker<'a>(threshold: &'a i32) -> impl Fn(&i32) -> bool + 'a {
    // Return closure that checks if value > threshold
}
```

**Test with:**
```rust
let value = 5;
let adder = make_adder(&value);
println!("{}", adder(3)); // 8
```

**Hints:**
- Closure captures references from environment
- `move` transfers ownership to closure
- `impl Fn() + 'a` for returning closures
- Closure lifetime must cover all captures

## Exercise 10: Lifetime Subtyping and Variance

Explore advanced lifetime relationships.

**Requirements:**

```rust
// Demonstrate that 'static is subtype of any lifetime
fn use_either<'a>(local: &'a str) -> &'a str {
    let static_str: &'static str = "static";
    // Can assign 'static to 'a
    if local.is_empty() {
        static_str
    } else {
        local
    }
}

// Container with invariant lifetime
struct Container<'a> {
    data: &'a mut i32,
}

impl<'a> Container<'a> {
    fn new(data: &'a mut i32) -> Self {
        Self { data }
    }
    
    fn get(&self) -> &i32 {
        self.data
    }
    
    fn set(&mut self, value: i32) {
        *self.data = value;
    }
}

// Function demonstrating lifetime shrinking
fn shrink_lifetime<'a, 'b: 'a>(outer: &'b str) -> &'a str {
    // 'b outlives 'a, so we can return &'a
    outer
}

// Complex nested references
fn get_first_word_from_first_line<'a>(text: &'a str) -> Option<&'a str> {
    // Get first line, then first word of that line
    // Both references come from same source
}

// Multiple related lifetimes
struct Context<'a, 'b> {
    short: &'a str,
    long: &'b str,
}

impl<'a, 'b: 'a> Context<'a, 'b> {
    fn new(short: &'a str, long: &'b str) -> Self 
    where
        'b: 'a  // long outlives short
    {
        Self { short, long }
    }
    
    fn get_short(&self) -> &'a str {
        self.short
    }
    
    fn get_long(&self) -> &'b str {
        self.long
    }
}
```

**Hints:**
- `'static` can be used where any lifetime expected
- `'b: 'a` means 'b outlives 'a
- Mutable references are invariant
- Can narrow lifetime but not extend it
- Chain operations on string slices

## Testing Your Solutions

For each exercise:

1. Start with simple cases
2. Pay attention to compiler errors
3. Understand what lifetime relationships exist
4. Test with different scopes
5. Verify no dangling references

## Common Lifetime Patterns

**Basic function:**
```rust
fn example<'a>(x: &'a str) -> &'a str
```

**Struct with lifetime:**
```rust
struct Example<'a> { field: &'a str }
```

**Multiple lifetimes:**
```rust
fn example<'a, 'b>(x: &'a str, y: &'b str) -> &'a str
```

**Lifetime bounds:**
```rust
fn example<'a, T: 'a>(x: &'a T)
where T: 'a { }
```

**Static lifetime:**
```rust
fn example() -> &'static str
```

## What You're Practicing

These exercises help you master:
- ✅ Basic lifetime annotations
- ✅ Structs with lifetimes
- ✅ Multiple lifetime parameters
- ✅ Lifetime elision rules
- ✅ Lifetime bounds on generics
- ✅ Static lifetime
- ✅ Lifetimes in iterators
- ✅ Complex borrowing scenarios
- ✅ Lifetimes in closures
- ✅ Lifetime subtyping and variance

**When you're done**, move on to [Week 8 Theory: Concurrency](../theory/week8-concurrency.md)!

---

[← Theory](../theory/week7-lifetimes.md) | [Home](../README.md) | [Next: Week 8 Theory →](../theory/week8-concurrency.md)
