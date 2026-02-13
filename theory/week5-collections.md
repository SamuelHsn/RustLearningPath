# Week 5: Collections and Iterators

## Learning Objectives

By the end of this week, you will:
- Master Rust's standard collections: Vec, HashMap, HashSet
- Understand when to use each collection type
- Use iterators and iterator adapters effectively
- Compare Rust collections to those in TypeScript, Java, and C#

## Why Collections?

Arrays are great for fixed-size data, but most programs need dynamic collections. Rust's standard library provides powerful, efficient collections with memory safety guarantees.

**Key difference from Week 1 arrays:** Collections grow and shrink at runtime.

## 1. Vec<T> - Dynamic Arrays

`Vec<T>` (vector) is Rust's growable array type, similar to ArrayList in Java, List<T> in C#, or arrays in TypeScript.

### Creating Vectors

```rust
// Creating empty vector
let v: Vec<i32> = Vec::new();

// Using vec! macro (more common)
let v = vec![1, 2, 3, 4, 5];

// With capacity (performance optimization)
let mut v = Vec::with_capacity(10);
```

**Comparison:**
```typescript
// TypeScript
const v: number[] = [1, 2, 3, 4, 5];
const v = new Array<number>();
```

```java
// Java
ArrayList<Integer> v = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));
```

```csharp
// C#
var v = new List<int> { 1, 2, 3, 4, 5 };
```

### Updating Vectors

```rust
let mut v = Vec::new();

v.push(5);    // Add to end
v.push(6);
v.push(7);

let last = v.pop();  // Remove and return last: Some(7)
```

**Important:** Vector must be `mut` to modify!

### Reading Elements

```rust
let v = vec![1, 2, 3, 4, 5];

// Method 1: Indexing (panics if out of bounds)
let third = v[2];  // 3

// Method 2: get() method (returns Option)
let third = v.get(2);  // Some(&3)
let sixth = v.get(5);  // None (no panic!)

match v.get(2) {
    Some(third) => println!("Third element: {}", third),
    None => println!("No third element"),
}
```

**Key difference:** `get()` is safer - it returns `Option<&T>` instead of panicking.

### Iterating Over Vectors

```rust
let v = vec![100, 32, 57];

// Immutable iteration
for i in &v {
    println!("{}", i);
}

// Mutable iteration
let mut v = vec![100, 32, 57];
for i in &mut v {
    *i += 50;  // Dereference to modify
}

// Taking ownership
for i in v {
    println!("{}", i);
}  // v is moved, can't use after this
```

**Comparison:**
```typescript
// TypeScript - all modify original
v.forEach(i => console.log(i));
for (const i of v) { }
```

```java
// Java
for (int i : v) { }
v.forEach(i -> System.out.println(i));
```

### Vectors with Enums

Vectors can only store one type, but enums let you store different types:

```rust
enum SpreadsheetCell {
    Int(i32),
    Float(f64),
    Text(String),
}

let row = vec![
    SpreadsheetCell::Int(3),
    SpreadsheetCell::Text(String::from("blue")),
    SpreadsheetCell::Float(10.12),
];
```

**Pattern:** Use enums to store heterogeneous data in Vec.

### Common Vec Methods

```rust
let mut v = vec![1, 2, 3, 4, 5];

v.len();           // Length: 5
v.is_empty();      // false
v.clear();         // Remove all elements
v.contains(&3);    // true if contains 3
v.insert(2, 99);   // Insert 99 at index 2
v.remove(2);       // Remove element at index 2
v.first();         // Option<&T> - first element
v.last();          // Option<&T> - last element
```

## 2. HashMap<K, V> - Key-Value Store

HashMap stores key-value pairs, like Map in TypeScript/Java or Dictionary in C#.

### Creating HashMaps

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);

// From vectors
let teams = vec![String::from("Blue"), String::from("Yellow")];
let initial_scores = vec![10, 50];

let scores: HashMap<_, _> = teams.into_iter()
    .zip(initial_scores.into_iter())
    .collect();
```

**Comparison:**
```typescript
// TypeScript
const scores = new Map([
    ["Blue", 10],
    ["Yellow", 50]
]);
```

```java
// Java
Map<String, Integer> scores = new HashMap<>();
scores.put("Blue", 10);
```

```csharp
// C#
var scores = new Dictionary<string, int> {
    ["Blue"] = 10,
    ["Yellow"] = 50
};
```

### Ownership and HashMap

```rust
let field_name = String::from("Favorite color");
let field_value = String::from("Blue");

let mut map = HashMap::new();
map.insert(field_name, field_value);
// field_name and field_value are moved, no longer valid!

// For types that implement Copy (like i32), values are copied
let x = 5;
map.insert("key", x);
println!("{}", x);  // ✅ Still valid
```

### Accessing Values

```rust
let mut scores = HashMap::new();
scores.insert(String::from("Blue"), 10);

// get() returns Option<&V>
let team_name = String::from("Blue");
let score = scores.get(&team_name);  // Some(&10)

match score {
    Some(s) => println!("Score: {}", s),
    None => println!("Team not found"),
}

// Using copied() and unwrap_or()
let score = scores.get(&team_name).copied().unwrap_or(0);
```

### Iterating Over HashMap

```rust
for (key, value) in &scores {
    println!("{}: {}", key, value);
}
```

**Note:** HashMap iteration order is not guaranteed (like most hash maps).

### Updating HashMap

```rust
let mut scores = HashMap::new();

// Overwrite value
scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Blue"), 25);  // Blue is now 25

// Only insert if key doesn't exist
scores.entry(String::from("Yellow")).or_insert(50);
scores.entry(String::from("Yellow")).or_insert(100);  // Yellow stays 50

// Update based on old value
let text = "hello world wonderful world";
let mut map = HashMap::new();

for word in text.split_whitespace() {
    let count = map.entry(word).or_insert(0);
    *count += 1;  // Dereference to modify
}
// map: {"hello": 1, "world": 2, "wonderful": 1}
```

**The `entry` API** is powerful and idiomatic - learn it well!

### Common HashMap Methods

```rust
let mut map = HashMap::new();
map.insert("key", "value");

map.get("key");          // Option<&V>
map.contains_key("key"); // bool
map.remove("key");       // Option<V> - returns old value
map.len();               // usize
map.is_empty();          // bool
map.clear();             // Remove all
```

## 3. HashSet<T> - Unique Values

HashSet stores unique values, like Set in TypeScript/Java or HashSet in C#.

```rust
use std::collections::HashSet;

let mut books = HashSet::new();

books.insert("A Game of Thrones");
books.insert("The Hobbit");
books.insert("A Game of Thrones");  // Duplicate - not added

println!("Count: {}", books.len());  // 2

// Check membership
if books.contains("The Hobbit") {
    println!("Found!");
}
```

**Comparison:**
```typescript
// TypeScript
const books = new Set(["A Game of Thrones", "The Hobbit"]);
```

```java
// Java
Set<String> books = new HashSet<>();
books.add("A Game of Thrones");
```

### Set Operations

```rust
let a: HashSet<_> = [1, 2, 3, 4].iter().cloned().collect();
let b: HashSet<_> = [3, 4, 5, 6].iter().cloned().collect();

// Union: {1, 2, 3, 4, 5, 6}
for x in a.union(&b) {
    println!("{}", x);
}

// Intersection: {3, 4}
for x in a.intersection(&b) {
    println!("{}", x);
}

// Difference: {1, 2}
for x in a.difference(&b) {
    println!("{}", x);
}

// Symmetric difference: {1, 2, 5, 6}
for x in a.symmetric_difference(&b) {
    println!("{}", x);
}
```

## 4. Iterators - The Rust Way

Iterators are central to idiomatic Rust code. They're lazy, efficient, and expressive.

### Creating Iterators

```rust
let v1 = vec![1, 2, 3];

// iter() - iterates over &T
let v1_iter = v1.iter();

// iter_mut() - iterates over &mut T
let mut v2 = vec![1, 2, 3];
let v2_iter = v2.iter_mut();

// into_iter() - takes ownership, iterates over T
let v3 = vec![1, 2, 3];
let v3_iter = v3.into_iter();
```

### The Iterator Trait

```rust
pub trait Iterator {
    type Item;
    
    fn next(&mut self) -> Option<Self::Item>;
    // ... many provided methods
}
```

All iterators implement this trait.

### Consuming Iterators

```rust
let v1 = vec![1, 2, 3];

// sum() consumes the iterator
let total: i32 = v1.iter().sum();

// collect() turns iterator into collection
let v2: Vec<i32> = v1.iter().cloned().collect();

// for loop consumes iterator
for val in v1.iter() {
    println!("{}", val);
}
```

### Iterator Adapters

Iterator adapters create new iterators from existing ones. They're **lazy** - nothing happens until consumed!

#### map()

```rust
let v1 = vec![1, 2, 3];
let v2: Vec<i32> = v1.iter().map(|x| x + 1).collect();
// v2: [2, 3, 4]
```

**Comparison:**
```typescript
// TypeScript
const v2 = v1.map(x => x + 1);
```

```java
// Java
List<Integer> v2 = v1.stream()
    .map(x -> x + 1)
    .collect(Collectors.toList());
```

#### filter()

```rust
let v1 = vec![1, 2, 3, 4];
let v2: Vec<i32> = v1.into_iter().filter(|x| x % 2 == 0).collect();
// v2: [2, 4]
```

#### Chain Multiple Adapters

```rust
let v1 = vec![1, 2, 3, 4, 5];

let result: Vec<i32> = v1.iter()
    .filter(|x| *x % 2 == 0)     // [2, 4]
    .map(|x| x * 2)               // [4, 8]
    .collect();

println!("{:?}", result);  // [4, 8]
```

**This is idiomatic Rust!** Chainable, readable, efficient.

### Common Iterator Methods

```rust
let v = vec![1, 2, 3, 4, 5];

// Transforming
v.iter().map(|x| x * 2);
v.iter().filter(|x| *x > 2);
v.iter().take(3);          // First 3 elements
v.iter().skip(2);          // Skip first 2
v.iter().enumerate();      // (index, value) pairs
v.iter().zip(other);       // Pair with another iterator

// Aggregating
v.iter().sum();            // Sum all
v.iter().product();        // Product of all
v.iter().min();            // Minimum
v.iter().max();            // Maximum
v.iter().count();          // Count elements
v.iter().any(|x| *x > 3);  // true if any match
v.iter().all(|x| *x > 0);  // true if all match
v.iter().find(|x| *x > 3); // First match
```

### Iterator Example: Word Count

```rust
use std::collections::HashMap;

fn word_count(text: &str) -> HashMap<&str, usize> {
    text.split_whitespace()
        .fold(HashMap::new(), |mut map, word| {
            *map.entry(word).or_insert(0) += 1;
            map
        })
}

let text = "hello world hello rust world";
let counts = word_count(text);
// {"hello": 2, "world": 2, "rust": 1}
```

**Pattern:** Iterators + fold/collect = elegant solutions.

## Performance Considerations

### Vec Performance

```rust
// Pre-allocate if you know size
let mut v = Vec::with_capacity(1000);  // Avoids reallocations

// Amortized O(1) push
v.push(1);

// O(1) index access
let x = v[0];
```

**Like:** ArrayList in Java, List<T> in C#, arrays in TypeScript

### HashMap Performance

- Average O(1) insert/lookup (like all hash maps)
- Worst case O(n) (hash collision)
- No guaranteed ordering

**Alternatives:**
- `BTreeMap` - Sorted keys, O(log n) operations
- `IndexMap` - Preserves insertion order (external crate)

### Iterator Performance

**Zero-cost abstraction:** Iterator chains are as fast as hand-written loops!

```rust
// These compile to the same assembly
let sum: i32 = v.iter().filter(|x| *x % 2 == 0).sum();

let mut sum = 0;
for x in &v {
    if x % 2 == 0 {
        sum += x;
    }
}
```

Rust inlines and optimizes iterator chains. Use them freely!

## When to Use Each Collection

| Collection | Use When | Similar To |
|------------|----------|------------|
| `Vec<T>` | Sequential data, indexed access | Array (TS), ArrayList (Java), List (C#) |
| `HashMap<K, V>` | Key-value lookup | Map (TS), HashMap (Java), Dictionary (C#) |
| `HashSet<T>` | Unique values, membership testing | Set (TS/Java/C#) |
| `VecDeque<T>` | Queue/deque operations | Deque (Java), Queue (C#) |
| `BTreeMap<K, V>` | Sorted keys | TreeMap (Java), SortedDictionary (C#) |
| `BTreeSet<T>` | Sorted unique values | TreeSet (Java), SortedSet (C#) |

## Common Patterns

### Pattern 1: Building Collections

```rust
// From iterator
let v: Vec<i32> = (0..10).collect();

// From another collection
let mut set = HashSet::new();
for item in vec![1, 2, 3] {
    set.insert(item);
}

// Or more idiomatically
let set: HashSet<_> = vec![1, 2, 3].into_iter().collect();
```

### Pattern 2: Chaining Operations

```rust
let result: Vec<String> = vec!["hello", "world", "rust"]
    .into_iter()
    .map(|s| s.to_uppercase())
    .filter(|s| s.len() > 4)
    .collect();
// ["HELLO", "WORLD"]
```

### Pattern 3: Entry API for HashMap

```rust
// Count occurrences
let mut counts = HashMap::new();
for item in items {
    *counts.entry(item).or_insert(0) += 1;
}

// Group by key
let mut groups: HashMap<char, Vec<String>> = HashMap::new();
for word in words {
    groups.entry(word.chars().next().unwrap())
        .or_insert(Vec::new())
        .push(word);
}
```

## Practice Time

Collections are fundamental to Rust programming. Practice with [Week 5 Exercises](../practical/week5-exercises.md)!

## Summary

- ✅ `Vec<T>` is a growable array (like ArrayList/List/array)
- ✅ `HashMap<K, V>` stores key-value pairs
- ✅ `HashSet<T>` stores unique values
- ✅ Iterators are lazy and zero-cost
- ✅ Iterator adapters (map, filter) are idiomatic Rust
- ✅ Collections follow ownership rules
- ✅ Use `entry()` API for elegant HashMap updates

**Next:** [Week 6: Traits](week6-traits.md) - Rust's powerful abstraction mechanism!

---

[← Week 4](week4-errors.md) | [Home](../README.md) | [Next: Week 6 →](week6-traits.md)
