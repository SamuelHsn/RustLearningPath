# Week 4: Error Handling Exercises

These exercises focus on robust error handling with `Result<T, E>`, the `?` operator, and custom error types.

## Exercise 1: Basic Result<T, E> Practice

Get comfortable with Result by implementing safe operations.

**Requirements:**

```rust
// Safely divide two numbers
fn safe_division(dividend: f64, divisor: f64) -> Result<f64, String> {
    // Return Err if divisor is 0
    // Return Ok(result) otherwise
}

// Parse a string to integer
fn parse_number(s: &str) -> Result<i32, String> {
    // Parse the string
    // Return descriptive error message if parsing fails
}

// Get element from array safely
fn get_element(arr: &[i32], index: usize) -> Result<i32, String> {
    // Return Err with message if index out of bounds
    // Return Ok(element) otherwise
}

// Test in main with match:
fn main() {
    match safe_division(10.0, 0.0) {
        Ok(result) => println!("Result: {}", result),
        Err(e) => println!("Error: {}", e),
    }
}
```

**Example output:**
```
Error: Cannot divide by zero
Result: 5.0
Error: Index 10 out of bounds for array of length 5
```

**Hints:**
- Return `Ok(value)` for success
- Return `Err(message)` for errors
- Use `match` to handle both cases
- String errors are simple but not ideal for production

## Exercise 2: Error Propagation with ?

Practice using the `?` operator for cleaner error handling.

**Requirements:**

```rust
// Read a number from string and double it
fn read_and_double(s: &str) -> Result<i32, String> {
    // Parse string to i32
    // If successful, multiply by 2
    // Use ? operator for parsing
}

// Calculate percentage
fn calculate_percentage(part: u32, total: u32) -> Result<f64, String> {
    // Check if total is 0
    // Calculate percentage
    // Use ? to propagate errors
}

// Chain multiple operations
fn process_input(s: &str) -> Result<f64, String> {
    // 1. Parse to i32
    // 2. Check if positive (return error if not)
    // 3. Convert to f64
    // 4. Take square root
    // Use ? throughout
}

// Parse two numbers and add them
fn add_from_strings(a: &str, b: &str) -> Result<i32, String> {
    // Parse both strings
    // Add the results
    // Use ? for both parses
}
```

**Hints:**
- `?` operator automatically returns Err if the operation fails
- `?` unwraps Ok values for you
- Function must return `Result` to use `?`
- Convert different error types to String with `.map_err(|e| e.to_string())`

## Exercise 3: Multiple Error Types

Work with functions that can fail in different ways.

**Requirements:**

```rust
// Parse and validate age
fn validate_age(s: &str) -> Result<u8, String> {
    // Parse to u8 - might fail
    // Check if age is reasonable (1-120) - might fail
    // Return appropriate error messages
}

// Open file and count lines (don't actually implement file I/O yet)
// Simulate with a function that might fail
fn count_lines(filename: &str) -> Result<usize, String> {
    // Simulate: return error if filename is empty
    // Simulate: return error if filename doesn't end with .txt
    // Otherwise return a dummy line count
}

// Chain multiple validations
fn process_user_input(name: &str, age_str: &str, email: &str) -> Result<String, String> {
    // Validate name is not empty
    // Validate age using validate_age
    // Validate email contains '@'
    // Return success message if all valid
}
```

**Example output:**
```
Error: Name cannot be empty
Error: Age must be between 1 and 120
Error: Invalid email format
Success: User Alice (25, alice@example.com) validated
```

**Hints:**
- Check conditions and return early with `Err`
- Use `?` to propagate errors from called functions
- Provide descriptive error messages
- Think about validation order

## Exercise 4: Custom Error Type with Enum

Create a custom error type for better error handling.

**Requirements:**

```rust
// Define a custom error enum
enum MathError {
    DivisionByZero,
    NegativeSquareRoot,
    Overflow,
    InvalidInput(String),
}

// Implement Display for your error (we'll cover traits properly later)
// For now, just implement functions that use it

fn safe_sqrt(x: f64) -> Result<f64, MathError> {
    // Return error if x is negative
    // Return Ok(sqrt) otherwise
}

fn safe_divide(a: f64, b: f64) -> Result<f64, MathError> {
    // Return error if b is zero
    // Return Ok(a/b) otherwise
}

fn calculate(operation: &str, a: f64, b: f64) -> Result<f64, MathError> {
    // Match operation: "add", "divide", "sqrt_first", etc.
    // Return appropriate error or result
    // Use ? to propagate errors
}

// Helper to print errors
fn print_error(e: &MathError) {
    match e {
        MathError::DivisionByZero => println!("Error: Division by zero"),
        MathError::NegativeSquareRoot => println!("Error: Cannot take square root of negative"),
        MathError::Overflow => println!("Error: Calculation overflow"),
        MathError::InvalidInput(msg) => println!("Error: {}", msg),
    }
}
```

**Hints:**
- Custom error enums are more type-safe than String
- Enum variants can hold data: `InvalidInput(String)`
- Use `match` to handle different error types
- Pattern matching on errors allows specific handling

## Exercise 5: Result in Collections

Work with collections of Results.

**Requirements:**

```rust
// Parse a vector of strings to numbers
fn parse_numbers(strings: &[&str]) -> Result<Vec<i32>, String> {
    // Try to parse all strings
    // Return Err on first failure
    // Return Ok(vec) if all succeed
}

// Parse numbers, collecting all errors
fn parse_with_errors(strings: &[&str]) -> (Vec<i32>, Vec<String>) {
    // Parse all strings
    // Return tuple: (successful parses, error messages)
}

// Sum all numbers from strings
fn sum_from_strings(strings: &[&str]) -> Result<i32, String> {
    // Parse all numbers using ?
    // Sum them
    // Return the result
}

// Find first valid number
fn find_first_valid(strings: &[&str]) -> Result<i32, String> {
    // Try parsing each string
    // Return first successful parse
    // Return Err if none succeed
}
```

**Example:**
```rust
let inputs = vec!["42", "not a number", "17"];
match parse_numbers(&inputs) {
    Ok(nums) => println!("Parsed: {:?}", nums),
    Err(e) => println!("Error: {}", e),
}
```

**Hints:**
- Iterate and use `?` to fail fast
- Use `Vec::new()` and `.push()` to build result vector
- `collect()` can gather Results: `vec.iter().map(|s| s.parse()).collect()`
- Look up `.ok()` to convert Result to Option

## Exercise 6: Configuration Parser

Build a simple configuration parser with proper error handling.

**Requirements:**

```rust
// Define ConfigError enum
enum ConfigError {
    MissingField(String),
    InvalidValue(String),
    ParseError(String),
}

// Define Config struct
struct Config {
    host: String,
    port: u16,
    timeout_seconds: u32,
}

// Parse config from key-value pairs
fn parse_config(lines: &[&str]) -> Result<Config, ConfigError> {
    // Parse lines in format "key=value"
    // Extract host, port, timeout_seconds
    // Return appropriate errors if:
    //   - Required field is missing
    //   - Value cannot be parsed
    //   - Format is invalid
}

// Validate config
fn validate_config(config: &Config) -> Result<(), ConfigError> {
    // Check port is in range 1-65535
    // Check timeout is reasonable (> 0, < 3600)
    // Check host is not empty
}

// Load and validate in one function
fn load_config(lines: &[&str]) -> Result<Config, ConfigError> {
    // Parse config using ?
    // Validate config using ?
    // Return config
}
```

**Example input:**
```
host=localhost
port=8080
timeout_seconds=30
```

**Hints:**
- Use `.split('=')` to parse key-value pairs
- Use `.collect::<Vec<_>>()` to split into parts
- Match on field names to extract values
- Chain operations with `?`

## Exercise 7: Fallible Conversions

Practice converting between types with error handling.

**Requirements:**

```rust
// Convert string to bool
fn parse_bool(s: &str) -> Result<bool, String> {
    // Accept: "true", "false", "yes", "no", "1", "0"
    // Case insensitive
    // Return error for invalid values
}

// Convert string to enum
enum Color {
    Red,
    Green,
    Blue,
}

fn parse_color(s: &str) -> Result<Color, String> {
    // Parse string to Color enum
    // Case insensitive
}

// Convert temperature with validation
fn celsius_to_fahrenheit(c: f64) -> Result<f64, String> {
    // Check if temperature is valid (above absolute zero: -273.15°C)
    // Convert to Fahrenheit
}

// Parse hex color code
fn parse_hex_color(s: &str) -> Result<(u8, u8, u8), String> {
    // Parse "#RRGGBB" format
    // Return (r, g, b) tuple
    // Handle errors: missing #, wrong length, invalid hex
}
```

**Hints:**
- Use `s.to_lowercase()` for case-insensitive comparison
- `.trim_start_matches('#')` removes prefix
- Parse hex: `u8::from_str_radix(s, 16)`
- Return specific error messages for each failure mode

## Exercise 8: Error Recovery

Practice recovering from errors with fallback values.

**Requirements:**

```rust
// Parse with default
fn parse_or_default(s: &str, default: i32) -> i32 {
    // Try to parse, return default if it fails
    // Use .unwrap_or(default)
}

// Parse with fallback
fn parse_or_compute(s: &str) -> i32 {
    // Try to parse
    // If fails, return length of string as fallback
    // Use .unwrap_or_else()
}

// Try multiple parsing strategies
fn flexible_parse(s: &str) -> Result<i32, String> {
    // Try parsing as decimal
    // If that fails, try parsing as hex (with "0x" prefix)
    // If that fails, try parsing as binary (with "0b" prefix)
    // Return error if all fail
}

// Get value or ask user (simulated)
fn get_required_value(key: &str, map: &[(String, String)]) -> Result<String, String> {
    // Find key in map
    // If not found, return error asking user to provide it
}
```

**Hints:**
- `.unwrap_or(default)` provides a fallback value
- `.unwrap_or_else(|| compute())` computes fallback lazily
- `.or_else()` tries alternative operations
- Chain multiple attempts with `.or()`

## Exercise 9: Transaction Processing

Build a simple transaction processor with rollback on errors.

**Requirements:**

```rust
// Define Transaction
enum Transaction {
    Deposit { account: String, amount: f64 },
    Withdraw { account: String, amount: f64 },
    Transfer { from: String, to: String, amount: f64 },
}

// Define TransactionError
enum TransactionError {
    InsufficientFunds,
    AccountNotFound(String),
    InvalidAmount,
    TransferToSelf,
}

// Process single transaction
fn process_transaction(
    transaction: &Transaction,
    balances: &mut [(String, f64)],
) -> Result<(), TransactionError> {
    // Process the transaction
    // Update balances
    // Return errors for invalid operations
}

// Process batch of transactions
fn process_batch(
    transactions: &[Transaction],
    balances: &mut [(String, f64)],
) -> Result<usize, TransactionError> {
    // Process all transactions
    // Return number processed
    // Stop and return error on first failure
}
```

**Example:**
```rust
let mut balances = vec![
    (String::from("Alice"), 100.0),
    (String::from("Bob"), 50.0),
];

let transaction = Transaction::Transfer {
    from: String::from("Alice"),
    to: String::from("Bob"),
    amount: 30.0,
};
```

**Hints:**
- Find account in slice: `balances.iter_mut().find(|(name, _)| name == account)`
- Validate before mutating
- Use `?` to stop batch processing on error
- Check for negative amounts and self-transfers

## Exercise 10: Comprehensive Error Handling System

Build a file metadata parser with comprehensive error handling.

**Requirements:**

```rust
// Define multiple error types
enum ParseError {
    EmptyInput,
    InvalidFormat,
    MissingField(String),
}

enum ValidationError {
    SizeTooLarge(u64),
    InvalidExtension(String),
    NameTooLong,
}

enum FileError {
    Parse(ParseError),
    Validation(ValidationError),
}

// Define FileMetadata
struct FileMetadata {
    name: String,
    extension: String,
    size_bytes: u64,
}

// Parse file info from string
fn parse_file_info(s: &str) -> Result<FileMetadata, ParseError> {
    // Parse format: "filename.ext:1024"
    // Extract name, extension, size
}

// Validate file metadata
fn validate_file(metadata: &FileMetadata) -> Result<(), ValidationError> {
    // Check size < 1GB
    // Check extension is in [txt, pdf, jpg, png]
    // Check name length < 255
}

// Full processing pipeline
fn process_file_string(s: &str) -> Result<FileMetadata, FileError> {
    // Parse the string
    // Validate the result
    // Convert errors to FileError
}

// Helper to print all error types
fn describe_error(error: &FileError) {
    // Match on error type and variant
    // Print descriptive message
}
```

**Example input:** `"document.pdf:524288"`

**Hints:**
- Nest enum variants: `FileError::Parse(ParseError::EmptyInput)`
- Convert between error types: `.map_err(|e| FileError::Parse(e))`
- Use `?` with error conversion
- Match nested enums: `FileError::Parse(ParseError::EmptyInput) => ...`

## Testing Your Solutions

For each exercise:

1. Test with valid inputs (happy path)
2. Test with various error cases
3. Ensure error messages are descriptive
4. Use `cargo clippy` to check for improvements
5. Practice both `match` and `?` operator
6. Try `unwrap()` to see panic messages (then handle properly!)

## Common Patterns

**Early return with ?:**
```rust
fn process() -> Result<i32, String> {
    let x = parse_input()?;
    let y = validate(x)?;
    Ok(y * 2)
}
```

**Match for specific handling:**
```rust
match dangerous_operation() {
    Ok(value) => process(value),
    Err(RecoverableError) => use_fallback(),
    Err(FatalError) => return Err(FatalError),
}
```

**Converting error types:**
```rust
some_result.map_err(|e| CustomError::from(e))?
```

## Debugging Tips

When working with Results:

1. **Use meaningful error messages**: "Parse failed" is not helpful
2. **Don't overuse unwrap()**: It panics on error
3. **Use expect() during prototyping**: Better panic message
4. **Prefer ? over manual matching**: Cleaner code
5. **Create custom error types**: Better than String errors
6. **Handle errors at appropriate level**: Don't propagate everything

## What You're Practicing

- ✅ Working with `Result<T, E>`
- ✅ Using the `?` operator for propagation
- ✅ Creating custom error types
- ✅ Pattern matching on errors
- ✅ Converting between error types
- ✅ Error recovery strategies
- ✅ Handling multiple failure modes
- ✅ Building robust, production-ready code

**Master error handling!** It's what separates toy code from production code.

When ready, continue to [Week 5 Theory](../theory/week5-collections.md)

---

[← Theory](../theory/week4-errors.md) | [Home](../README.md) | [Next: Week 5 Theory →](../theory/week5-collections.md)
