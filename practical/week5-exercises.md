# Week 5: Collections & Iterators Exercises

These exercises focus on Rust's powerful collection types and the iterator pattern.

## Exercise 1: Word Frequency Counter

Create a program that counts word frequencies in a text.

**Requirements:**

```rust
use std::collections::HashMap;

fn count_words(text: &str) -> HashMap<String, u32> {
    // Return a HashMap with word counts
    // Convert all words to lowercase
    // Remove punctuation
}

fn most_common_word(counts: &HashMap<String, u32>) -> Option<(&String, &u32)> {
    // Return the word with highest count
}
```

**Test text:** `"The quick brown fox jumps over the lazy dog. The dog was really lazy!"`

**Example output:**
```
Word frequencies:
the: 3
dog: 2
lazy: 2
quick: 1
brown: 1
...

Most common word: "the" (3 times)
```

**Hints:**
- Use `.split_whitespace()` to split into words
- Use `.to_lowercase()` for case-insensitive counting
- Use `.trim_matches()` to remove punctuation
- HashMap methods: `.entry()` and `.or_insert()`
- Find max with `.iter().max_by_key()`

## Exercise 2: Student Gradebook with HashMap

Build a gradebook system using HashMaps.

**Requirements:**

```rust
use std::collections::HashMap;

struct Gradebook {
    // Student name -> HashMap of (assignment name -> grade)
    grades: HashMap<String, HashMap<String, f64>>,
}

impl Gradebook {
    fn new() -> Self {
        // Create empty gradebook
    }
    
    fn add_grade(&mut self, student: &str, assignment: &str, grade: f64) {
        // Add a grade for a student
    }
    
    fn student_average(&self, student: &str) -> Option<f64> {
        // Calculate average for one student
    }
    
    fn assignment_average(&self, assignment: &str) -> Option<f64> {
        // Calculate average for one assignment across all students
    }
    
    fn top_student(&self) -> Option<String> {
        // Return name of student with highest average
    }
}
```

**Example usage:**
```rust
let mut gradebook = Gradebook::new();
gradebook.add_grade("Alice", "Midterm", 95.0);
gradebook.add_grade("Alice", "Final", 92.0);
gradebook.add_grade("Bob", "Midterm", 87.0);
gradebook.add_grade("Bob", "Final", 89.0);
```

**Hints:**
- Nested HashMaps: `HashMap<K, HashMap<K2, V>>`
- Use `.entry()` and `.or_insert_with()` for nested maps
- Return `Option` for operations that might fail
- Calculate averages by summing and dividing by count

## Exercise 3: Unique Elements with HashSet

Work with sets to find unique and common elements.

**Requirements:**

```rust
use std::collections::HashSet;

fn unique_elements(vec: &[i32]) -> HashSet<i32> {
    // Return set of unique elements
}

fn intersection(set1: &HashSet<i32>, set2: &HashSet<i32>) -> HashSet<i32> {
    // Return elements common to both sets
}

fn union(set1: &HashSet<i32>, set2: &HashSet<i32>) -> HashSet<i32> {
    // Return all unique elements from both sets
}

fn difference(set1: &HashSet<i32>, set2: &HashSet<i32>) -> HashSet<i32> {
    // Return elements in set1 but not in set2
}

fn is_subset(set1: &HashSet<i32>, set2: &HashSet<i32>) -> bool {
    // Check if set1 is a subset of set2
}
```

**Test with:**
```rust
let vec1 = vec![1, 2, 3, 4, 5, 5, 4];
let vec2 = vec![4, 5, 6, 7, 8];
```

**Hints:**
- Create HashSet: `.iter().cloned().collect()`
- Built-in methods: `.intersection()`, `.union()`, `.difference()`
- These return iterators, need to collect into HashSet
- `.is_subset()` method exists

## Exercise 4: Iterator Transformations

Practice iterator methods for data processing.

**Requirements:**

```rust
fn sum_of_squares(nums: &[i32]) -> i32 {
    // Use iterators: square each number, then sum
}

fn filter_and_map(nums: &[i32]) -> Vec<i32> {
    // Keep only even numbers, then multiply by 10
}

fn find_first_over(nums: &[i32], threshold: i32) -> Option<i32> {
    // Find first number greater than threshold
}

fn partition_by_sign(nums: &[i32]) -> (Vec<i32>, Vec<i32>) {
    // Return (positive numbers, negative numbers)
    // Zero counts as positive
}

fn chain_and_dedupe(vec1: &[i32], vec2: &[i32]) -> Vec<i32> {
    // Combine both vectors and remove duplicates
    // Maintain order of first occurrence
}
```

**Test data:** `[1, -2, 3, -4, 5, -6, 7, 8]`

**Hints:**
- Chain methods: `.iter().map().filter().collect()`
- `.find()` returns first match
- `.partition()` splits based on predicate
- Use HashSet to track seen elements for deduplication

## Exercise 5: Data Pipeline Processing

Build a data processing pipeline using iterators.

**Requirements:**

Process a list of transactions:

```rust
#[derive(Debug, Clone)]
struct Transaction {
    id: u32,
    amount: f64,
    category: String,
    is_refund: bool,
}

fn total_by_category(transactions: &[Transaction]) -> HashMap<String, f64> {
    // Sum amounts grouped by category
    // Subtract refunds
}

fn expensive_transactions(transactions: &[Transaction], threshold: f64) -> Vec<&Transaction> {
    // Return non-refund transactions above threshold
    // Sorted by amount descending
}

fn category_count(transactions: &[Transaction]) -> HashMap<String, usize> {
    // Count number of transactions per category
}

fn average_transaction_amount(transactions: &[Transaction]) -> f64 {
    // Calculate average of non-refund transactions
}
```

**Hints:**
- Use `.filter()` to exclude refunds
- Use `.fold()` or `.sum()` for aggregation
- Use `.sort_by()` or `.sort_by_key()` for sorting
- Group with HashMap using `.entry()` API

## Exercise 6: Vec Operations and Algorithms

Implement various vector operations.

**Requirements:**

```rust
fn remove_duplicates(vec: Vec<i32>) -> Vec<i32> {
    // Remove duplicates while preserving order
}

fn rotate_left(vec: &mut Vec<i32>, positions: usize) {
    // Rotate vector left by n positions
    // [1,2,3,4,5] rotated left by 2 -> [3,4,5,1,2]
}

fn merge_sorted(vec1: &[i32], vec2: &[i32]) -> Vec<i32> {
    // Merge two sorted vectors into one sorted vector
}

fn sliding_window_max(vec: &[i32], window_size: usize) -> Vec<i32> {
    // Return max of each sliding window
    // [1,3,2,5,4] with window 3 -> [3,5,5]
}

fn median(vec: &[i32]) -> f64 {
    // Calculate median (middle value when sorted)
    // For even length, average the two middle values
}
```

**Hints:**
- Use HashSet to track seen elements
- `.rotate_left()` method exists on slices
- Merge with two pointers approach
- `.windows()` creates sliding windows
- Sort a copy for median calculation

## Exercise 7: Nested Collections

Work with complex nested data structures.

**Requirements:**

```rust
// Represent a university with departments and students
use std::collections::HashMap;

type StudentId = u32;
type DepartmentName = String;

struct University {
    // Department -> Vec of student IDs
    departments: HashMap<DepartmentName, Vec<StudentId>>,
    // Student ID -> Student info
    students: HashMap<StudentId, Student>,
}

struct Student {
    id: StudentId,
    name: String,
    gpa: f64,
}

impl University {
    fn new() -> Self {
        // Create empty university
    }
    
    fn add_student(&mut self, student: Student, department: &str) {
        // Add student to department and student map
    }
    
    fn department_average_gpa(&self, department: &str) -> Option<f64> {
        // Calculate average GPA for department
    }
    
    fn top_students(&self, n: usize) -> Vec<&Student> {
        // Return top N students by GPA across all departments
    }
    
    fn transfer_student(&mut self, student_id: StudentId, from: &str, to: &str) -> Result<(), String> {
        // Move student between departments
        // Return error if student not found or invalid department
    }
}
```

**Hints:**
- Maintain data in two places for efficient lookup
- Use `.remove()` and `.push()` for transfers
- Sort students by GPA with `.sort_by()`
- Use `.take()` to limit results

## Exercise 8: Iterator Adapters and Custom Iteration

Create custom iterator patterns.

**Requirements:**

```rust
fn fizz_buzz_iterator(n: usize) -> Vec<String> {
    // Use iterator methods to generate FizzBuzz
    // (1..=n) -> map to FizzBuzz strings
}

fn running_sum(nums: &[i32]) -> Vec<i32> {
    // Return running sum: [1,2,3,4] -> [1,3,6,10]
    // Use .scan() iterator adapter
}

fn pairwise_sum(nums: &[i32]) -> Vec<i32> {
    // Sum consecutive pairs: [1,2,3,4] -> [3,7]
    // Use .windows(2)
}

fn flatten_nested(nested: Vec<Vec<i32>>) -> Vec<i32> {
    // Flatten nested vector: [[1,2],[3,4]] -> [1,2,3,4]
}

fn group_consecutive(nums: &[i32]) -> Vec<Vec<i32>> {
    // Group consecutive numbers: [1,2,3,5,6,8] -> [[1,2,3],[5,6],[8]]
}
```

**Hints:**
- `.scan()` maintains state across iterations
- `.windows(2)` gives overlapping pairs
- `.flatten()` or `.flat_map()` for flattening
- Use `.fold()` to build groups

## Exercise 9: Text Processing Pipeline

Build a text analyzer using collections and iterators.

**Requirements:**

```rust
use std::collections::{HashMap, HashSet};

struct TextAnalyzer {
    text: String,
}

impl TextAnalyzer {
    fn new(text: String) -> Self {
        Self { text }
    }
    
    fn word_count(&self) -> usize {
        // Total number of words
    }
    
    fn unique_words(&self) -> HashSet<String> {
        // Set of unique words (case-insensitive)
    }
    
    fn longest_words(&self, n: usize) -> Vec<String> {
        // Return n longest words
    }
    
    fn words_starting_with(&self, prefix: &str) -> Vec<String> {
        // Find all words starting with prefix
    }
    
    fn bigrams(&self) -> HashMap<(String, String), usize> {
        // Count consecutive word pairs
        // "the quick brown fox" -> {("the","quick"): 1, ("quick","brown"): 1, ...}
    }
    
    fn palindrome_words(&self) -> Vec<String> {
        // Find all palindrome words (same forwards and backwards)
    }
}
```

**Hints:**
- Split, normalize, and collect words once
- Use `.chars().rev()` to reverse for palindrome check
- `.windows(2)` on word vector for bigrams
- `.filter()` and `.starts_with()` for prefix search

## Exercise 10: Performance-Critical Data Processing

Implement efficient data processing algorithms.

**Requirements:**

```rust
// Process large datasets efficiently
fn find_duplicates(nums: &[i32]) -> Vec<i32> {
    // Find all numbers that appear more than once
    // Return in ascending order
    // Optimize for large inputs
}

fn k_most_frequent(nums: &[i32], k: usize) -> Vec<i32> {
    // Return k most frequent elements
    // Use HashMap for counting
}

fn intersection_of_arrays(arrays: &[Vec<i32>]) -> Vec<i32> {
    // Find elements common to all arrays
    // Return sorted
}

fn remove_if_exists(vec: &mut Vec<i32>, items: &HashSet<i32>) {
    // Remove all elements that exist in the set
    // Do it efficiently (in-place if possible)
}

fn group_anagrams(words: Vec<String>) -> Vec<Vec<String>> {
    // Group words that are anagrams
    // ["eat","tea","tan","ate","nat","bat"] 
    // -> [["eat","tea","ate"],["tan","nat"],["bat"]]
}
```

**Test data:**
```rust
let nums = vec![1,2,2,3,3,3,4,4,4,4,5];
let words = vec!["eat","tea","tan","ate","nat","bat"];
```

**Hints:**
- Use HashMap to count frequencies
- Sort character vector for anagram detection
- `.retain()` for efficient in-place filtering
- HashSet for intersection (convert arrays to sets)
- Sort with `.sort_by_key()` for frequency sorting

## Testing Your Solutions

For each exercise:

1. Create a new project: `cargo new exercise_name`
2. Implement the required functions
3. Test with various inputs in `main()`
4. Check edge cases: empty collections, single elements
5. Run `cargo clippy` for optimization suggestions

## Common Iterator Methods Reference

- `.map()` - Transform each element
- `.filter()` - Keep elements matching predicate
- `.fold()` / `.reduce()` - Combine elements
- `.collect()` - Gather into collection
- `.chain()` - Concatenate iterators
- `.zip()` - Combine two iterators
- `.enumerate()` - Add indices
- `.skip()` / `.take()` - Limit elements
- `.find()` - First matching element
- `.any()` / `.all()` - Boolean tests
- `.partition()` - Split into two groups
- `.windows()` - Sliding windows
- `.chunks()` - Non-overlapping groups

## What You're Practicing

These exercises help you master:
- ✅ Vec operations and algorithms
- ✅ HashMap for key-value storage and counting
- ✅ HashSet for uniqueness and set operations
- ✅ Iterator methods and chaining
- ✅ Functional programming patterns
- ✅ Data transformation pipelines
- ✅ Collection performance considerations
- ✅ Working with complex nested data structures

**When you're done**, move on to [Week 6 Theory: Traits and Generics](../theory/week6-traits-generics.md)!

---

[← Theory](../theory/week5-collections-iterators.md) | [Home](../README.md) | [Next: Week 6 Theory →](../theory/week6-traits-generics.md)
