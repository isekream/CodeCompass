# Express.js Rules

## Project Structure

* **Separation of Concerns:** Organize the project into logical directories based on responsibility. A common structure includes:
    * `src/` (or `app/`): Main application source code.
        * `config/`: Configuration loading and management (e.g., database connection strings, external API keys, loaded via environment variables).
        * `controllers/`: Request handlers containing the main logic for processing requests and sending responses. Interacts with services.
        * `routes/`: Defines API endpoints and maps them to controller functions. Uses `express.Router()`.
        * `services/`: Contains business logic, coordinating between controllers and data access layers/models.
        * `models/`: Data definitions (e.g., Mongoose schemas, Sequelize models) and potentially data access logic (repositories).
        * `middleware/`: Custom middleware functions (e.g., authentication, logging, validation).
        * `utils/`: Shared utility functions.
    * `tests/`: Unit and integration tests.
    * `public/`: Static assets (CSS, JS, images) if serving directly from Express.
    * `views/`: Template files if using server-side rendering (e.g., Pug, EJS).
* **Entry Point (`app.js` / `server.js`):** Keep the main application entry point (`app.js` or `server.js`) clean. Focus on initializing the Express app, loading middleware, mounting routers, and starting the server. Separate server creation (`http.createServer(app)`) from the app definition (`app`) for better testability.
* **Configuration:** Load configuration from environment variables (using `.env` files via `dotenv` for local development) and centralize access via a `config` module. Do not hardcode configuration values.

## Routing

* **`express.Router`:** Use `express.Router()` to group related routes into modular files (e.g., `userRoutes.js`, `productRoutes.js`). Mount these routers onto specific base paths in the main `app.js` file (e.g., `app.use('/api/v1/users', userRoutes);`).
* **HTTP Verbs:** Use appropriate HTTP verbs for actions (`GET` for retrieval, `POST` for creation, `PUT`/`PATCH` for updates, `DELETE` for removal).
* **Route Paths:** Use clear, consistent, and resource-oriented route paths (e.g., `/users`, `/users/:id`, `/products/:productId/reviews`).
* **Keep Routes Lean:** Route handlers should primarily parse requests, call service functions for business logic, and format responses. Avoid putting complex business logic directly in route handlers.

## Middleware

* **Purpose:** Use middleware for cross-cutting concerns like logging, authentication, authorization, request validation, response compression, security headers, and error handling.
* **Order Matters:** The order in which middleware is applied using `app.use()` or `router.use()` is critical. Place middleware like logging, security headers (`helmet`), body parsing (`express.json()`, `express.urlencoded()`), and compression (`compression`) early in the stack. Authentication middleware should come before authorization and protected routes. Error handling middleware must be defined *last*.
* **Single Responsibility:** Keep middleware functions small and focused on a single task.
* **Asynchronous Middleware:** If middleware performs asynchronous operations (e.g., database checks), ensure it's an `async` function and uses `await` with `try...catch` or handles Promise rejections correctly, passing errors to `next(err)`. Consider using `express-async-errors` to automatically handle errors in async middleware/routes.
* **`next()`:** Always call `next()` to pass control to the next middleware in the stack, unless the middleware terminates the request-response cycle (e.g., by sending a response or an error). Call `next(err)` to pass control to the error handling middleware.
* **Third-Party Middleware:** Leverage well-maintained third-party middleware for common tasks (e.g., `helmet` for security headers, `cors` for CORS, `morgan` for HTTP request logging, `compression` for response compression, `express-rate-limit` for rate limiting).

## Error Handling

* **Centralized Error Handler:** Implement a centralized error-handling middleware function defined with four arguments (`(err, req, res, next)`). This middleware must be added *after* all other `app.use()` and route calls.
    ```javascript
    app.use((err, req, res, next) => {
      console.error(err.stack); // Log the error stack trace
      // Determine status code (use error's status or default to 500)
      const statusCode = err.statusCode || 500;
      // Send generic error response in production
      const message = process.env.NODE_ENV === 'production' ? 'Something went wrong!' : err.message;
      res.status(statusCode).json({ error: message });
    });
    ```
* **Pass Errors with `next(err)`:** In route handlers and other middleware, pass errors to the centralized handler by calling `next(err)`. Do *not* handle errors locally if they should result in an error response.
* **Async Error Handling:** For asynchronous code (callbacks or Promises without `express-async-errors`), explicitly catch errors and pass them to `next(err)`. With `async/await` (and `express-async-errors`), simply throwing an error or rejecting a promise will be handled.
* **Custom Error Classes:** Consider creating custom error classes extending `Error` (e.g., `NotFoundError`, `ValidationError`) with additional properties like `statusCode` to allow the centralized handler to respond appropriately.
* **Operational Errors:** Use the centralized handler primarily for operational errors (e.g., resource not found, invalid input).
* **Uncaught Exceptions/Rejections:** Implement global handlers using `process.on('uncaughtException', ...)` and `process.on('unhandledRejection', ...)` to log fatal errors and gracefully shut down the application (see Node.js rules). Do not attempt to resume operation after these events.

## Security

* **Use Helmet:** Use the `helmet` middleware to set various security-related HTTP headers (e.g., `X-Frame-Options`, `Strict-Transport-Security`, `X-Content-Type-Options`).
* **Rate Limiting:** Implement rate limiting using `express-rate-limit` or similar middleware to protect against brute-force attacks and DoS.
* **Input Validation:** Validate and sanitize all incoming request data (body, query parameters, path parameters, headers) using libraries like `express-validator` or `joi`. Reject requests with invalid data early.
* **Prevent Injection:** Use parameterized queries or ORMs/ODMs to prevent SQL/NoSQL injection. Sanitize input used in OS commands or file paths.
* **Authentication & Authorization:** Implement robust authentication (e.g., using Passport.js with strategies like JWT, OAuth, sessions) and authorization (middleware checking roles/permissions) for protected routes.
* **CSRF Protection:** If using sessions/cookies for web applications, implement CSRF protection using middleware like `csurf`.
* **HTTPS:** Enforce HTTPS in production environments (`app.use(https://...)`, configure reverse proxy).
* **CORS:** Configure Cross-Origin Resource Sharing using the `cors` middleware appropriately. Restrict allowed origins in production.
* **Secrets Management:** Do not hardcode secrets. Use environment variables or secret management tools.
* **Dependency Security:** Regularly audit dependencies (`npm audit`) and keep them updated.

## Performance

* **Gzip Compression:** Use the `compression` middleware to compress HTTP responses (Gzip/Brotli).
* **Caching:** Implement caching strategies (HTTP response caching headers, in-memory cache, Redis/Memcached) where appropriate.
* **Asynchronous Operations:** Use async I/O operations to avoid blocking the event loop (see Node.js rules).
* **Static Assets:** Serve static files efficiently using `express.static`. For high-traffic sites, consider using a CDN or a reverse proxy (like Nginx) to serve static assets.
* **Logging:** Use efficient logging libraries (like `pino` or `winston` with proper configuration) and avoid excessive logging in production. Log asynchronously if possible.
* **Minimize Middleware:** Only use middleware that is necessary for a given route or application. Avoid applying heavy middleware globally if it's only needed for specific endpoints.

## API Design

* **API Versioning:** Include API version in the URL path (e.g., `/api/v1/users`) or via a header to maintain backward compatibility.
* **Standardized Responses:** Use consistent response formats across the API. For example:
    ```javascript
    // Success response
    {
      "success": true,
      "data": { /* response data */ }
    }
    
    // Error response
    {
      "success": false,
      "error": {
        "code": "VALIDATION_ERROR",
        "message": "Username is required"
      }
    }
    ```
* **HTTP Status Codes:** Use appropriate HTTP status codes (200 for success, 201 for creation, 400 for client errors, 401/403 for authentication/authorization errors, 404 for not found, 500 for server errors).
* **Pagination:** Implement consistent pagination for list endpoints using parameters like `page` and `limit`. Include metadata about total results and pagination in the response.
* **Filtering & Sorting:** Support filtering and sorting using query parameters. Document the supported filter/sort fields.
* **Documentation:** Document your API using tools like Swagger/OpenAPI, providing clear descriptions of endpoints, parameters, request/response examples, and error codes.

## Testing

* **Unit Tests:** Write unit tests for individual components (controllers, services, models, utility functions). Mock dependencies to isolate the code under test.
* **Integration Tests:** Write integration tests to verify that the components work together correctly (e.g., route handlers with middleware, database operations).
* **API Tests:** Test the API endpoints end-to-end, including request validation, authentication, response format, and status codes.
* **Test Framework:** Use test frameworks like Jest, Mocha, or Jasmine with assertion libraries (Chai, Jest's expect).
* **Supertest:** Use Supertest or similar libraries to test Express applications programmatically.
* **Mocks & Stubs:** Use mocks and stubs (e.g., with Sinon.js) to isolate tests from external dependencies.
* **Test Database:** Use a separate test database or an in-memory database for testing.
* **Test Coverage:** Aim for comprehensive test coverage, focusing on edge cases and error conditions.

## Production Readiness

* **Environment Variables:** Configure the application using environment variables (`process.env`) for settings that vary between environments (development, staging, production), like database connection strings, API keys, feature flags, etc.
* **Logging:** Implement structured logging with appropriate log levels. Log important events, errors, and performance metrics.
* **Monitoring:** Add health check endpoints (e.g., `/health`) and integrate with monitoring tools.
* **Process Manager:** Use a process manager like PM2 to keep the application running in production, handle restarts, and manage logs.
* **Graceful Shutdown:** Implement graceful shutdown to handle SIGTERM and SIGINT signals, closing database connections and finishing in-flight requests before exiting.
* **Clustering:** Leverage Node.js clustering or a process manager to utilize multiple CPU cores.
* **CI/CD:** Set up continuous integration and deployment pipelines, including automated testing.
* **Error Tracking:** Integrate error tracking services (like Sentry) to capture and report production errors.
