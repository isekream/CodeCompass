# C# / .NET Core Rules

## Naming & Formatting Conventions

* **Style Guide:** Follow the official Microsoft C# Coding Conventions and .NET Runtime coding style guidelines. Use tools like `.editorconfig` to enforce consistency.
* **Formatting:**
    * Use 4 spaces for indentation (no tabs).
    * Use Allman style braces (each brace on a new line).
    * Use `dotnet format` or IDE formatting tools (configured consistently) to enforce style.
* **Naming:**
    * **Namespaces:** `PascalCase` (e.g., `MyCompany.MyApplication.DataAccess`). Organize namespaces logically based on features or layers.
    * **Classes/Structs/Interfaces/Enums/Delegates:** `PascalCase` (e.g., `UserService`, `IOrderRepository`).
    * **Interfaces:** Prefix interfaces with `I` (e.g., `IUserService`).
    * **Methods/Properties/Events/Local Functions:** `PascalCase` (e.g., `CalculateTotal()`, `UserName`, `DataReceived`).
    * **Method Parameters/Local Variables:** `camelCase` (e.g., `userId`, `totalCount`).
    * **Private/Internal Fields:** Prefix with underscore and use `camelCase` (`_connectionString`). Use `readonly` where possible.
    * **Public Fields:** Use `PascalCase`. Use sparingly; prefer properties.
    * **Constants (`const`, `static readonly`):** `PascalCase`.
    * **Acronyms:** Treat acronyms over two letters long like words (e.g., `XmlReader`, `HtmlDocument`). Two-letter acronyms are all caps (e.g., `IOStream`).
* **Readability:**
    * Write only one statement per line.
    * Write only one declaration per line.
    * Add blank lines to separate logical groups of code (e.g., between methods).

## Language Features (C#)

* **`var` Keyword:** Use `var` for local variable declarations *only* when the type is obvious from the right-hand side of the assignment (e.g., `var stream = new MemoryStream();`, `var user = GetUser();`). Otherwise, use explicit types for clarity. Do not use `var` for primitive types (`int`, `string`, `bool`, etc.).
* **Properties:** Use properties (`public string Name { get; set; }`) instead of public fields for encapsulation. Use auto-properties where appropriate. Use `init` accessors for immutable properties set during object initialization.
* **Expression-Bodied Members:** Use expression-bodied members (`=>`) for simple methods, properties, constructors, etc., where it improves readability.
* **LINQ:** Use LINQ (query syntax or method syntax) for querying and manipulating collections where it enhances readability compared to manual loops. Prefer method syntax for simpler queries and query syntax for more complex ones.
* **Pattern Matching:** Utilize C#'s pattern matching features (e.g., `is` expressions, `switch` expressions) for clearer type checks and conditional logic.
* **String Interpolation:** Prefer string interpolation (`$"Hello, {name}"`) over `string.Format()` or concatenation (`+`) for readability.
* **Tuples:** Use Value Tuples `(int, string)` for simple, temporary groupings of data, especially for returning multiple values from private methods. For public APIs, prefer defining dedicated classes or structs.
* **`using` Statements/Declarations:** Always wrap disposable objects (`IDisposable`) in `using` statements or `using` declarations (C# 8+) to ensure proper disposal (e.g., file streams, database connections, `HttpClient`).
* **Default Interface Methods:** Use default interface methods (C# 8+) cautiously to add functionality to interfaces without breaking existing implementers.
* **Records:** Use `record` types (C# 9+) for immutable data transfer objects (DTOs) or value objects to reduce boilerplate code.

## Error Handling (Exceptions)

* **Catch Specific Exceptions:** Catch the most specific exception types possible rather than generic `Exception` or `SystemException`. Handle only exceptions you can meaningfully address at the current level.
* **Avoid Empty Catch Blocks:** Never swallow exceptions with empty `catch` blocks. At minimum, log the exception.
* **Use `finally` or `using`:** Use `finally` blocks or, preferably, `using` statements/declarations to ensure resource cleanup (closing files, disposing objects) occurs even if exceptions are thrown.
* **Throw Appropriately:** Throw exceptions when methods cannot fulfill their contract due to exceptional circumstances. Prefer standard .NET exception types where applicable (`ArgumentNullException`, `InvalidOperationException`, etc.). Create custom exceptions for domain-specific error conditions if needed.
* **Don't Use Exceptions for Flow Control:** Avoid using exceptions for normal program flow logic (e.g., checking if a key exists before accessing a dictionary). Use conditional checks instead.
* **Exception Messages:** Provide clear and informative messages when throwing exceptions.

## Asynchronous Programming (`async`/`await`)

* **Use `async`/`await` for I/O:** Use `async`/`await` for I/O-bound operations (network requests, database calls, file system access) to avoid blocking threads and improve application responsiveness and scalability.
* **Async All the Way:** Prefer making the entire call stack asynchronous (from the UI event handler or API controller down to the I/O call). Avoid mixing blocking and async code (`.Result`, `.Wait()`) as this can lead to deadlocks.
* **Return `Task`/`Task<T>`:** Async methods should return `Task` (for void methods) or `Task<T>` (for methods returning `T`). Avoid `async void` except for top-level event handlers (e.g., UI button click handlers).
* **`ConfigureAwait(false)`:** In library code (code not dependent on a specific synchronization context like UI or ASP.NET Classic), use `.ConfigureAwait(false)` on awaited tasks to avoid potential deadlocks and improve performance by not forcing resumption on the original context. This is generally *not* needed in modern ASP.NET Core applications.
* **Cancellation:** Use `CancellationToken` to support cancellation in long-running async operations. Pass the token through the async call chain and check `token.IsCancellationRequested` or pass it to cancellable async methods.
* **`ValueTask<T>`:** Use `ValueTask<T>` instead of `Task<T>` only in performance-critical scenarios where methods are expected to complete synchronously frequently, to avoid heap allocation for the `Task` object. Use with care as it has usage limitations.
* **Parallelism:** Use `Task.WhenAll()` to await multiple independent asynchronous operations concurrently. Use `Task.WhenAny()` to proceed when the first of several operations completes.

## .NET Core Specifics

* **Dependency Injection (DI):** Embrace the built-in DI container. Register services in `Program.cs` (minimal APIs) or `Startup.cs` (`ConfigureServices`). Prefer constructor injection. Understand service lifetimes (`Singleton`, `Scoped`, `Transient`) and choose appropriately.
* **Configuration:** Use the `IConfiguration` system (`appsettings.json`, environment variables, command-line args, etc.). Bind configuration sections to strongly-typed options classes (`IOptions<T>`). Store secrets securely using tools like Secret Manager (development), Azure Key Vault, or environment variables, not in `appsettings.json` committed to source control.
* **Logging:** Use the built-in `ILogger<T>` interface for logging. Configure logging providers and levels appropriately for different environments. Use structured logging where possible.
* **Middleware (ASP.NET Core):** Understand the middleware pipeline. Order middleware components correctly in `Program.cs` / `Startup.Configure`. Write custom middleware for cross-cutting concerns.

## ASP.NET Core

* **Use Controllers or Minimal APIs:** Choose between MVC Controllers/API Controllers or Minimal APIs based on project needs and complexity.
* **RESTful Principles:** Design APIs following RESTful principles (use appropriate HTTP verbs, status codes, resource-based URLs).
* **DTOs:** Use Data Transfer Objects (DTOs) for API request/response payloads to decouple the API contract from internal domain/database models. Use libraries like AutoMapper or Mapster for mapping.
* **Model Validation:** Use data annotations or libraries like FluentValidation to validate incoming request models. Check `ModelState.IsValid` in controllers or use automatic validation filters.
* **HTTPS:** Enforce HTTPS and enable HSTS (HTTP Strict Transport Security) in production environments.
* **Authentication/Authorization:** Implement authentication (e.g., ASP.NET Core Identity, JWT Bearer tokens, OpenID Connect) and authorization (e.g., `[Authorize]` attribute, roles, policies) correctly.
* **Error Handling:** Use centralized exception handling middleware or exception filters to handle errors gracefully and return consistent error responses. Avoid leaking stack traces in production.
* **Response Caching:** Utilize response caching middleware or attributes (`[ResponseCache]`) for frequently accessed, static responses.
* **Compression:** Enable response compression (Gzip, Brotli) to reduce payload size.
* **Rate Limiting:** Implement rate limiting on APIs to prevent abuse.

## Entity Framework Core (EF Core)

* **DbContext Lifetime:** Register `DbContext` with a scoped lifetime (`AddDbContext`) in DI for web applications.
* **Async Methods:** Use asynchronous EF Core methods (`SaveChangesAsync`, `ToListAsync`, `FirstOrDefaultAsync`, etc.) in async application code.
* **Query Optimization:**
    * Retrieve only necessary data: Use `Select()` projections to fetch only required columns/properties.
    * Avoid N+1 query problems: Use eager loading (`Include()`, `ThenInclude()`) or explicit loading (`LoadAsync()`) appropriately. Be cautious with excessive `Include()` calls causing large queries (consider projections instead).
    * Use `AsNoTracking()` for read-only queries where change tracking is not needed, improving performance.
    * Filter and order data in the database where possible (using `Where()`, `OrderBy()`) rather than fetching large amounts of data into memory.
* **Migrations:** Use EF Core Migrations to manage database schema changes. Keep migrations small and focused. Review generated migration scripts. Apply migrations safely during deployment.
* **Transactions:** Use transactions explicitly (`BeginTransactionAsync`, `CommitAsync`, `RollbackAsync`) for operations involving multiple changes that must succeed or fail together. Service methods often define transaction boundaries.
* **Concurrency Control:** Implement concurrency control mechanisms (e.g., using concurrency tokens with `@timestamp`/`rowversion` or specific properties) to handle simultaneous updates to the same entity.

## Security

* **Input Validation:** Validate ALL input (request bodies, query parameters, headers, configuration) to prevent injection attacks (SQLi, XSS, command injection, etc.).
* **Output Encoding:** Ensure data output to HTML/JS contexts is properly encoded (ASP.NET Core MVC/Razor does much of this automatically, but be vigilant, especially with raw HTML or JavaScript).
* **Parameterized Queries:** Use EF Core or other ORMs/libraries that use parameterized queries to prevent SQL injection. Avoid raw SQL string concatenation with user input.
* **Secrets Management:** Store connection strings, API keys, certificates securely (see Configuration section).
* **Dependencies:** Keep .NET SDK, runtime, and NuGet packages updated. Regularly scan for vulnerabilities (e.g., using `dotnet list package --vulnerable`).
* **Least Privilege:** Run application processes with the minimum necessary permissions.

## Testing

* **Unit Tests:** Write unit tests for business logic, services, and utility classes using frameworks like xUnit, NUnit, or MSTest. Use mocking frameworks (e.g., Moq, NSubstitute) to isolate dependencies.
* **Integration Tests:** Write integration tests for interactions between components (e.g., API controllers interacting with services, services interacting with databases). Use `WebApplicationFactory` for testing ASP.NET Core apps in-memory. Use test databases (e.g., in-memory SQLite provider for EF Core, or test containers) for data access layer tests.
* **Test Coverage:** Aim for meaningful test coverage, focusing on critical paths, business logic, and edge cases.
