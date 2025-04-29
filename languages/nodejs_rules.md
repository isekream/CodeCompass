# Node.js Rules

## Project Structure & Conventions

* **Modular Structure:** Organize code into logical modules/components (e.g., by feature or layer). Consider a structure like `src/{routes, controllers, services, models, middleware, config, utils}`.
* **`package.json`:** Initialize projects with `npm init` or `yarn init`. Keep `package.json` updated with accurate project information, dependencies, and scripts. Define the specific Node.js engine version required using the `engines` key.
* **Entry Point:** Clearly define the application entry point (commonly `server.js`, `app.js`, or `index.js`).
* **Separation:** Separate the 'server' setup (HTTP server initialization, port listening) from the 'app' definition (Express/framework setup, middleware, routes) for better testability.
* **Configuration:**
    * Use environment variables for configuration, especially for secrets and environment-specific settings (database URLs, API keys, ports).
    * Use libraries like `dotenv` to load environment variables from a `.env` file during local development (ensure `.env` is in `.gitignore`).
    * Consider hierarchical configuration files (e.g., using `config` package) for managing different environments, but prioritize environment variables.
* **Naming:**
    * Use `camelCase` for variables, functions, and instance properties.
    * Use `PascalCase` for classes and constructors.
    * Use `UPPERCASE_SNAKE_CASE` for constants.
    * Use lowercase filenames (e.g., `user-controller.js`).

## Asynchronous Programming

* **Prefer `async/await`:** Use `async/await` for handling asynchronous operations over callbacks or raw Promises for improved readability and maintainability.
* **Error Handling:** Always use `try...catch` blocks within `async` functions to handle potential errors from `await`ed Promises. Avoid unhandled promise rejections.
* **Parallel Execution:** Use `Promise.all()` or `Promise.allSettled()` to run independent asynchronous operations concurrently when appropriate, rather than awaiting them sequentially.
* **Avoid Blocking Event Loop:** Never use synchronous, CPU-intensive operations (complex calculations, synchronous I/O like `fs.readFileSync` in request handlers, heavy regex) directly in the main event loop thread, as they block other requests. Offload CPU-intensive tasks to worker threads or background jobs.

## Error Handling

* **Use Built-in `Error` Object:** Throw or pass instances of the built-in `Error` object or custom error classes inheriting from `Error`. Avoid throwing strings or plain objects.
* **Operational vs. Programmer Errors:** Distinguish between operational errors (expected runtime issues, e.g., invalid input, connection failure) and programmer errors (bugs in the code). Operational errors should generally be handled gracefully (e.g., return HTTP error code), while programmer errors might warrant crashing the process (see below).
* **Centralized Error Handling:** Implement centralized error handling middleware (e.g., in Express) to catch errors propagated via `next(err)` and send consistent, user-friendly error responses. Do not leak stack traces or sensitive details to the client in production.
* **Handle `uncaughtException` & `unhandledRejection`:**
    * Register listeners for `process.on('uncaughtException', ...)` and `process.on('unhandledRejection', ...)`.
    * Log the error robustly.
    * Perform necessary cleanup (e.g., close database connections).
    * **Crucially:** Gracefully shut down the process after catching these. Continuing after an uncaught exception or unhandled rejection can leave the application in an unknown, potentially corrupted state. Use a process manager (like PM2, nodemon) or orchestrator (Kubernetes) to restart the process automatically.
* **Async Error Propagation:** Ensure errors from asynchronous operations (callbacks, Promises) are correctly propagated to the central error handler (e.g., pass `err` to `next(err)` in Express middleware, `reject(err)` in Promises).

## Code Style & Conventions (Extends JS/TS Rules)

* **Use Linters & Formatters:** Enforce code style and catch potential errors using tools like ESLint and Prettier with agreed-upon configurations. Integrate into CI.
* **`const` over `let`:** Use `const` by default; use `let` only when reassignment is necessary. Avoid `var`.
* **Strict Equality:** Always use strict equality (`===`, `!==`).
* **Module Imports:** Use `require` (CommonJS) or `import` (ES Modules) consistently based on project setup (`type: "module"` in `package.json` for ES Modules). Require/import modules at the top of the file, not inside functions.
* **Function Naming:** Name functions (including callbacks and anonymous functions assigned to variables) for clearer stack traces.

## Performance

* **Non-Blocking I/O:** Leverage Node.js's non-blocking I/O model. Use asynchronous APIs for database operations, file system access, network calls, etc.
* **Caching:** Implement caching (e.g., using in-memory caches like `node-cache` or external caches like Redis, Memcached) for frequently accessed data to reduce latency and load on databases or external services.
* **Streams:** Use Streams for handling large amounts of data (e.g., file uploads/downloads, data processing) to avoid buffering large payloads entirely into memory.
* **Database Optimization:** Optimize database queries (use indexes, select only necessary fields, avoid N+1 problems). Use connection pooling.
* **Load Balancing:** Use a load balancer (like Nginx or a cloud load balancer) to distribute traffic across multiple Node.js instances for scalability and availability.
* **Clustering:** Utilize the built-in `cluster` module or tools like PM2 to run multiple Node.js processes on multi-core systems, taking advantage of all available CPU cores.
* **Profiling:** Use Node.js's built-in profiler (`--prof`) or APM tools (Datadog, New Relic) to identify performance bottlenecks (CPU-intensive functions, memory leaks).
* **Memory Management:** Monitor memory usage. Be mindful of potential memory leaks (e.g., unremoved event listeners, growing global caches, closures holding references). Use heap dumps and memory profiling tools to diagnose leaks. Increase default memory limit (`--max-old-space-size`) only if necessary and understood.

## Security (Extends General Security Rules)

* **Dependency Management:**
    * Keep dependencies updated using `npm update` or `yarn upgrade`.
    * Regularly audit dependencies for known vulnerabilities using `npm audit` or tools like Snyk. Integrate scans into CI.
    * Use `package-lock.json` or `yarn.lock` to lock dependency versions for reproducible builds. Commit this lock file.
* **Input Validation:** Validate and sanitize ALL incoming data (request bodies, query parameters, headers) to prevent injection attacks (SQLi, NoSQLi, XSS, command injection). Use validation libraries (e.g., Joi, express-validator).
* **Output Encoding:** Properly encode output displayed in HTML contexts to prevent XSS. Use templating engines that provide auto-escaping.
* **Rate Limiting & Payload Size:** Implement rate limiting on APIs to prevent DoS attacks and abuse. Limit request payload sizes (e.g., using `express.json({ limit: '10kb' })`).
* **Security Headers:** Use security-focused HTTP headers (e.g., using `helmet` middleware for Express) like `Strict-Transport-Security`, `X-Content-Type-Options`, `X-Frame-Options`, `Content-Security-Policy`.
* **Authentication & Authorization:** Implement robust authentication and authorization mechanisms. Use standard libraries/frameworks (e.g., Passport.js).
* **Secrets Management:** Do not hardcode secrets. Use environment variables or dedicated secrets management solutions.
* **Prevent Query Injection:** Use parameterized queries or ORMs/ODMs that properly handle query building to prevent SQL/NoSQL injection.
* **CSRF Protection:** For web applications with sessions/cookies, implement CSRF protection (e.g., using `csurf` middleware).
* **Avoid Dangerous Functions:** Avoid using dangerous functions like `eval()` or `child_process.exec()` with unsanitized user input.

## Testing

* **Test Types:** Write unit, integration, and end-to-end (E2E) tests.
* **Frameworks:** Use established testing frameworks (e.g., Jest, Mocha, Chai, Sinon).
* **Coverage:** Aim for high test coverage, particularly for critical business logic and complex functions. Use tools like Istanbul/nyc to measure coverage.
* **Automation:** Automate test execution in the CI pipeline.
* **Mocking/Stubbing:** Use mocking and stubbing libraries (e.g., Sinon.js, Jest mocks) to isolate components during unit testing.

## Best Practices for Common Node.js Frameworks

### Express.js

* **Routing:** Organize routes using Express Router. Group related routes and mount them at appropriate paths.
* **Middleware:** Use middleware for cross-cutting concerns (auth, logging, error handling). Order middleware carefully.
* **Error Handling:** Implement centralized error handling middleware. Handle both synchronous and asynchronous errors.
* **Validation:** Validate request data using middleware (express-validator, Joi).
* **Security:** Use helmet for security headers. Apply appropriate CORS configuration.

### Nest.js

* **Architecture:** Follow the Nest.js modular architecture with decorators.
* **Dependency Injection:** Use the built-in DI container effectively.
* **Exception Filters:** Implement custom exception filters for error handling.
* **Interceptors & Pipes:** Use interceptors for cross-cutting concerns and pipes for validation.
* **Testing:** Take advantage of Nest.js testing utilities for easy mocking and testing.

## Deployment & Operations

* **Process Management:** Use process managers (PM2, Forever) for production deployments to handle crashes, logs, and clustering.
* **Health Checks:** Implement health check endpoints to monitor application status.
* **Logging:** Use structured logging (Winston, Pino) with appropriate log levels. Consider log aggregation services.
* **Monitoring:** Implement application monitoring (metrics, error tracking) using APM tools.
* **Docker:** Containerize Node.js applications using optimized Docker images and multi-stage builds.
* **CI/CD:** Set up continuous integration and deployment pipelines that include testing, linting, and vulnerability scanning.

## Common Antipatterns to Avoid

* **Callback Hell:** Nested callbacks making code hard to read and maintain. Use async/await instead.
* **Massive Files:** Monolithic files with too many responsibilities. Split into smaller, focused modules.
* **Synchronous Blocking Operations:** Blocking the event loop with synchronous operations.
* **Unhandled Promise Rejections:** Not properly catching and handling promise errors.
* **Magic Strings/Numbers:** Hardcoded values scattered throughout the codebase. Use constants and configuration.
* **Manual CORS Implementation:** Implementing CORS manually which can lead to security issues. Use the cors package.
* **Insecure Default Settings:** Not configuring security headers, request size limits, etc.
