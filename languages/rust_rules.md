# Rust Rules

## Code Style & Formatting

* **`rustfmt`:** Use `rustfmt` to automatically format all Rust code. Adhere to the default Rust style guide conventions enforced by `rustfmt`. Integrate `rustfmt --check` into CI pipelines.
* **Line Length:** Keep lines under 100 characters where possible.
* **Indentation:** Use 4 spaces for indentation, not tabs.
* **Naming Conventions:**
    * **Crates & Modules:** `snake_case` (e.g., `my_crate`, `utils`).
    * **Types (Structs, Enums, Traits):** `PascalCase` (e.g., `MyStruct`, `ServiceStatus`, `Display`).
    * **Functions & Methods:** `snake_case` (e.g., `calculate_sum`, `user.get_name()`).
    * **Variables & Statics:** `snake_case` (e.g., `let current_user;`, `static MAX_CONNECTIONS`).
    * **Constants:** `UPPER_SNAKE_CASE` (e.g., `const DEFAULT_PORT: u16 = 8080;`).
    * **Type Parameters (Generics):** Short `PascalCase` (e.g., `<T>`, `<K, V>`, `<AppError>`).
    * **Lifetimes:** Short lowercase, usually starting with `'a` (e.g., `&'a str`, `<'a, 'b>`).
* **Comments:**
    * Use line comments (`//`) over block comments (`/* ... */`). Add a space after `//`.
    * Use doc comments (`///` for outer, `//!` for inner) to generate documentation with `cargo doc`. Document all public items (crates, modules, functions, structs, enums, traits, type aliases).
    * Write comments for non-obvious code, explaining the *why*.
* **Imports (`use`):**
    * Group `use` statements at the top of the module. Standard order: `std`, external crates, then internal project modules (`crate::`, `super::`).
    * Use curly braces `{}` to import multiple items from the same path concisely.
    * Avoid wildcard imports (`use std::collections::*;`) except potentially in prelude patterns.

## Ownership, Borrowing & Lifetimes

* **Embrace Ownership:** Understand and leverage Rust's ownership system to ensure memory safety. Pass ownership when appropriate; borrow otherwise.
* **Prefer Borrowing:** Use immutable (`&`) or mutable (`&mut`) references (borrows) to access data without taking ownership whenever possible.
* **Minimize Cloning:** Avoid unnecessary cloning (`.clone()`). Clone data only when ownership transfer or mutation of shared data is not feasible or desired. Profile code if cloning seems excessive.
* **Lifetimes:** Use explicit lifetime annotations only when the compiler cannot infer them (often in structs holding references or functions returning references derived from multiple inputs). Keep lifetimes as simple as possible.

## Error Handling

* **`Result<T, E>`:** Use `Result<T, E>` for recoverable errors (operations that might fail but can be reasonably handled, e.g., I/O, parsing). Avoid panicking for recoverable errors in library code.
* **`panic!`:** Use `panic!` primarily for unrecoverable errors, which represent bugs in the program logic (e.g., index out of bounds on a checked access, violated invariants). Panics should ideally not occur in robust applications.
* **Custom Error Types:** Define custom error types (structs or enums) for your libraries or applications, especially if you need to represent different kinds of errors or provide additional context.
    * Implement the `std::error::Error` trait for your custom error types.
    * Consider using crates like `thiserror` to easily derive `Error` implementations for enums.
* **Propagate Errors:** Use the `?` operator to propagate errors cleanly up the call stack within functions returning `Result`.
* **Handling Errors:** Use `match`, `if let Ok/Err`, or combinator methods (`map`, `map_err`, `and_then`, `ok_or`) to handle `Result` values appropriately at the point where recovery or specific action is needed.
* **`unwrap()` & `expect()`:** Avoid `.unwrap()` in production code, as it panics on `Err` or `None`. Use `.expect("Reason why this should succeed")` during prototyping or in tests where failure indicates a bug, providing a helpful panic message. Prefer proper error handling (`match`, `?`, etc.) in library and application code.

## Concurrency

* **Threads:** Use `std::thread` for spawning OS threads. Use `move` closures with `thread::spawn` to transfer ownership of variables needed by the thread.
* **Shared State:**
    * Use `Arc<T>` (Atomically Reference Counted) for sharing ownership of data across multiple threads.
    * Use `Mutex<T>` or `RwLock<T>` (within an `Arc` if shared) to safely mutate shared data. `Mutex` allows only one thread access at a time; `RwLock` allows multiple readers *or* one writer.
    * Minimize the time locks are held to reduce contention. Avoid long-running operations while holding a lock.
* **Message Passing:** Prefer message passing using channels (`std::sync::mpsc` or alternatives like `crossbeam-channel`) for communication between threads over shared memory with locks when possible, as it often leads to simpler designs.
* **Async/Await:** Use Rust's `async/await` syntax with an async runtime (like Tokio, async-std) for non-blocking I/O-bound concurrency. This is generally more efficient than spawning many OS threads for tasks waiting on I/O.
* **Send & Sync:** Understand the `Send` (type can be sent to another thread) and `Sync` (type can be safely shared via `&T` across threads) marker traits. The compiler enforces these for thread safety.

## Crates & Cargo

* **`Cargo.toml`:**
    * Define package metadata clearly (`name`, `version`, `authors`, `description`, `license`). Use SemVer for versioning.
    * Specify dependencies with specific, compatible versions. Use caret requirements (`^1.2.3`) or tilde requirements (`~1.2.3`) generally, or exact versions (`=1.2.3`) if necessary. Avoid wildcard versions (`*`).
    * Use features (`[features]`) to enable optional dependencies or conditional compilation.
    * Use `[dev-dependencies]` for testing and benchmarking libraries.
    * Define profiles (`[profile.release]`) to configure optimization levels, debug info, etc., for different build types (e.g., enable LTO for release builds).
* **`Cargo.lock`:** Always commit the `Cargo.lock` file for applications and binaries to ensure reproducible builds. Libraries may or may not commit it, depending on policy (committing it helps CI but can cause issues for downstream users if versions are too restrictive).
* **Workspaces:** Use Cargo Workspaces to manage multiple related crates within a single project/repository.
* **Dependency Audit:** Regularly audit dependencies for security vulnerabilities using `cargo-audit` or similar tools. Keep dependencies updated (`cargo update`).
* **Build Scripts (`build.rs`):** Use build scripts only when necessary (e.g., compiling C code, code generation). Keep them simple and reliable.

## Security

* **Minimize `unsafe`:** Avoid using `unsafe` blocks whenever possible. Rust's primary benefit is compile-time safety guarantees, which `unsafe` bypasses.
    * If `unsafe` is necessary (FFI, low-level performance optimizations), encapsulate it within a safe abstraction/API.
    * Thoroughly document the invariants that must be upheld for the `unsafe` block to be sound.
    * Rigorously review and test all `unsafe` code.
* **Input Validation:** Validate and sanitize all external inputs (user input, network data, file contents) to prevent injection, path traversal, and other vulnerabilities. Use Rust's type system (e.g., specific types instead of strings) to enforce constraints where possible.
* **Error Handling:** Handle errors properly (see Error Handling section). Avoid panics caused by unhandled errors, which could lead to DoS.
* **Dependencies:** Vet dependencies carefully. Use `cargo-audit` to check for known vulnerabilities. Keep dependencies updated.
* **Cryptography:** Use well-vetted, audited cryptography crates (e.g., `ring`, `rustls`). Do not attempt to implement cryptographic primitives yourself unless you are an expert. Use secure random number generation (`rand::rngs::OsRng`).
* **Integer Overflows:** Be aware that integer overflows panic in debug builds but wrap in release builds by default. Use checked (`.checked_add()`), wrapping (`.wrapping_add()`), or saturating (`.saturating_add()`) arithmetic explicitly if specific overflow behavior is required. Consider enabling overflow checks for release builds in `Cargo.toml` if needed.
* **Memory Safety:** Leverage Rust's ownership and borrowing rules. Avoid raw pointers and manual memory management unless within carefully controlled `unsafe` blocks.

## Performance

* **Benchmarking:** Use `cargo bench` and libraries like `criterion` to write benchmarks for performance-critical code.
* **Profiling:** Use profiling tools (e.g., `perf`, `Instruments`, `cargo-flamegraph`) to identify performance bottlenecks ("hot spots"). Optimize hot paths first.
* **Release Builds:** Always test and measure performance using release builds (`cargo build --release`), which enable optimizations.
* **Allocations:** Be mindful of memory allocations, especially within loops. Prefer stack allocation where possible. Use efficient data structures (`Vec` vs. `HashMap` vs. `BTreeMap` depending on needs). Avoid unnecessary allocations and cloning. Use iterators effectively.
* **Zero-Cost Abstractions:** Leverage Rust's zero-cost abstractions (traits, generics), but be aware that excessive monomorphization can increase compile times and binary size.
* **Compiler Optimizations:** Understand basic compiler optimizations (inlining, LTO). Configure build profiles (`[profile.release]`) in `Cargo.toml` for optimization levels, LTO, codegen units, etc.

## Tooling

* **`clippy`:** Regularly run `cargo clippy` and address its lints. Clippy catches common mistakes and suggests more idiomatic or performant code. Configure `clippy.toml` or use attributes (`#[allow(...)]`, `#[deny(...)]`) as needed.
* **`rustfmt`:** Use `cargo fmt` (see Formatting section).
* **`cargo check`:** Use `cargo check` frequently during development for faster feedback than `cargo build` as it only checks for compilation errors without generating code.

## Idiomatic Rust Patterns

* **Enums over Boolean Flags:** Use enums instead of boolean flags when a variable can have more than two states or when the meaning of true/false isn't immediately clear from context.
* **Builder Pattern:** Implement the Builder Pattern for structs with many optional fields to improve usability and readability of object construction.
* **NewType Pattern:** Use the NewType pattern (wrapping a type in a tuple struct) to create distinct types for type safety, implementing traits, or adding domain-specific behavior.
* **RAII (Resource Acquisition Is Initialization):** Follow RAII principles for resource management. Implement `Drop` trait for types that need cleanup.
* **Traits for Abstraction:** Design APIs around traits rather than concrete types to enable polymorphism and flexibility. Use trait objects (`dyn Trait`) when needed for runtime polymorphism.
* **Type-Driven Development:** Leverage Rust's type system to encode invariants and prevent logic errors at compile time. Make illegal states unrepresentable through the type system when possible.
