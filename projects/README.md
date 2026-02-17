# Rust Project Ideas

After completing the 8-week learning path, you're ready to build real-world projects! Here are project ideas organized by difficulty and the concepts they reinforce.

## 🟢 Beginner Projects (After Week 4)

### 1. Command-Line Todo List

**What you'll build:** A CLI app to manage todos

**Features:**
- Add, remove, list todos
- Mark todos as complete
- Save/load from JSON file
- Filter by status

**Concepts practiced:**
- Structs and enums
- File I/O
- Error handling with Result
- Command-line argument parsing

**Crates to explore:**
- `clap` for CLI argument parsing
- `serde` and `serde_json` for JSON serialization

**Estimated time:** 2-3 days

### 2. Temperature Logger

**What you'll build:** Program that logs temperature readings

**Features:**
- Read temperature from user input
- Store readings with timestamps
- Calculate statistics (min, max, average)
- Export to CSV

**Concepts practiced:**
- Collections (Vec, HashMap)
- Error handling
- File I/O
- String manipulation

**Estimated time:** 2-3 days

### 3. Text File Analyzer

**What you'll build:** CLI tool to analyze text files

**Features:**
- Word count, line count, character count
- Most frequent words
- Reading level calculation
- Support for multiple files

**Concepts practiced:**
- File I/O
- String processing
- HashMap for counting
- Iterators

**Crates to explore:**
- `regex` for pattern matching

**Estimated time:** 2-4 days

### 4. Password Generator

**What you'll build:** Secure password generator

**Features:**
- Configurable length and character sets
- Strength indicator
- Multiple password generation
- Copy to clipboard

**Concepts practiced:**
- Randomness
- String manipulation
- Error handling
- CLI arguments

**Crates to explore:**
- `rand` for random generation
- `clap` for CLI

**Estimated time:** 1-2 days

## 🟡 Intermediate Projects (After Week 6)

### 5. Markdown to HTML Converter

**What you'll build:** Parser that converts Markdown to HTML

**Features:**
- Headers, bold, italic, links
- Lists and code blocks
- File watching for live preview
- Custom themes

**Concepts practiced:**
- String parsing
- Pattern matching
- Trait implementations
- File I/O

**Crates to explore:**
- `pulldown-cmark` (or build your own parser!)
- `notify` for file watching

**Estimated time:** 5-7 days

### 6. Simple HTTP Server

**What you'll build:** Basic web server

**Features:**
- Serve static files
- Handle GET/POST requests
- Basic routing
- MIME type detection

**Concepts practiced:**
- TCP sockets
- HTTP protocol
- Error handling
- File system operations

**Crates to explore:**
- `std::net` for networking
- Or use `tiny_http` or `actix-web`

**Estimated time:** 5-7 days

### 7. CSV Data Processor

**What you'll build:** Tool for processing CSV files

**Features:**
- Filter rows by criteria
- Aggregate data (sum, average, group by)
- Join multiple CSV files
- Export results

**Concepts practiced:**
- Iterators
- Generic functions
- Trait bounds
- Error handling

**Crates to explore:**
- `csv` crate
- `serde` for deserialization

**Estimated time:** 4-6 days

### 8. Calculator with Expression Parsing

**What you'll build:** Calculator that evaluates mathematical expressions

**Features:**
- Basic operations (+, -, *, /)
- Parentheses support
- Variables
- Functions (sin, cos, sqrt, etc.)

**Concepts practiced:**
- Parsing and tokenization
- Enums for AST
- Pattern matching
- Recursive algorithms

**Estimated time:** 5-8 days

## 🔴 Advanced Projects (After Week 8)

### 9. Concurrent Web Crawler

**What you'll build:** Multi-threaded web crawler

**Features:**
- Download and parse web pages
- Follow links (with depth limit)
- Extract specific information
- Respect robots.txt
- Rate limiting

**Concepts practiced:**
- Concurrency with threads
- Channels for communication
- Shared state with Arc/Mutex
- HTTP requests
- HTML parsing

**Crates to explore:**
- `reqwest` for HTTP
- `scraper` for HTML parsing
- `tokio` for async runtime

**Estimated time:** 7-10 days

### 10. Chat Application

**What you'll build:** Multi-user chat server and client

**Features:**
- TCP-based communication
- Multiple clients
- Broadcast messages
- Private messages
- User nicknames

**Concepts practiced:**
- Async/await
- Networking
- Concurrent connections
- Message passing
- Error handling

**Crates to explore:**
- `tokio` for async runtime
- `serde` for message serialization

**Estimated time:** 7-10 days

### 11. Key-Value Database

**What you'll build:** Simple persistent key-value store

**Features:**
- Set, get, delete operations
- Persistence to disk
- Transaction support
- Concurrent access
- CLI and library interface

**Concepts practiced:**
- File I/O and serialization
- Concurrency (Mutex, RwLock)
- API design
- Performance optimization

**Crates to explore:**
- `serde` for serialization
- `bincode` or `sled` for inspiration

**Estimated time:** 10-14 days

### 12. Real-Time Data Dashboard

**What you'll build:** Web-based dashboard with live updates

**Features:**
- REST API backend
- WebSocket for live updates
- Data aggregation
- Multiple data sources
- Frontend integration

**Concepts practiced:**
- Async web servers
- WebSockets
- JSON APIs
- Concurrent data processing
- Error handling

**Crates to explore:**
- `axum` or `actix-web` for web server
- `tokio-tungstenite` for WebSockets
- `serde_json` for JSON

**Estimated time:** 14-21 days

## 🎯 Project Ideas by Domain

### Systems Programming
- **File System Watcher**: Monitor directory changes
- **Process Monitor**: Track running processes and resource usage
- **Log Analyzer**: Parse and analyze system logs
- **Backup Tool**: Incremental file backup utility

### Command-Line Tools
- **Git Alternative**: Simple version control
- **Package Manager**: Dependency management tool
- **Task Scheduler**: Cron-like job scheduler
- **Terminal UI App**: Interactive TUI with `tui-rs`

### Web & Network
- **REST API**: Complete CRUD API
- **Proxy Server**: HTTP/HTTPS proxy
- **Port Scanner**: Network security tool
- **DNS Resolver**: Custom DNS client

### Data Processing
- **JSON Processor**: `jq`-like tool
- **Image Processor**: Basic image manipulation
- **Log Aggregator**: Collect and analyze logs
- **Data Pipeline**: ETL tool for data transformation

### Games & Graphics
- **Snake Game**: Terminal-based game
- **Chess Engine**: AI chess player
- **Ray Tracer**: 3D rendering
- **Game of Life**: Conway's cellular automaton

## 💡 Project Development Tips

### Start Small
- Begin with minimal features
- Add complexity gradually
- Focus on core functionality first

### Use Version Control
```bash
git init
git add .
git commit -m "Initial commit"
```

### Write Tests
```rust
#[cfg(test)]
mod tests {
    use super::*;
    
    #[test]
    fn test_feature() {
        // Your tests here
    }
}
```

### Document Your Code
```rust
/// Calculates the sum of two numbers.
///
/// # Examples
///
/// ```
/// let result = add(2, 3);
/// assert_eq!(result, 5);
/// ```
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}
```

### Use Clippy and Rustfmt
```bash
cargo clippy    # Catch common mistakes
cargo fmt       # Format code consistently
```

### Publish Your Project
- Push to GitHub
- Write a good README
- Add examples
- Consider publishing to crates.io

## 📚 Resources for Project Development

### Documentation
- [docs.rs](https://docs.rs/) - Documentation for all crates
- [Rust Cookbook](https://rust-lang-nursery.github.io/rust-cookbook/) - Common tasks
- [This Week in Rust](https://this-week-in-rust.org/) - Community updates

### Find Crates
- [crates.io](https://crates.io/) - Official package registry
- [lib.rs](https://lib.rs/) - Alternative with better categorization
- [Blessed.rs](https://blessed.rs/) - Curated list of quality crates

### Getting Help
- [Rust Users Forum](https://users.rust-lang.org/)
- [Rust Discord](https://discord.gg/rust-lang)
- [r/rust](https://www.reddit.com/r/rust/)
- Stack Overflow with `[rust]` tag

## 🎓 After Your First Project

1. **Get feedback**: Share on Reddit, Discord, or Users Forum
2. **Iterate**: Improve based on feedback
3. **Start another project**: Each project teaches new lessons
4. **Contribute to open source**: Find Rust projects on GitHub
5. **Keep learning**: Explore advanced topics like macros, unsafe code, embedded Rust

## 🚀 Advanced Topics to Explore Next

After mastering the basics and building projects:

- **Macros**: Procedural and declarative macros
- **Unsafe Rust**: When and how to use unsafe code
- **Embedded Rust**: Programming microcontrollers
- **WebAssembly**: Compile Rust to WASM
- **Game Development**: `bevy` game engine
- **Operating Systems**: Write OS components in Rust
- **Cryptography**: Implement crypto algorithms

---

**Ready to build?** Pick a project that excites you and start coding!

[← Back to Main](../README.md)
