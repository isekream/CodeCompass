# ASP.NET Core Rules

## Project Structure & Design

* **Follow C# / .NET Rules:** Adhere to all rules defined in `csharp_dotnet_rules.md`.
* **Standard Project Templates:** Start with standard ASP.NET Core project templates (e.g., Web API, Web App (MVC), Blazor, Razor Pages) unless a custom structure is well-justified.
* **Layered Architecture:** Structure applications logically into layers (e.g., Presentation/API, Application/Services, Domain, Infrastructure). Consider Clean Architecture or Onion Architecture principles for larger applications to enforce separation of concerns and dependency rules.
* **Dependency Injection:** Heavily utilize the built-in Dependency Injection (DI) container (see DI section).
* **Modularity:** Break down large applications into smaller, manageable modules or projects where appropriate.

## Configuration & Dependency Injection (DI)

* **Configuration Sources:** Load configuration from multiple sources (`appsettings.json`, environment-specific files like `appsettings.Development.json`, environment variables, command-line arguments, Azure Key Vault, etc.) using `IConfiguration`.
* **Options Pattern:** Use the Options pattern (`IOptions<T>`, `IOptionsSnapshot<T>`, `IOptionsMonitor<T>`) to bind configuration sections to strongly-typed POCO classes. Inject `IOptions<T>` (or variants) into services.
* **Secrets Management:** Store sensitive configuration (connection strings, API keys) securely using Secret Manager (development), environment variables, Azure Key Vault, or other secure stores. Do **not** commit secrets to source control (e.g., in `appsettings.json`).
* **Service Registration (`Program.cs` / `Startup.cs`):** Register services with the DI container in `Program.cs` (minimal APIs) or `Startup.ConfigureServices`.
* **Service Lifetimes:** Choose appropriate service lifetimes:
    * **Singleton:** One instance for the application lifetime. Use for stateless services or state that must be shared globally (use with caution regarding concurrency).
    * **Scoped:** One instance per request (in web applications). Suitable for services like `DbContext`, unit-of-work patterns, or request-specific state.
    * **Transient:** A new instance is created every time it's requested. Use for lightweight, stateless services.
* **Constructor Injection:** Prefer constructor injection for mandatory dependencies. This makes dependencies explicit and ensures the class cannot be constructed without them.
* **Avoid Service Locator:** Do not use the Service Locator anti-pattern (injecting `IServiceProvider` into classes to resolve dependencies manually). Let the DI container resolve dependencies via constructors.
* **Dispose `IDisposable`:** The DI container automatically disposes `IDisposable` services it creates (Singleton and Scoped). Transient `IDisposable` services resolved from the root container are *not* disposed automatically; use the factory pattern or resolve within a scope if disposal is needed.

## Middleware

* **Purpose:** Use middleware to handle cross-cutting concerns affecting the HTTP request/response pipeline (e.g., exception handling, authentication, authorization, logging, routing, static files, CORS, compression, caching, security headers).
* **Order Matters:** Register middleware in `Program.cs` / `Startup.Configure` in the correct order, as it executes sequentially. Key ordering considerations:
    * Exception Handling (`UseExceptionHandler`, `UseDeveloperExceptionPage`) early.
    * HSTS (`UseHsts`), HTTPS Redirection (`UseHttpsRedirection`) early for security.
    * Static Files (`UseStaticFiles`) early to short-circuit requests for static content.
    * Routing (`UseRouting`) before endpoint selection/authorization.
    * CORS (`UseCors`) before components that need it (e.g., before Auth, Routing).
    * Authentication (`UseAuthentication`) before Authorization (`UseAuthorization`).
    * Endpoint Execution (`UseEndpoints`) usually last.
* **Keep Middleware Lightweight:** Middleware should execute quickly and have a single responsibility. Avoid long-running or blocking operations directly in middleware.
* **Custom Middleware:** Write custom middleware for application-specific cross-cutting concerns. Encapsulate registration logic in extension methods (`public static IApplicationBuilder UseMyMiddleware(this IApplicationBuilder builder)`).
* **Use Built-in Middleware:** Leverage built-in middleware provided by the framework where possible (e.g., `UseAuthentication`, `UseAuthorization`, `UseStaticFiles`, `UseResponseCaching`, `UseResponseCompression`).

## Routing

* **Choose Strategy:** Select an appropriate routing strategy:
    * **Attribute Routing:** (`[Route("api/[controller]")]`, `[HttpGet("{id}")]`) Preferred for APIs. Defines routes directly on controllers and actions.
    * **Conventional Routing:** (`MapControllerRoute(...)`) Often used for MVC UI applications. Defines route templates mapping URL patterns to controller/action names.
    * **Minimal APIs:** (`app.MapGet(...)`, etc.) Defines routes and handlers directly in `Program.cs` or related files. Suitable for smaller APIs or microservices.
* **RESTful URLs:** Design API routes following REST principles (resource-based nouns, standard HTTP verbs).
* **Route Parameters & Constraints:** Use route parameters (`{id}`) to capture URL segments and route constraints (`{id:int}`) to enforce data types.
* **URL Generation:** Use URL helpers (`Url.Action`, `Url.RouteUrl`, `LinkGenerator`) for generating URLs to avoid hardcoding paths.

## Controllers (MVC / API) & Actions / Minimal API Endpoints

* **Naming:** Follow conventions: `PascalCase` ending with `Controller` (e.g., `ProductsController`). Action methods are `PascalCase`.
* **Skinny Controllers:** Controllers/Endpoints should primarily handle HTTP concerns: parsing requests, validating input, calling application/domain services, mapping results to responses (DTOs), and returning appropriate HTTP status codes. Keep business logic out of controllers.
* **Return Types:**
    * Use `IActionResult` (or specific results like `Ok()`, `NotFound()`, `BadRequest()`) in controllers for flexibility in returning different status codes and response types.
    * Minimal APIs often use `Results` static class (e.g., `Results.Ok()`, `Results.NotFound()`).
* **Model Binding:** Utilize model binding to automatically map request data (route parameters, query string, request body) to action method parameters (simple types, complex objects/DTOs).
* **Validation:**
    * Use Data Annotations (`[Required]`, `[MaxLength]`, etc.) or FluentValidation on request DTOs.
    * Check `ModelState.IsValid` in controller actions or use `[ApiController]` attribute (for APIs) which triggers automatic 400 responses for invalid models.
* **Async Actions:** Make controller actions/endpoint handlers `async Task<IActionResult>` (or `async Task<IResult>`) if they perform I/O-bound operations (database calls, HTTP requests) and use `await`.

## Security

* **HTTPS & HSTS:** Enforce HTTPS redirection (`UseHttpsRedirection()`) and HSTS (`UseHsts()`) in production.
* **Authentication:** Implement appropriate authentication (ASP.NET Core Identity for web apps, JWT Bearer tokens for APIs, OpenID Connect, etc.). Use `UseAuthentication()`.
* **Authorization:** Implement authorization using `[Authorize]` attributes, roles, or policy-based authorization. Use `UseAuthorization()`.
* **Secrets Management:** Securely manage secrets (see Configuration section).
* **Data Protection:** Leverage the Data Protection API for encrypting sensitive data like tokens or cookies if needed (often used implicitly by framework features like authentication).
* **XSS Prevention:** Rely on Razor's/Blazor's automatic HTML encoding. Be careful when rendering raw HTML (`Html.Raw`). Sanitize user input intended for display if necessary.
* **CSRF Prevention:** Use anti-forgery tokens (`@Html.AntiForgeryToken()` in forms, validate via `[ValidateAntiForgeryToken]` attribute) for web applications using cookies/sessions. APIs using token-based auth (JWT) are typically not vulnerable to CSRF.
* **Security Headers:** Use `Helmet` (via middleware like `app.UseHelmet()` if using a third-party package) or configure headers manually/via policies to enhance security (e.g., `Content-Security-Policy`, `X-Content-Type-Options`, `Referrer-Policy`).
* **CORS:** Configure CORS (`UseCors()`) carefully in APIs, allowing only specific origins, methods, and headers needed by legitimate clients in production. Avoid allowing `*` for origins in production.
* **Input Validation:** Validate all input thoroughly (see Controllers section).
* **SQL Injection:** Use EF Core or other ORMs/micro-ORMs (like Dapper) with parameterized queries (see EF Core section).

## Performance

* **Asynchronous Programming:** Use `async/await` for all I/O operations (see Async section).
* **Caching:** Implement response caching (`UseResponseCaching`, `[ResponseCache]`), in-memory caching (`IMemoryCache`), or distributed caching (`IDistributedCache` with Redis/SQL Server/etc.) to reduce load and latency for frequently accessed or expensive data.
* **Compression:** Enable response compression (`UseResponseCompression`) using Gzip/Brotli.
* **Optimize Data Access:** Minimize database roundtrips, retrieve only necessary data, use no-tracking queries for read-only scenarios (see EF Core section).
* **Logging:** Configure logging efficiently. Avoid excessive logging in performance-sensitive paths. Use appropriate log levels. Consider asynchronous logging sinks.
* **Minimize Middleware:** Only include necessary middleware in the pipeline.
* **Static Files:** Serve static files efficiently (`UseStaticFiles`). Consider using a CDN for high-traffic sites.
* **Profiling:** Use .NET profiling tools (Visual Studio Profiler, `dotnet-trace`, `dotnet-counters`, Application Insights) to identify and address performance bottlenecks.

## Entity Framework Core (If Used)

* **DbContext Registration:** Register `DbContext` with a scoped lifetime in DI (`services.AddDbContext<ApplicationDbContext>(...)`).
* **Async Database Operations:** Use async methods (`SaveChangesAsync`, `ToListAsync`, `FirstOrDefaultAsync`, etc.) in async controller actions/methods.
* **Query Optimization:**
    * **Projections:** Use `Select()` to retrieve only necessary properties/columns.
    * **Eager Loading:** Use `Include()` and `ThenInclude()` to load related entities in a single query, avoiding N+1 query problems.
    * **Explicit Loading:** Use `Load()` or `LoadAsync()` when you need to load related entities after the parent entity is already loaded.
    * **Tracking:** Use `AsNoTracking()` for read-only queries to improve performance by skipping change tracking.
    * **Filtering:** Apply filters in the database layer (`Where()`) rather than retrieving large datasets and filtering in memory.
* **Migrations:** Use EF Core Migrations to manage database schema changes. Review migration scripts before applying to production.
* **Pooling:** Consider `AddDbContextPool<>()` for high-throughput applications to reuse DbContext instances.

## Testing

* **Unit Testing:** Unit test services, logic classes, and utility functions. Use mocking frameworks (Moq, NSubstitute) for dependencies.
* **Integration Testing:** Use `WebApplicationFactory<TEntryPoint>` for in-memory integration testing of the ASP.NET Core application pipeline (middleware, routing, controllers, validation). Use test doubles or test databases for external dependencies like databases.
* **Test Database:** Use a separate test database (or in-memory provider for simple tests) to avoid affecting production/development data.
* **Test Environment:** Configure a specific test environment (`ASPNETCORE_ENVIRONMENT=Testing`) with appropriate settings.
* **Authentication/Authorization Testing:** Test both authenticated and unauthenticated scenarios. For integration tests, mock authentication using test authentication handlers.
* **API Testing:** Test API endpoints for correct status codes, response formats, validation behavior, and edge cases.
* **Mock External Dependencies:** Use mocks or stubs for external services, APIs, or databases that are not part of the integration test scope.

## Logging & Monitoring

* **Structured Logging:** Use structured logging with appropriate log levels. Configure logging in `Program.cs` using `ConfigureLogging()` or builder methods.
* **Logging Providers:** Configure appropriate logging providers (Console, Debug, ApplicationInsights, Serilog, NLog, etc.).
* **Include Context:** Include contextual information in logs (request IDs, user IDs, correlation IDs).
* **Health Checks:** Implement health checks (`AddHealthChecks()`) for the application and its dependencies.
* **Application Insights:** Consider using Application Insights or similar APM tools for comprehensive monitoring, especially in production.
* **Performance Counters:** Monitor key metrics (request rates, response times, error rates, resource usage).

## Deployment & CI/CD

* **Environment Configuration:** Configure applications differently for different environments (Development, Staging, Production) using environment-specific settings and the `ASPNETCORE_ENVIRONMENT` variable.
* **Docker Support:** Consider containerizing applications using Docker for consistent deployment across environments.
* **CI/CD Pipelines:** Implement CI/CD pipelines to automate build, test, and deployment processes.
* **Release Management:** Plan for zero-downtime deployments and rollback strategies.
* **Infrastructure as Code:** Use infrastructure as code tools (Terraform, ARM templates, etc.) to define and provision infrastructure.
* **Logging & Monitoring Setup:** Ensure proper logging and monitoring is set up for production deployments.
