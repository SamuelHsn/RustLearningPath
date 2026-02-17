# Week 2: Ownership & Borrowing Exercises

These exercises focus on Rust's ownership system. This is the most important week - take your time!

## Exercise 1: Move Semantics Exploration

Create a program that demonstrates move semantics.

**Requirements:**
- Create a `String` and assign it to another variable
- Try to use the original variable (observe the compiler error)
- Fix it by using `.clone()`
- Create a function that takes ownership of a `String` and returns it
- Create a function that takes ownership and does NOT return it

**Questions to explore:**
1. What happens when you try to use a moved value?
2. How does the compiler help you?
3. What's the difference between `Copy` and `Clone`?

**Hints:**
- Start with: `let s1 = String::from("hello");`
- Try: `let s2 = s1; println!("{}", s1);`
- Read the compiler error carefully

## Exercise 2: Reference Practice

Write functions using different types of references.

**Requirements:**

Create these functions:

```rust
// Function that borrows a String and returns its length
fn get_length(s: &String) -> usize {
    // Your code here
}

// Function that borrows a String and checks if it contains a character
fn contains_char(s: &String, c: char) -> bool {
    // Your code here
}

// Function that modifies a String by appending text
fn append_text(s: &mut String, text: &str) {
    // Your code here
}

// Function that modifies a String by making it uppercase
fn make_uppercase(s: &mut String) {
    // Your code here
}
```

Test all functions in `main()` with various strings.

**Expected output example:**
```
Original: "hello"
Length: 5
Contains 'e': true
After append: "hello world"
Uppercase: "HELLO WORLD"
```

**Hints:**
- `String::push_str()` appends to a string
- `String::chars()` iterates over characters
- Look up `String::to_uppercase()` but think about how to apply it

## Exercise 3: Borrowing Rules

Experiment with the borrowing rules to understand them deeply.

**Task 1: Multiple Immutable Borrows**
```rust
let s = String::from("hello");
let r1 = &s;
let r2 = &s;
let r3 = &s;
// Can you use all three references?
```

**Task 2: Mutable Borrow Restriction**
```rust
let mut s = String::from("hello");
let r1 = &mut s;
// Try to create r2 as another mutable borrow
// What happens?
```

**Task 3: Mixed Borrows**
```rust
let mut s = String::from("hello");
let r1 = &s;
let r2 = &s;
// Try to create a mutable borrow here
// What happens? Why?
```

**Task 4: Scope Understanding**
```rust
let mut s = String::from("hello");
let r1 = &s;
println!("{}", r1);
// r1 is no longer used after this point
// Can you create a mutable borrow here?
```

For each task, try to predict what will happen, then test it.

## Exercise 4: Ownership Transfer

Write a program that demonstrates ownership transfer between functions.

**Requirements:**

```rust
fn main() {
    let s = String::from("hello");
    
    // Pass s to process_string (s is moved)
    let s = process_string(s);
    
    // Pass s to add_exclamation (s is moved again)
    let s = add_exclamation(s);
    
    // Pass s to make_loud (s is moved again)
    let s = make_loud(s);
    
    println!("Final result: {}", s);
}

fn process_string(s: String) -> String {
    // Add " world" to the string
    // Return the modified string
}

fn add_exclamation(s: String) -> String {
    // Add "!" to the string
    // Return the modified string
}

fn make_loud(s: String) -> String {
    // Convert to uppercase
    // Return the modified string
}
```

**Expected output:**
```
Final result: HELLO WORLD!
```

**Challenge:** Can you rewrite these functions to use references instead of taking ownership?

## Exercise 5: String Slices

Practice working with string slices.

**Requirements:**

Implement these functions:

```rust
// Return the first word in a string
fn first_word(s: &str) -> &str {
    // Your code here
}

// Return the last word in a string
fn last_word(s: &str) -> &str {
    // Your code here
}

// Return the second word (if it exists)
fn second_word(s: &str) -> Option<&str> {
    // Your code here - use Option::Some or Option::None
}

// Count the number of words
fn word_count(s: &str) -> usize {
    // Your code here
}
```

Test with: `"The quick brown fox jumps"`

**Expected output:**
```
First word: "The"
Last word: "jumps"
Second word: Some("quick")
Word count: 5
```

**Hints:**
- Use `s.split_whitespace()` to split by spaces
- For first_word, find the first space
- Look at bytes: `s.as_bytes()`
- String slicing: `&s[start..end]`

## Exercise 6: Array Slices

Work with array slices.

**Requirements:**

```rust
// Return sum of a slice
fn sum_slice(slice: &[i32]) -> i32 {
    // Your code here
}

// Return average of a slice
fn average_slice(slice: &[i32]) -> f64 {
    // Your code here
}

// Find maximum in a slice
fn max_slice(slice: &[i32]) -> Option<i32> {
    // Return None if slice is empty
}

// Reverse a slice (return new Vec)
fn reverse_slice(slice: &[i32]) -> Vec<i32> {
    // Your code here
}
```

Test with: `[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]` and its slices.

**Example:**
```rust
let arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
println!("Sum of all: {}", sum_slice(&arr));
println!("Sum of first half: {}", sum_slice(&arr[..5]));
println!("Sum of second half: {}", sum_slice(&arr[5..]));
```

## Exercise 7: Fixing Ownership Errors

Here's broken code. Fix it!

```rust
fn main() {
    let s = String::from("hello");
    let len = calculate_length(s);
    println!("The length of '{}' is {}.", s, len);  // Error here!
}

fn calculate_length(s: String) -> usize {
    s.len()
}
```

**Task:** Fix this code in TWO different ways:
1. By returning the String along with the length (tuple)
2. By using a reference instead

Which approach is better? Why?

## Exercise 8: String Builder

Create a program that builds strings efficiently.

**Requirements:**

```rust
// Build a string by combining multiple parts
fn build_sentence(words: &[&str]) -> String {
    // Create an empty String
    // Loop through words and add them with spaces
    // Return the final string
}

// Build a formatted report
fn build_report(name: &str, age: u32, score: f64) -> String {
    // Create a string like: "Name: Alice, Age: 30, Score: 95.5"
}
```

Test with various inputs.

**Hints:**
- Start with `String::new()` or `String::from("")`
- Use `.push_str()` to append
- Use `format!()` macro for formatting: `format!("Name: {}", name)`

## Exercise 9: Safe Buffer Manipulation

Write functions that safely manipulate data.

**Requirements:**

```rust
// Safely get element at index (return Option)
fn safe_get(arr: &[i32], index: usize) -> Option<i32> {
    // Your code here
}

// Safely get a slice range (return Option)
fn safe_slice(arr: &[i32], start: usize, end: usize) -> Option<&[i32]> {
    // Check bounds, return slice if valid
}

// Swap two elements safely (return Result)
fn safe_swap(arr: &mut [i32], i: usize, j: usize) -> Result<(), String> {
    // Check bounds, swap if valid, return error message if not
}
```

**Hints:**
- Check: `index < arr.len()`
- Use `arr.get(index)` for safe access
- Return `None` or `Err` for invalid operations

## Exercise 10: Complex Borrowing Scenario

Build a simple text processor.

**Requirements:**

```rust
struct TextProcessor {
    text: String,
}

impl TextProcessor {
    fn new(text: String) -> TextProcessor {
        // Create new processor
    }
    
    fn get_text(&self) -> &str {
        // Return reference to text
    }
    
    fn append(&mut self, s: &str) {
        // Append to text
    }
    
    fn word_count(&self) -> usize {
        // Count words
    }
    
    fn clear(&mut self) {
        // Clear the text
    }
}

fn main() {
    let mut processor = TextProcessor::new(String::from("Hello"));
    println!("Text: {}", processor.get_text());
    println!("Words: {}", processor.word_count());
    
    processor.append(" world");
    println!("Text: {}", processor.get_text());
    println!("Words: {}", processor.word_count());
    
    processor.clear();
    println!("After clear: {}", processor.get_text());
}
```

**This introduces `struct` and `impl` - we'll cover these more in Week 3!**

## Exercise 11: Common Patterns

Practice common ownership patterns.

**Pattern 1: Consuming and Returning**
```rust
fn process_and_return(mut s: String) -> String {
    s.push_str(" processed");
    s
}
```

**Pattern 2: Borrowing for Reading**
```rust
fn analyze(s: &String) {
    println!("Length: {}, First char: {:?}", s.len(), s.chars().next());
}
```

**Pattern 3: Borrowing for Writing**
```rust
fn modify(s: &mut String) {
    s.push_str(" modified");
}
```

Create a program that uses all three patterns together with the same `String`.

## Debugging Tips

When you get ownership errors:

1. **Read the error carefully**: Rust's errors are helpful
2. **Identify the owner**: Who owns the value?
3. **Track the move**: Where did ownership move?
4. **Consider borrowing**: Can you use a reference instead?
5. **Check lifetimes**: Is the reference still valid?

Common errors you'll see:
- "value borrowed here after move"
- "cannot borrow as mutable more than once"
- "cannot borrow as mutable because it is also borrowed as immutable"

## Conceptual Questions

Think about these (no coding):

1. Why does Rust prevent multiple mutable references?
2. How does Rust prevent dangling references?
3. When should you use `.clone()` vs references?
4. What's the performance difference between moving and cloning?
5. Why are string literals (`&str`) different from `String`?

## What You're Practicing

- ✅ Understanding move semantics
- ✅ Working with references (`&` and `&mut`)
- ✅ Following borrowing rules
- ✅ Using slices
- ✅ Preventing common memory errors
- ✅ Writing memory-safe code

**Take your time with this week!** Ownership is challenging but crucial.

When ready, move to [Week 3 Theory: Structs and Enums](../theory/week3-types.md)

---

[← Theory](../theory/week2-ownership.md) | [Home](../README.md) | [Next: Week 3 Theory →](../theory/week3-types.md)
