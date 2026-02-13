# Week 3: Structs, Enums & Pattern Matching Exercises

These exercises focus on Rust's powerful type system and pattern matching capabilities.

## Exercise 1: Student Record System

Create a student management system using structs.

**Requirements:**

```rust
// Define a Student struct with fields:
// - name: String
// - age: u8
// - gpa: f64
// - major: String

// Implement these functions:
fn create_student(name: String, age: u8, gpa: f64, major: String) -> Student {
    // Create and return a Student
}

fn print_student(student: &Student) {
    // Print student details in a nice format
}

fn is_honor_student(student: &Student) -> bool {
    // Return true if GPA >= 3.5
}

fn average_gpa(students: &[Student]) -> f64 {
    // Calculate average GPA across all students
}
```

**Example output:**
```
Name: Alice Smith
Age: 20
GPA: 3.8
Major: Computer Science
Honor Student: Yes

Average GPA: 3.6
```

**Hints:**
- Struct definition: `struct Name { field: Type, ... }`
- Access fields with dot notation: `student.gpa`
- For printing, use `{}` with format strings

## Exercise 2: Geometric Shapes with Methods

Create shapes with associated methods.

**Requirements:**

```rust
// Define a Rectangle struct with width and height
// Define a Circle struct with radius

// Implement methods:
impl Rectangle {
    fn new(width: f64, height: f64) -> Rectangle {
        // Constructor
    }
    
    fn area(&self) -> f64 {
        // Calculate area
    }
    
    fn perimeter(&self) -> f64 {
        // Calculate perimeter
    }
    
    fn is_square(&self) -> bool {
        // Check if it's a square
    }
}

impl Circle {
    fn new(radius: f64) -> Circle {
        // Constructor
    }
    
    fn area(&self) -> f64 {
        // Calculate area (π * r²)
    }
    
    fn circumference(&self) -> f64 {
        // Calculate circumference (2 * π * r)
    }
}
```

Test with various shapes and print their properties.

**Hints:**
- Use `std::f64::consts::PI` for π
- `impl` blocks define methods on structs
- `&self` is the first parameter for instance methods
- Constructor pattern: `fn new(...) -> Self`

## Exercise 3: Enum-Based State Machine

Create a traffic light system using enums.

**Requirements:**

```rust
// Define an enum TrafficLight with variants: Red, Yellow, Green

// Implement functions:
fn next_light(current: TrafficLight) -> TrafficLight {
    // Return the next state: Red -> Green -> Yellow -> Red
}

fn duration_seconds(light: &TrafficLight) -> u32 {
    // Red: 60 seconds, Yellow: 5 seconds, Green: 45 seconds
}

fn can_cross(light: &TrafficLight) -> bool {
    // Pedestrians can cross only on Red
}

fn print_light(light: &TrafficLight) {
    // Print the current light and instructions
}
```

**Example output:**
```
Current: Green
Duration: 45 seconds
Can cross: No
Instructions: Keep driving

Current: Yellow
Duration: 5 seconds
Can cross: No
Instructions: Prepare to stop
```

**Hints:**
- Enum definition: `enum Name { Variant1, Variant2, ... }`
- Match on enum: `match light { TrafficLight::Red => ..., ... }`
- Enums without data are simple variants

## Exercise 4: Option<T> Practice

Work with Option to handle missing data.

**Requirements:**

```rust
// Find element in array, return Option
fn find_element(arr: &[i32], target: i32) -> Option<usize> {
    // Return Some(index) if found, None otherwise
}

// Get nth element safely
fn get_nth(arr: &[i32], n: usize) -> Option<i32> {
    // Return Some(element) if in bounds, None otherwise
}

// Divide two numbers, return None if divisor is 0
fn safe_divide(dividend: f64, divisor: f64) -> Option<f64> {
    // Your code here
}

// Parse string to number, handle errors with Option
fn parse_age(s: &str) -> Option<u8> {
    // Parse string to u8, return None if invalid
}
```

Test all functions and handle both Some and None cases.

**Hints:**
- Return `Some(value)` when successful
- Return `None` when operation fails
- Use `match` to extract values from Option
- `.parse()` returns a Result (convert to Option with `.ok()`)

**Challenge:** Chain multiple Option operations using `.and_then()` or `.map()`

## Exercise 5: Enum with Data

Create a payment system with enums containing data.

**Requirements:**

```rust
// Define an enum PaymentMethod:
// - Cash(f64) - amount
// - CreditCard { number: String, cvv: u16 }
// - BankTransfer { account: String, routing: String }
// - Crypto { wallet_address: String, coin: String }

// Implement functions:
fn process_payment(method: &PaymentMethod, amount: f64) -> String {
    // Return a message describing the payment
    // For cash, check if amount is sufficient
}

fn payment_fee(method: &PaymentMethod) -> f64 {
    // Cash: 0%, CreditCard: 2.5%, BankTransfer: 1%, Crypto: 0.5%
}

fn is_instant(method: &PaymentMethod) -> bool {
    // Cash and CreditCard are instant, others are not
}
```

**Example output:**
```
Payment: CreditCard ending in 1234
Amount: $100.00
Fee: $2.50
Total: $102.50
Instant: Yes
```

**Hints:**
- Enum variants can hold data: `Variant(Type)` or `Variant { field: Type }`
- Match patterns can destructure: `PaymentMethod::Cash(amount) => ...`
- Access fields in named variants: `PaymentMethod::CreditCard { number, cvv } => ...`

## Exercise 6: Pattern Matching Mastery

Practice complex pattern matching.

**Requirements:**

```rust
// Classify a number into a category
fn classify_number(n: i32) -> &'static str {
    // Use match with guards
    // Negative: "Negative"
    // Zero: "Zero"
    // 1-10: "Small positive"
    // 11-100: "Medium positive"
    // 101+: "Large positive"
}

// Describe a coordinate position
fn describe_point(x: i32, y: i32) -> &'static str {
    // (0, 0): "Origin"
    // (_, 0): "On X-axis"
    // (0, _): "On Y-axis"
    // (x, y) if x == y: "On diagonal"
    // (x, y) if x > 0 && y > 0: "Quadrant I"
    // etc. for all four quadrants
}

// Match tuple patterns
fn describe_pair(pair: (i32, i32)) -> String {
    // (0, 0): "Both zero"
    // (0, _): "First is zero"
    // (_, 0): "Second is zero"
    // (x, y) if x == y: "Equal numbers"
    // (x, y) if x > y: "First is greater"
    // (x, y): "Second is greater"
}
```

**Hints:**
- Use `_` to ignore values
- Use guards: `match value { pattern if condition => ... }`
- Patterns can match structure: `(x, y)`, `Some(value)`, etc.
- Use `..` for ranges: `1..=10`

## Exercise 7: Result<T, E> with Match

Work with Option and Result using pattern matching.

**Requirements:**

```rust
// Parse and validate age
fn parse_and_validate_age(s: &str) -> Option<u8> {
    // Parse string to u8
    // Return None if parsing fails OR age is > 120
}

// Calculate percentage safely
fn percentage(part: u32, total: u32) -> Option<f64> {
    // Return None if total is 0
    // Otherwise return (part / total) * 100
}

// Get grade from score
fn get_grade(score: u8) -> Option<char> {
    // 90-100: 'A', 80-89: 'B', 70-79: 'C', 60-69: 'D', 0-59: 'F'
    // Return None if score > 100
}

// Test these functions with match expressions:
// match parse_and_validate_age("25") {
//     Some(age) => println!("Valid age: {}", age),
//     None => println!("Invalid age"),
// }
```

**Hints:**
- Use `if let` for simpler cases: `if let Some(value) = option { ... }`
- Chain operations: `s.parse::<u8>().ok()?` with early return
- Match arms are exhaustive - handle all cases

## Exercise 8: Building a Menu System

Create a restaurant menu with structs and enums.

**Requirements:**

```rust
// Define MenuItem struct:
// - name: String
// - category: Category (enum)
// - price: f64
// - calories: u32

// Define Category enum:
// Appetizer, MainCourse, Dessert, Beverage

// Define OrderStatus enum:
// Pending, Preparing, Ready, Delivered

// Implement:
impl MenuItem {
    fn new(name: String, category: Category, price: f64, calories: u32) -> MenuItem {
        // Constructor
    }
    
    fn display(&self) {
        // Print item details
    }
}

fn filter_by_category(menu: &[MenuItem], category: Category) -> Vec<&MenuItem> {
    // Return all items in the category
}

fn calculate_total(items: &[&MenuItem]) -> f64 {
    // Sum up prices
}

fn healthy_items(menu: &[MenuItem]) -> Vec<&MenuItem> {
    // Return items with calories < 500
}
```

**Hints:**
- Store Category as an enum field in the struct
- Use `Vec::new()` and `.push()` to build vectors
- Use iterators and filters for efficiency
- You'll need to derive traits: `#[derive(Debug, PartialEq)]`

## Exercise 9: Pattern Matching with if let

Practice the `if let` syntax for simpler pattern matching.

**Requirements:**

```rust
// Search for a user by ID
fn find_user_by_id(users: &[(u32, String)], id: u32) -> Option<String> {
    // Find user by ID, return name if found
}

// In main, use if let to handle the result:
fn main() {
    let users = vec![
        (1, String::from("Alice")),
        (2, String::from("Bob")),
        (3, String::from("Charlie")),
    ];
    
    // Use if let to print user if found
    // Use else to print "User not found"
    
    // Also implement with match to compare
}

// Parse configuration value
fn parse_config(value: Option<&str>) -> String {
    // Use if let to extract value, or return "default" if None
}

// Check if number is even and positive
fn check_number(n: Option<i32>) {
    // Use if let with guard to check if Some(x) where x is even and positive
}
```

**Hints:**
- `if let` syntax: `if let Some(value) = option { ... } else { ... }`
- Useful when you only care about one pattern
- Can combine with `else if let` for multiple patterns
- More concise than full `match` for simple cases

## Exercise 10: Complex Type System

Build a file system representation using nested enums and structs.

**Requirements:**

```rust
// Define FileType enum:
// - Text { content: String }
// - Image { format: String, size_kb: u32 }
// - Video { format: String, duration_sec: u32, size_mb: u32 }

// Define File struct:
// - name: String
// - file_type: FileType

// Define Permission enum:
// - ReadOnly
// - ReadWrite
// - Execute

// Implement:
impl File {
    fn new(name: String, file_type: FileType) -> File {
        // Constructor
    }
    
    fn describe(&self) -> String {
        // Return description based on file type
    }
    
    fn size_description(&self) -> String {
        // Return size info (differs by type)
    }
}

fn filter_by_type(files: &[File], type_name: &str) -> Vec<&File> {
    // Return all files of given type ("text", "image", "video")
}

fn total_storage(files: &[File]) -> u32 {
    // Calculate total storage in KB
    // Text: estimate 1 KB per 100 chars
    // Image: use size_kb
    // Video: convert size_mb to KB
}
```

**Example output:**
```
File: document.txt
Type: Text
Content preview: "This is a sample..."
Size: ~5 KB

File: photo.jpg
Type: Image (JPEG)
Size: 250 KB

Total storage: 3,421 KB
```

**Hints:**
- Nested enums can contain structs and vice versa
- Use `match` inside methods to handle different variants
- String methods: `.len()` gives character count
- Think about data organization: what belongs in enum vs struct?

## Testing Your Solutions

For each exercise:

1. Ensure your code compiles without warnings: `cargo build`
2. Test with various inputs, especially edge cases
3. Use `cargo clippy` for suggestions
4. Try deriving `Debug` trait to print structs: `#[derive(Debug)]`
5. Use `println!("{:?}", value)` to debug print

## Common Patterns to Practice

**Destructuring in match:**
```rust
match payment {
    PaymentMethod::Cash(amount) => println!("Cash: ${}", amount),
    PaymentMethod::CreditCard { number, .. } => println!("Card: {}", number),
}
```

**Option handling:**
```rust
// With match
match maybe_value {
    Some(v) => println!("Got: {}", v),
    None => println!("Nothing"),
}

// With if let
if let Some(v) = maybe_value {
    println!("Got: {}", v);
}
```

**Method chaining:**
```rust
let result = some_option
    .map(|x| x * 2)
    .filter(|x| x > &10)
    .unwrap_or(0);
```

## What You're Practicing

- ✅ Defining and using structs
- ✅ Creating methods with `impl` blocks
- ✅ Defining enums with and without data
- ✅ Pattern matching with `match`
- ✅ Simplified matching with `if let`
- ✅ Working with `Option<T>`
- ✅ Match guards and complex patterns
- ✅ Destructuring data structures
- ✅ Building type-safe systems

**Master these concepts!** They're fundamental to idiomatic Rust.

When ready, move to [Week 4 Theory: Error Handling](../theory/week4-errors.md)

---

[← Theory](../theory/week3-types.md) | [Home](../README.md) | [Next: Week 4 Theory →](../theory/week4-errors.md)
