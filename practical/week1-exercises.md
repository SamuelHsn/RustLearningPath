# Week 1: Practical Exercises

These exercises help you practice Rust fundamentals. **Remember**: No solutions are provided - work through them yourself!

## Setup

For each exercise, create a new Rust project:

```bash
cargo new exercise_name
cd exercise_name
# Edit src/main.rs
cargo run  # To test your solution
```

## Exercise 1: Temperature Converter

Create a program that converts temperatures between Celsius and Fahrenheit.

**Requirements:**
- Create two functions: `celsius_to_fahrenheit` and `fahrenheit_to_celsius`
- Formula: F = (C × 9/5) + 32
- Formula: C = (F - 32) × 5/9
- Test with several values in `main()`

**Example output:**
```
0°C = 32°F
100°C = 212°F
32°F = 0°C
212°F = 100°C
```

**Hints:**
- Use `f64` for floating-point numbers
- Remember function syntax: `fn name(param: Type) -> ReturnType`
- Use `println!("{}°C = {}°F", celsius, result);` for formatting

## Exercise 2: Fibonacci Sequence

Write a program that prints the first N Fibonacci numbers.

**Requirements:**
- Accept N as a variable in your code (we'll learn user input later)
- Use a loop to calculate and print Fibonacci numbers
- The sequence starts: 0, 1, 1, 2, 3, 5, 8, 13...

**Example for N=10:**
```
0
1
1
2
3
5
8
13
21
34
```

**Hints:**
- You'll need two mutable variables to track the last two numbers
- Use a `for` loop with a range: `for _ in 0..n`
- Think about how to update the two tracking variables each iteration

**Challenge:** Can you also create a function that returns the Nth Fibonacci number?

## Exercise 3: Is Prime?

Write a function that determines if a number is prime.

**Requirements:**
- Function signature: `fn is_prime(n: u32) -> bool`
- Test it with several numbers
- Print results for numbers 1 through 30

**Example output:**
```
1 is not prime
2 is prime
3 is prime
4 is not prime
5 is prime
...
```

**Hints:**
- A prime number is only divisible by 1 and itself
- You only need to check divisors up to the square root of n
- Use `n % divisor == 0` to check divisibility
- Think about edge cases: What about 1? What about 2?

## Exercise 4: Array Statistics

Write functions to calculate statistics on an array of integers.

**Requirements:**
- Create a function to find the maximum value
- Create a function to find the minimum value
- Create a function to calculate the sum
- Create a function to calculate the average
- Test with the array: `[12, 45, 7, 23, 56, 89, 34]`

**Function signatures:**
```rust
fn find_max(arr: &[i32]) -> i32
fn find_min(arr: &[i32]) -> i32
fn calculate_sum(arr: &[i32]) -> i32
fn calculate_average(arr: &[i32]) -> f64
```

**Example output:**
```
Array: [12, 45, 7, 23, 56, 89, 34]
Max: 89
Min: 7
Sum: 266
Average: 38.0
```

**Hints:**
- Use a `for` loop to iterate: `for &element in arr`
- For max/min, start with the first element
- For average, convert sum to `f64` using `as f64`
- Array length: `arr.len()`

**Challenge:** What happens with an empty array? How would you handle that?

## Exercise 5: FizzBuzz

The classic programming exercise!

**Requirements:**
- Print numbers from 1 to 100
- For multiples of 3, print "Fizz" instead
- For multiples of 5, print "Buzz" instead
- For multiples of both 3 and 5, print "FizzBuzz"

**Example output:**
```
1
2
Fizz
4
Buzz
Fizz
7
8
Fizz
Buzz
11
Fizz
13
14
FizzBuzz
...
```

**Hints:**
- Use `for number in 1..=100`
- Check divisibility: `number % 3 == 0`
- Think about the order of your conditions
- Remember: if must have `bool` conditions, not truthy values

## Exercise 6: Pattern Printer

Create a program that prints different patterns using nested loops.

**Requirements:**

1. Print a right triangle:
```
*
**
***
****
*****
```

2. Print a square:
```
*****
*****
*****
*****
*****
```

3. Print a pyramid:
```
    *
   ***
  *****
 *******
*********
```

**Hints:**
- Use nested `for` loops
- `print!()` doesn't add a newline, `println!()` does
- For the pyramid, think about spaces and stars separately
- You might want to use `print!(" ")` and `print!("*")`

## Exercise 7: Sum of Digits

Write a function that calculates the sum of digits in a number.

**Requirements:**
- Function signature: `fn sum_of_digits(n: u32) -> u32`
- Example: sum_of_digits(1234) should return 10 (1+2+3+4)
- Test with several numbers

**Example output:**
```
Sum of digits of 1234 is 10
Sum of digits of 5678 is 26
Sum of digits of 999 is 27
```

**Hints:**
- Use modulo (`%`) to get the last digit: `n % 10`
- Use integer division (`/`) to remove the last digit: `n / 10`
- Use a `while` loop: keep going while n > 0
- You'll need a mutable variable to accumulate the sum

## Exercise 8: Guessing Game (Without Input)

Since we haven't learned user input yet, create a simpler version.

**Requirements:**
- Generate a "secret number" (just hardcode it: `let secret = 42;`)
- Create an array of guesses: `[10, 50, 42, 30]`
- For each guess, print whether it's too high, too low, or correct
- Stop when the correct number is guessed

**Example output:**
```
Guess 10: Too low!
Guess 50: Too high!
Guess 42: Correct!
Game over!
```

**Hints:**
- Use a `for` loop to iterate through guesses
- Use `if/else if/else` to compare
- Use `break` to exit the loop when correct

**Challenge:** Can you count how many guesses it took?

## Exercise 9: Character Counter

Write a function that counts different types of characters in a string.

**Requirements:**
- Count uppercase letters
- Count lowercase letters
- Count digits
- Count spaces
- Count other characters

**Function signature:**
```rust
fn analyze_string(s: &str) {
    // Print the counts
}
```

**Test string:** `"Hello World 123!"`

**Example output:**
```
String: "Hello World 123!"
Uppercase: 2
Lowercase: 8
Digits: 3
Spaces: 2
Others: 1
```

**Hints:**
- Iterate over characters: `for c in s.chars()`
- Check if uppercase: `c.is_uppercase()`
- Check if lowercase: `c.is_lowercase()`
- Check if digit: `c.is_digit(10)`
- Check if space: `c == ' '`

## Exercise 10: Array Rotation

Write a function that rotates an array to the right by N positions.

**Requirements:**
- Create an array: `[1, 2, 3, 4, 5]`
- Rotate it by 2 positions
- Result should be: `[4, 5, 1, 2, 3]`
- Print the original and rotated array

**Hints:**
- You'll need to create a new array for the result
- Think about the indices: where does each element move?
- Use modulo to wrap around: `(index + n) % length`
- Array syntax: `let result = [0; 5];` creates array of 5 zeros

**Challenge:** Can you do it in-place without creating a new array? (This is much harder!)

## Testing Your Solutions

For each exercise:

1. Make sure your code compiles: `cargo build`
2. Run your program: `cargo run`
3. Check the output matches your expectations
4. Try edge cases (empty arrays, zero, negative numbers where applicable)
5. Run `cargo clippy` to check for common mistakes

## Tips for Success

- **Read compiler errors carefully**: Rust's compiler gives helpful messages
- **Start simple**: Get basic functionality working first
- **Test incrementally**: Don't write everything at once
- **Use `println!` for debugging**: Print intermediate values
- **Experiment**: Try different approaches if stuck

## What You're Practicing

These exercises help you master:
- ✅ Variable declarations (`let`, `let mut`)
- ✅ Functions with parameters and return types
- ✅ Integer and floating-point types
- ✅ Control flow (`if`, `loop`, `while`, `for`)
- ✅ Arrays and indexing
- ✅ Basic string operations
- ✅ Type conversions
- ✅ Formatting with `println!`

**When you're done**, move on to [Week 2 Theory: Ownership](../theory/week2-ownership.md) - the most important concept in Rust!

---

[← Theory](../theory/week1-fundamentals.md) | [Home](../README.md) | [Next: Week 2 Theory →](../theory/week2-ownership.md)
