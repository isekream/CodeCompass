# Go (Golang) Rules

## Formatting & Style

* **`gofmt` / `goimports`:** All Go code MUST be formatted with `gofmt`. Using `goimports` (a superset of `gofmt` that also manages imports) is preferred. Enforce this in CI pipelines and pre-commit hooks. This covers indentation, spacing, alignment, and import organization.
* **Line Length:** While `gofmt` doesn't enforce a strict line length, aim for readability. Keep lines reasonably short (often cited around 80-120 characters, but prioritize clarity).
* **Comments:** Use `//` for most comments. Use `/* ... */` primarily for package comments or temporarily disabling code blocks. Write clear, concise, and grammatically correct comments. Document all exported identifiers (variables, constants, functions, types). Use complete sentences starting with the name of the thing being documented.
* **MixedCaps / camelCase:** Go uses `MixedCaps` (or `camelCase` starting with lowercase) for identifiers. Exported identifiers start with an uppercase letter; unexported identifiers start with a lowercase letter.
* **Simplicity:** Strive for simple, clear, and readable code. Avoid unnecessary complexity or overly clever solutions.

## Naming Conventions

* **Packages:** Package names should be short, concise, lowercase, single-word names. Avoid underscores or `mixedCaps`. The package name is the base name of its source directory (e.g., package `net/http` is in directory `net/http`). Avoid generic names like `util`, `common`, or `helpers` for packages; group by functionality.
* **Interfaces:** Interfaces defining a single method are often named by the method name plus an "-er" suffix (e.g., `Reader`, `Writer`, `Formatter`). For broader interfaces, use descriptive nouns.
* **Variables:** Use short, descriptive variable names. Single-letter variables are common for loop counters or receivers, but use longer names for variables with wider scope or less obvious purpose. Use `camelCase`.
* **Constants:** Constants should be named using `MixedCaps` or `camelCase` like variables. Use `iota` for related constants.
* **Acronyms:** Treat acronyms (like `URL`, `ID`, `HTTP`) as single words in names. Capitalize them fully when they start an exported identifier (`ServeHTTP`) or are entirely the identifier (`URL`). Use all lowercase if unexported and not at the beginning (`xmlHTTPRequest`). `userID` is preferred over `userId`.
* **Receivers:** Receiver names should be short (often one or two letters), typically an abbreviation of the type (e.g., `c` for `Client`). Be consistent within a type's methods. Do not use generic names like `this` or `self`.

## Packages & Project Structure

* **Keep it Simple:** Start with a simple project structure. Avoid deep nesting or overly complex layouts initially. Add structure (sub-packages) only when necessary.
* **`internal/`:** Place code intended only for use within your project/module under an `internal/` directory. Go tooling prevents code outside the parent of `internal/` from importing it.
* **`pkg/`:** Use a `pkg/` directory *only if* you intend to provide library code for external projects to import directly. For internal-only libraries used by your application(s), prefer `internal/`. Avoid generic top-level directories like `src/`.
* **`cmd/`:** Place `main` packages (executables) within subdirectories under `cmd/` (e.g., `cmd/my-app/main.go`).
* **Circular Dependencies:** Avoid circular package dependencies. Refactor code or use interfaces to break cycles if they arise.
* **Package Scope:** Design packages with clear responsibilities. Export only the necessary identifiers (types, functions, constants, variables).

## Error Handling

* **Explicit Error Returns:** Functions that can fail should return an `error` value as their last return value.
* **Check Errors Immediately:** Check returned errors immediately after the function call. Do not ignore errors using the blank identifier (`_`) unless you explicitly intend to discard the error (document why).
* **Handle or Return:** Handle the error if possible within the current function's context. If not, return the error (potentially wrapped) to the caller. Avoid logging and returning an error; do one or the other. Logging should typically happen at the top level (e.g., `main` or HTTP request handler).
* **Error Wrapping:** Use `fmt.Errorf` with the `%w` verb to wrap errors, adding contextual information as the error propagates up the call stack. This preserves the underlying error type for inspection using `errors.Is` and `errors.As`.
    * Example: `return fmt.Errorf("connecting to database: %w", err)`
* **Error Context:** Add meaningful context when wrapping errors (what operation was attempted, relevant parameters). Avoid generic messages like "failed to...". Error messages should not be capitalized or end with punctuation.
* **Sentinel Errors:** Define specific error values (sentinel errors, e.g., `var ErrNotFound = errors.New("not found")`) for errors callers might need to check for specifically. Use `errors.Is` to check for sentinel errors. Use sparingly.
* **Custom Error Types:** Define custom error types (structs implementing the `error` interface) when you need to carry more structured information with an error. Use `errors.As` to check if an error in a chain matches a specific custom type.
* **`panic` / `recover`:** Avoid using `panic` for normal error handling. Use it only for truly exceptional, unrecoverable situations (e.g., impossible state, programmer error during initialization). If a library panics, it's generally considered a bug. `recover` can be used in `defer` functions to regain control after a panic, often used in server frameworks to prevent one request from crashing the whole server, but should not be used for standard flow control.

## Concurrency (Goroutines & Channels)

* **Use Goroutines for Concurrency:** Launch concurrent tasks using the `go` keyword.
* **Channels for Communication:** Prefer communication over shared memory. Use channels (`chan`) to pass data and synchronize between goroutines ("Share memory by communicating; don't communicate by sharing memory").
* **`sync` Package:** Use `sync` package primitives (`Mutex`, `RWMutex`, `WaitGroup`, `Cond`, `Once`) when direct memory sharing and locking are necessary. Keep critical sections (code protected by mutexes) short.
* **`sync.WaitGroup`:** Use `WaitGroup` to wait for a collection of goroutines to finish.
* **Buffered Channels:** Use buffered channels carefully when you need some decoupling between sender and receiver, but be mindful of potential deadlocks or hidden blocking if the buffer fills.
* **`select` Statement:** Use `select` to coordinate multiple channel operations (send or receive) and handle timeouts or cancellations.
* **`context` Package:** Use the `context` package (`context.Context`) for managing deadlines, cancellations, and passing request-scoped values across API boundaries and between goroutines. Pass `Context` as the first argument to functions.
* **Avoid Goroutine Leaks:** Ensure goroutines have a clear exit condition. Use `context` cancellation or close channels to signal goroutines to terminate when they are no longer needed.
* **Race Detector:** Use the Go race detector (`go test -race`, `go run -race`) during testing and development to identify potential data races.

## Simplicity & Readability

* **Keep Functions Short:** Aim for small, focused functions that do one thing well.
* **Minimize Nesting:** Avoid deep nesting of `if`, `for`, etc. Handle error conditions first ("guard clauses") and return early.
* **Avoid Unnecessary Abstraction:** Don't over-abstract. Use interfaces effectively to decouple components, but avoid creating interfaces for things that don't need abstraction. Accept interfaces, return structs.
* **Explicit is Better than Implicit:** Go generally favors explicitness.
* **Zero Values:** Understand and utilize zero values (e.g., `0` for numeric types, `false` for bool, `""` for string, `nil` for pointers, slices, maps, channels, interfaces, functions). Initialize variables explicitly if the zero value isn't appropriate.

## Testing

* **`testing` Package:** Use the standard `testing` package for writing tests (`_test.go` files, `TestXxx` functions).
* **Table-Driven Tests:** Use table-driven tests for testing multiple input/output scenarios concisely.
* **Subtests:** Use `t.Run` to create subtests within a test function for better organization and reporting.
* **Test Coverage:** Use `go test -cover` to measure test coverage. Aim for reasonable coverage of logic.
* **Assertions:** Use standard `if condition { t.Errorf(...) }` checks or helper functions. Consider libraries like `testify/assert` or `testify/require` for more complex assertions if desired, but standard library checks are often sufficient.
* **Parallel Tests:** Use `t.Parallel()` within subtests to allow them to run concurrently for faster execution.

## Security

* **Input Validation:** Validate and sanitize all input from untrusted sources (HTTP requests, files, databases, environment variables).
* **SQL Injection:** Use parameterized queries or prepared statements with database drivers. Do not construct SQL queries by concatenating strings with user input.
* **Cross-Site Scripting (XSS):** Use Go's `html/template` package which provides context-aware auto-escaping for HTML output. Be careful when injecting data into JavaScript contexts.
* **Command Injection:** Avoid executing external commands (`os/exec`) with user-provided input. If necessary, carefully validate and sanitize arguments.
* **Directory Traversal:** Sanitize file paths provided by users. Use `filepath.Clean` and ensure paths stay within expected directories.
* **Dependency Vulnerabilities:** Use `govulncheck` to scan dependencies for known vulnerabilities. Keep dependencies updated (`go get -u`).
* **Secrets Management:** Do not hardcode secrets. Use environment variables, configuration files (outside version control), or dedicated secrets management systems.

## Common Go Patterns & Idioms

* **Functional Options:** Use the Functional Options pattern for configurable objects with many optional settings.
* **Context Propagation:** Always propagate `context.Context` through your call stack, passing it as the first parameter.
* **Error Handling Patterns:** Return errors from functions that can fail, check errors immediately, wrap errors for context.
* **Composition Over Inheritance:** Use struct embedding and interfaces for composition instead of relying on inheritance.
* **Accept Interfaces, Return Structs:** Define functions to accept interface types but return concrete types.
* **Empty Interface Avoidance:** Avoid `interface{}` (or `any` in Go 1.18+) when possible, preferring strongly typed code.
* **Embedding:** Use embedding to compose structs and interfaces instead of traditional inheritance.

## Performance Considerations

* **Allocations:** Be mindful of unnecessary memory allocations, especially in hot paths.
* **Optimization:** Profile (`pprof`) before optimizing. Focus on algorithms and data structures first.
* **Memory Pooling:** For high-performance applications, use `sync.Pool` to reuse objects and reduce GC pressure.
* **String Concatenation:** Use `strings.Builder` for efficient string building rather than `+` operator.
* **JSON Performance:** Consider using code generation tools like `easyjson` for high-performance JSON handling.
* **Inlining:** Keep performance-critical functions small enough to be inlined by the compiler.
* **Avoid Reflection:** Minimize use of the `reflect` package as it can hurt performance and type safety.
