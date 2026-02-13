# Setting Up Your Rust Development Environment

## Installing Rust

### Using Rustup (Recommended)

Rustup is the official Rust toolchain installer. It manages Rust versions and associated tools.

**Linux/macOS:**
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

**Windows:**
Download and run [rustup-init.exe](https://rustup.rs/)

### Verify Installation

```bash
rustc --version
cargo --version
```

You should see version information for both the Rust compiler (`rustc`) and Cargo (Rust's package manager).

## Essential Tools

### 1. Cargo - The Rust Build Tool

Cargo comes with Rust and handles:
- Project creation: `cargo new project_name`
- Building: `cargo build`
- Running: `cargo run`
- Testing: `cargo test`
- Documentation: `cargo doc`
- Package management (like npm, NuGet, Maven)

### 2. Rust Analyzer (IDE Support)

**For Visual Studio Code:**
- Install the "rust-analyzer" extension
- This provides IntelliSense, error checking, and code completion

**For IntelliJ/CLion:**
- Install the "Rust" plugin

**For Vim/Neovim:**
- Use CoC with coc-rust-analyzer or native LSP

### 3. Clippy (Linter)

Install with:
```bash
rustup component add clippy
```

Run with:
```bash
cargo clippy
```

Clippy is like ESLint for TypeScript, or ReSharper for C# - it catches common mistakes and suggests improvements.

### 4. Rustfmt (Code Formatter)

Install with:
```bash
rustup component add rustfmt
```

Format your code:
```bash
cargo fmt
```

Similar to Prettier for TypeScript or dotnet format for C#.

## Creating Your First Project

```bash
cargo new hello_rust
cd hello_rust
cargo run
```

This creates a new binary project with:
```
hello_rust/
├── Cargo.toml       # Like package.json, pom.xml, or .csproj
└── src/
    └── main.rs      # Your main source file
```

## Understanding Cargo.toml

```toml
[package]
name = "hello_rust"
version = "0.1.0"
edition = "2021"

[dependencies]
# Add external crates here (like npm packages, NuGet packages, or Maven dependencies)
```

## IDE Comparison

Coming from other languages, here's how Rust tooling compares:

| Feature | Rust | TypeScript | C# | Java |
|---------|------|------------|----|----- |
| Package Manager | Cargo | npm/yarn | NuGet | Maven/Gradle |
| Build Tool | Cargo | tsc/webpack | MSBuild/dotnet | Maven/Gradle |
| Formatter | rustfmt | Prettier | dotnet format | google-java-format |
| Linter | Clippy | ESLint | Roslyn analyzers | SpotBugs/Checkstyle |
| Test Runner | cargo test | Jest/Mocha | xUnit/NUnit | JUnit |

## Cargo Commands You'll Use Daily

```bash
# Create new project
cargo new project_name          # Binary (executable)
cargo new --lib library_name    # Library

# Development
cargo build                     # Compile (debug mode)
cargo build --release           # Compile (optimized)
cargo run                       # Compile and run
cargo check                     # Fast compile check (no binary)

# Quality
cargo test                      # Run tests
cargo clippy                    # Run linter
cargo fmt                       # Format code

# Documentation
cargo doc --open                # Generate and open docs
```

## Next Steps

1. Create a test project with `cargo new test_project`
2. Open it in your IDE
3. Verify rust-analyzer is working (you should see inline type hints)
4. Run `cargo run` to see "Hello, world!"
5. Continue to [Language Comparison Guide](../comparisons/README.md)

---

[← Back to Main](../README.md) | [Next: Language Comparisons →](../comparisons/README.md)
