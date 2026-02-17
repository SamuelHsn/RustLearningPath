# Week 7: Lifetimes - Advanced Borrowing

## Learning Objectives

By the end of this week, you will:
- Understand lifetime annotations and their purpose
- Apply lifetime elision rules correctly
- Use static lifetimes appropriately
- Handle complex borrowing scenarios
- Write functions that return references safely

## Why Lifetimes?

You've used references extensively (Week 2), but the compiler has been inferring lifetimes automatically. Sometimes you need to be explicit.

**The problem lifetimes solve:** Ensuring references are always valid.

```rust
// ❌ Won't compile - which reference to return?
fn longest(x: &str, y: &str) -> &str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

**Compiler error:** "expected named lifetime parameter"

The compiler can't tell how long the returned reference should be valid. Lifetimes fix this!

## 1. Lifetime Basics

### What Are Lifetimes?

**Lifetime:** The scope for which a reference is valid.

Every reference has a lifetime, but usually the compiler infers it. Sometimes you must specify explicitly.

**Mental model:** Lifetimes are like type annotations for references' validity periods.

### Lifetime Annotation Syntax

```rust
&i32        // A reference
&'a i32     // A reference with an explicit lifetime
&'a mut i32 // A mutable reference with an explicit lifetime
```

**Key point:** Lifetime annotations **don't change** how long references live. They describe relationships between lifetimes.

### The Longest Function

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

**What this means:**
- "The returned reference will be valid as long as both `x` and `y` are valid"
- Lifetime `'a` is the **smaller** (shorter) of the two input lifetimes

**Usage:**

```rust
fn main() {
    let string1 = String::from("long string is long");
    
    {
        let string2 = String::from("xyz");
        let result = longest(string1.as_str(), string2.as_str());
        println!("Longest: {}", result);  // ✅ OK
    }
}
```

**But this fails:**

```rust
fn main() {
    let string1 = String::from("long string is long");
    let result;
    
    {
        let string2 = String::from("xyz");
        result = longest(string1.as_str(), string2.as_str());
    }
    
    println!("Longest: {}", result);  // ❌ Error! string2 doesn't live long enough
}
```

The compiler prevents using a reference after its data has been dropped!

### Understanding Lifetime Annotations

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    // ...
}
```

Read as: "This function takes two string slices with the same lifetime `'a` and returns a string slice with that lifetime."

**Important:** The lifetime `'a` will be the **minimum** lifetime of `x` and `y`.

### Different Lifetime Parameters

```rust
fn longest<'a, 'b>(x: &'a str, y: &'b str) -> &'a str {
    x  // Only return x, so return type only needs to match x's lifetime
}
```

You can use different lifetime parameters when they're independent.

**Rule:** Only constrain lifetimes as much as necessary.

## 2. Lifetimes in Structs

Structs can hold references, but they need lifetime annotations:

```rust
struct ImportantExcerpt<'a> {
    part: &'a str,
}

fn main() {
    let novel = String::from("Call me Ishmael. Some years ago...");
    let first_sentence = novel.split('.').next().expect("Could not find a '.'");
    
    let i = ImportantExcerpt {
        part: first_sentence,
    };
    
    println!("{}", i.part);
}
```

**What this means:** An instance of `ImportantExcerpt` can't outlive the reference it holds in its `part` field.

**Comparison to other languages:**
- Java/C#/TypeScript: Don't have this concept (GC manages lifetime)
- C: Manual management, can have dangling pointers
- Rust: Compiler enforces safety

### Structs with Multiple Lifetimes

```rust
struct Context<'s, 't> {
    source: &'s str,
    target: &'t str,
}

impl<'s, 't> Context<'s, 't> {
    fn new(source: &'s str, target: &'t str) -> Context<'s, 't> {
        Context { source, target }
    }
}
```

Each reference can have its own lifetime.

## 3. Lifetime Elision Rules

The compiler can often infer lifetimes using **elision rules**. That's why you haven't needed them until now!

### The Three Rules

**Rule 1:** Each parameter that is a reference gets its own lifetime parameter.

```rust
fn foo(x: &i32)  // becomes
fn foo<'a>(x: &'a i32)

fn foo(x: &i32, y: &i32)  // becomes
fn foo<'a, 'b>(x: &'a i32, y: &'b i32)
```

**Rule 2:** If there's exactly one input lifetime, assign it to all output lifetimes.

```rust
fn foo(x: &i32) -> &i32  // becomes
fn foo<'a>(x: &'a i32) -> &'a i32
```

**Rule 3:** If there are multiple input lifetimes, but one is `&self` or `&mut self`, the lifetime of `self` is assigned to all output lifetimes.

```rust
impl<'a> ImportantExcerpt<'a> {
    fn announce_and_return_part(&self, announcement: &str) -> &str {
        println!("Attention: {}", announcement);
        self.part  // Return lifetime is tied to self
    }
}
```

### Examples of Elision

```rust
// No annotation needed - Rule 2
fn first_word(s: &str) -> &str {
    // Compiler infers: fn first_word<'a>(s: &'a str) -> &'a str
    &s[..1]
}

// No annotation needed - Rule 3
impl<'a> MyStruct<'a> {
    fn get_part(&self) -> &str {
        // Compiler infers return lifetime from &self
        self.part
    }
}

// Annotation required - multiple inputs, not self
fn longest(x: &str, y: &str) -> &str {  // ❌ Error!
    // Compiler can't determine which input's lifetime to use
}
```

**Pattern:** Write code without lifetimes first. Add them only when the compiler complains.

## 4. Lifetime Annotations in Method Definitions

```rust
impl<'a> ImportantExcerpt<'a> {
    fn level(&self) -> i32 {
        3
    }
    
    fn announce_and_return_part(&self, announcement: &str) -> &str {
        println!("Attention: {}", announcement);
        self.part  // Returns reference with lifetime of self
    }
}
```

**Key point:** When implementing methods on a struct with lifetime, repeat the lifetime declaration.

### Method that Returns Reference

```rust
impl<'a> ImportantExcerpt<'a> {
    fn part(&self) -> &'a str {
        self.part
    }
}
```

The return type `&'a str` must match the struct's lifetime `'a`.

## 5. The Static Lifetime

`'static` means "for the entire duration of the program".

```rust
let s: &'static str = "I have a static lifetime.";
```

**String literals** have static lifetime - they're baked into the program binary.

### When to Use 'static

```rust
// String literals
let s: &'static str = "hello";

// Constants
const MESSAGE: &'static str = "constant message";

// Leaked data (advanced)
let leaked: &'static str = Box::leak(Box::new(String::from("leaked")));
```

**Warning:** Don't use `'static` just to make code compile! Usually indicates a design issue.

### Common Misconception

```rust
// ❌ Bad - trying to fix compiler error with 'static
fn bad_function() -> &'static str {
    let s = String::from("hello");
    &s  // Error! s doesn't live long enough
}

// ✅ Good - return owned value
fn good_function() -> String {
    String::from("hello")
}
```

**Rule:** Only use `'static` when data truly lives for the entire program.

## 6. Advanced Lifetime Scenarios

### Lifetime Subtyping

```rust
// 'long outlives 'short
fn with_subtype<'long: 'short, 'short>(x: &'long str, y: &'short str) {
    // 'long: 'short means 'long lives at least as long as 'short
}
```

**Read as:** `'long` outlives `'short` (written `'long: 'short`).

### Lifetime Bounds on Trait Objects

```rust
trait MyTrait {
    fn do_something(&self);
}

// Trait object with lifetime
fn use_trait(t: &dyn MyTrait) {
    t.do_something();
}

// Or explicitly
fn use_trait_explicit<'a>(t: &'a dyn MyTrait) {
    t.do_something();
}
```

### Multiple Lifetime Constraints

```rust
struct Context<'a, 'b> {
    x: &'a str,
    y: &'b str,
}

impl<'a, 'b> Context<'a, 'b> {
    fn combine(&self) -> String 
    where
        'a: 'b,  // 'a must outlive 'b
    {
        format!("{}{}", self.x, self.y)
    }
}
```

## 7. Common Lifetime Patterns

### Pattern 1: Returning One of Many References

```rust
fn choose_first<'a>(first: &'a str, _second: &str) -> &'a str {
    first  // Only return first, so only its lifetime matters
}
```

**Pattern:** When returning one specific input, only that input's lifetime matters.

### Pattern 2: Structs with References

```rust
struct Parser<'a> {
    input: &'a str,
    pos: usize,
}

impl<'a> Parser<'a> {
    fn new(input: &'a str) -> Parser<'a> {
        Parser { input, pos: 0 }
    }
    
    fn peek(&self) -> Option<char> {
        self.input[self.pos..].chars().next()
    }
    
    fn next_token(&mut self) -> Option<&'a str> {
        // Returns slice of input with same lifetime
        let start = self.pos;
        self.pos += 1;
        Some(&self.input[start..self.pos])
    }
}
```

**Use case:** Parsers, iterators over borrowed data.

### Pattern 3: Caching References

```rust
struct Cache<'a, T> {
    data: &'a T,
    computed: Option<String>,
}

impl<'a, T> Cache<'a, T>
where
    T: std::fmt::Display,
{
    fn new(data: &'a T) -> Cache<'a, T> {
        Cache {
            data,
            computed: None,
        }
    }
    
    fn get(&mut self) -> &str {
        if self.computed.is_none() {
            self.computed = Some(self.data.to_string());
        }
        self.computed.as_ref().unwrap()
    }
}
```

### Pattern 4: Multiple References with Independent Lifetimes

```rust
fn compare<'a, 'b>(x: &'a str, y: &'b str) -> bool
where
    'a: 'b,  // x must live at least as long as y
{
    x.len() > y.len()
}
```

## 8. Lifetime Bounds in Generics

```rust
struct Ref<'a, T: 'a> {  // T: 'a means T can contain references with lifetime 'a
    value: &'a T,
}

// Modern Rust (2018+) - don't need T: 'a
struct Ref<'a, T> {
    value: &'a T,
}
```

**Modern rule:** Lifetime bounds on type parameters are usually inferred.

### Trait Bounds with Lifetimes

```rust
fn print_ref<'a, T>(t: &'a T)
where
    T: std::fmt::Display + 'a,
{
    println!("{}", t);
}
```

## 9. Common Lifetime Errors

### Error 1: Returning Reference to Local Variable

```rust
// ❌ Error!
fn dangle() -> &String {
    let s = String::from("hello");
    &s  // s goes out of scope!
}

// ✅ Solution: Return owned value
fn no_dangle() -> String {
    String::from("hello")
}
```

### Error 2: Reference Outlives Data

```rust
// ❌ Error!
let r;
{
    let x = 5;
    r = &x;  // x doesn't live long enough
}
println!("{}", r);

// ✅ Solution: Keep data alive
let x = 5;
let r = &x;
println!("{}", r);
```

### Error 3: Returning References with Wrong Lifetime

```rust
struct Context<'a> {
    data: &'a str,
}

impl<'a> Context<'a> {
    // ❌ Error! Can't return reference to local
    fn new_local(&self) -> &'a str {
        let s = String::from("local");
        &s  // Doesn't live long enough
    }
    
    // ✅ OK - returns reference to struct's data
    fn get_data(&self) -> &'a str {
        self.data
    }
}
```

## 10. When You Need Lifetimes

You need lifetime annotations when:

1. **Returning references from functions with multiple reference parameters:**
   ```rust
   fn longest<'a>(x: &'a str, y: &'a str) -> &'a str
   ```

2. **Structs holding references:**
   ```rust
   struct Excerpt<'a> { part: &'a str }
   ```

3. **Compiler can't infer relationships:**
   ```rust
   fn complex<'a, 'b>(x: &'a str, y: &'b str) -> &'a str
   ```

You **don't** need them when:
- Single reference parameter and single reference return
- No references in struct
- Elision rules apply

## 11. Practical Guidelines

### Guideline 1: Avoid References in Structs When Possible

```rust
// Prefer owned data
struct Config {
    hostname: String,  // Owned, no lifetime needed
}

// Over references
struct Config<'a> {
    hostname: &'a str,  // Requires lifetime management
}
```

**Trade-off:** Owned data is simpler but uses more memory.

### Guideline 2: Use Cow for Flexibility

```rust
use std::borrow::Cow;

struct Config<'a> {
    hostname: Cow<'a, str>,  // Can be borrowed or owned
}

// Use borrowed when possible
let config = Config {
    hostname: Cow::Borrowed("localhost"),
};

// Use owned when needed
let config = Config {
    hostname: Cow::Owned(format!("host-{}", id)),
};
```

### Guideline 3: Start Simple, Add Lifetimes as Needed

```rust
// Start with owned
struct Parser {
    input: String,
}

// If performance matters, switch to borrowed
struct Parser<'a> {
    input: &'a str,
}
```

## 12. Comparison to Other Languages

| Concept | Rust | C | Java/C# | TypeScript |
|---------|------|---|---------|------------|
| References | Checked at compile time | Manual, error-prone | GC, checked at runtime | GC, checked at runtime |
| Dangling | Impossible | Common bug | Prevented by GC | Prevented by GC |
| Performance | Zero-cost | Zero-cost | GC overhead | GC overhead |
| Complexity | High (learning) | High (error-prone) | Low | Low |

**Rust's advantage:** Compile-time guarantees with zero runtime cost.

## Practice Time

Lifetimes are challenging but essential for advanced Rust. Practice with [Week 7 Exercises](../practical/week7-exercises.md)!

## Summary

- ✅ Lifetimes ensure references are always valid
- ✅ Lifetime annotations describe relationships, not durations
- ✅ Three elision rules let you skip annotations often
- ✅ `'static` means "for the entire program"
- ✅ Structs with references need lifetime parameters
- ✅ Prefer owned data over references when possible
- ✅ Compiler error messages guide you to correct lifetimes

**Key insight:** Lifetimes are about proving safety to the compiler, not changing behavior!

**Next:** [Week 8: Concurrency](week8-concurrency.md) - Safe concurrent programming!

---

[← Week 6](week6-traits.md) | [Home](../README.md) | [Next: Week 8 →](week8-concurrency.md)
