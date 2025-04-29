# PHP Rules

## Coding Standards (PSR)

* **Follow PSRs:** Adhere to the widely adopted PHP Standards Recommendations (PSRs) maintained by PHP-FIG for consistency and interoperability. Key PSRs include:
    * **PSR-1 (Basic Coding Standard):**
        * Use only `<?php` and `<?= ` tags.
        * Use UTF-8 without BOM for PHP files.
        * Files should either declare symbols (classes, functions, constants) OR cause side-effects, but not both.
        * Use namespaces.
        * Class names MUST be declared in `PascalCase` (StudlyCaps).
        * Class constants MUST be declared in `UPPER_CASE` with underscore separators.
        * Method names MUST be declared in `camelCase`.
    * **PSR-12 (Extended Coding Style):** (Supersedes PSR-2)
        * Follows PSR-1.
        * Use 4 spaces for indentation, not tabs.
        * No hard limit on line length, but aim for a soft limit of 120 characters, ideally 80.
        * One blank line after `namespace` and `use` blocks.
        * Opening braces for classes and methods go on the next line; closing braces go on the line after the body.
        * Opening braces for control structures (`if`, `switch`, `for`, `foreach`, `while`, `try...catch`) go on the same line; closing braces go on the line after the body.
        * Visibility (`public`, `protected`, `private`) MUST be declared on all properties and methods. `abstract` and `final` MUST precede visibility; `static` MUST follow visibility.
        * Keywords MUST be lowercase (`true`, `false`, `null`).
        * Use `elseif`, not `else if`.
        * One space after control structure keywords; no space after function/method calls; no space before/after parentheses in control structures.
        * See the full PSR-12 spec for detailed rules on spacing, operators, casts, etc.
    * **PSR-4 (Autoloading Standard):** Follow PSR-4 for autoloading classes based on file paths and namespaces.
* **Tooling:** Use tools like PHP_CodeSniffer (with PSR-12 ruleset) and PHP CS Fixer to automatically check and enforce coding standards.

## Naming & Formatting (Beyond PSR-12)

* **Variables:** Use `camelCase` for variables. Choose meaningful, descriptive names. Avoid single-letter variables except for simple loop counters.
* **Constants:** Use `UPPER_SNAKE_CASE` for constants defined with `define()` or `const` outside classes (PSR-1 covers class constants).
* **Readability:** Use whitespace (blank lines) to separate logical blocks of code within methods. Keep methods focused and reasonably short.
* **Comments:**
    * Use `//` for single-line comments and `/* ... */` for multi-line or docblock comments.
    * Use PHPDoc blocks (`/** ... */`) to document classes, methods, properties, functions, constants. Document parameters (`@param`), return types (`@return`), and thrown exceptions (`@throws`).
    * Comment complex or non-obvious logic. Explain the *why*, not just the *what*. Avoid commenting obvious code.
    * Keep comments up-to-date with the code.

## Error Handling & Exceptions

* **Error Reporting:** Configure error reporting appropriately for different environments.
    * Development: `display_errors = On`, `error_reporting = E_ALL`.
    * Production: `display_errors = Off`, `log_errors = On`, `error_reporting` set to an appropriate level (e.g., `E_ALL & ~E_DEPRECATED & ~E_STRICT`). Log errors to a file or centralized logging system.
* **Exceptions:** Use exceptions for exceptional/error conditions, not for normal control flow.
    * Throw specific, meaningful exception types (either built-in SPL exceptions like `InvalidArgumentException`, `RuntimeException` or custom exception classes inheriting from `Exception` or SPL exceptions).
    * Use `try...catch` blocks to handle exceptions where recovery or specific action is possible.
    * Use `finally` blocks for essential cleanup code (e.g., closing resources) that must run regardless of whether an exception occurred (though often handled by destructors or `register_shutdown_function`).
* **Avoid `@`:** Do not use the error control operator (`@`) to suppress errors. It makes debugging harder and can hide underlying problems. Handle errors properly instead.
* **Custom Error Handler:** Consider using `set_error_handler()` to convert traditional PHP errors into exceptions for consistent handling, often done by frameworks.

## Security

* **Input Validation:** Validate and sanitize ALL external input (e.g., `$_GET`, `$_POST`, `$_COOKIE`, headers, file uploads, API inputs).
    * Use filter functions (`filter_input`, `filter_var`) with appropriate filters (e.g., `FILTER_VALIDATE_EMAIL`, `FILTER_VALIDATE_INT`).
    * Check data types, lengths, formats, and ranges.
    * Use allow-lists (whitelisting) over block-lists (blacklisting) where possible.
* **Output Encoding:** Properly encode ALL output displayed in HTML/JS contexts to prevent Cross-Site Scripting (XSS). Use functions like `htmlspecialchars()` with appropriate flags (e.g., `ENT_QUOTES | ENT_HTML5`) or templating engines with automatic contextual escaping (e.g., Twig).
* **SQL Injection:** Use prepared statements (with PDO or MySQLi) with bound parameters. NEVER construct SQL queries by concatenating user input directly.
* **CSRF Protection:** Implement Cross-Site Request Forgery (CSRF) protection for state-changing requests (e.g., forms submitted via POST). Use anti-CSRF tokens synchronized with the user session.
* **File Uploads:** Validate uploaded files rigorously: check file type (using server-side checks like MIME type or `finfo`, not just the extension), size limits, and sanitize filenames. Store uploaded files outside the web root or with restricted execution permissions.
* **Session Management:** Use secure session handling practices. Regenerate session IDs upon login/logout. Configure session cookies with `HttpOnly` and `Secure` flags (if using HTTPS). Store minimal sensitive data in sessions.
* **Password Hashing:** Store passwords using modern, strong, salted hashing algorithms like Argon2id (`password_hash()` with `PASSWORD_ARGON2ID`) or bcrypt (`PASSWORD_BCRYPT`). Use `password_verify()` to check passwords.
* **HTTPS:** Always use HTTPS for secure data transmission.
* **Dependency Security:** Use Composer to manage dependencies. Regularly run `composer audit` or use security scanning tools to check for known vulnerabilities in dependencies. Keep dependencies updated.
* **Configuration:** Do not store sensitive credentials (database passwords, API keys) directly in code. Use environment variables (e.g., via `.env` files loaded securely) or secrets management systems. Disable dangerous PHP functions (`disable_functions` in `php.ini`) if not needed.
* **File Permissions:** Set appropriate, restrictive file and directory permissions on the server.

## Performance

* **Use Latest PHP Version:** Use a recent, actively supported PHP version for significant performance improvements and security fixes.
* **Opcode Caching:** Ensure an opcode cache (like OPcache, usually enabled by default) is active and properly configured in production to avoid recompiling PHP scripts on every request.
* **Database Queries:** Optimize database interactions. Use indexing effectively, select only necessary columns, avoid queries inside loops (N+1 problem), use caching where appropriate.
* **Caching:** Implement caching strategies (opcode cache, data cache using Redis/Memcached, application cache, HTTP caching headers) for frequently accessed data or computationally expensive operations.
* **Avoid Functions in Loops:** Avoid calling functions (especially expensive ones like `count()` on arrays) inside loop conditions if the result doesn't change. Calculate the value before the loop.
* **Use Built-in Functions:** Prefer built-in PHP functions over custom implementations where possible, as they are often implemented in C and highly optimized.
* **String Concatenation:** For complex string building, especially in loops, consider alternatives like `sprintf` or collecting parts in an array and using `implode` at the end, rather than repeated concatenation (`.`).
* **Lazy Loading:** Load resources (classes, data) only when needed (autoloading helps here).
* **Profiling:** Use profiling tools (e.g., Xdebug profiler, Blackfire.io) to identify performance bottlenecks in the code before optimizing prematurely.

## Dependency Management (Composer)

* **Use Composer:** Manage all project dependencies using Composer.
* **`composer.json`:** Define dependencies clearly with appropriate version constraints. Prefer caret (`^1.2.3`) or tilde (`~1.2.3`) for libraries following SemVer. Use specific versions for applications to ensure stability.
* **`composer.lock`:** Always commit the `composer.lock` file to version control for applications to guarantee consistent dependency versions across all environments (dev, CI, production). Libraries may or may not commit the lock file.
* **Autoloading:** Configure PSR-4 autoloading in `composer.json` for your application's namespaces.
* **Scripts:** Use Composer scripts (`scripts` section in `composer.json`) to automate common tasks (e.g., running tests, clearing cache, code quality checks).
* **Audit:** Run `composer audit` regularly and in CI pipelines to check for known security vulnerabilities in dependencies.
* **Update:** Update dependencies deliberately (`composer update`) and test thoroughly afterward. Avoid running `composer update` blindly in production deployment scripts. Use `composer install` in CI/deployment to install exact versions from `composer.lock`.

## PHP 7/8 Features

* **Type Declarations:** Use strict type declarations (with `declare(strict_types=1);`), parameter and return type hints, and property types to improve code clarity, catch errors early, and enhance IDE support.
* **Nullable Types:** Use nullable types (`?string`) for parameters, return types, and properties that can be null.
* **Constructor Property Promotion:** Use constructor property promotion (PHP 8+) to reduce boilerplate code for simple class declarations.
* **Match Expression:** Prefer `match` expressions (PHP 8+) over `switch` statements where appropriate for more concise and type-safe comparisons.
* **Named Arguments:** Use named arguments (PHP 8+) for improved readability in function/method calls with many parameters or for skipping optional parameters.
* **Attributes:** Use attributes (PHP 8+) instead of docblock annotations where supported by frameworks and libraries.
* **Null Coalescing Operator:** Use the null coalescing operator (`??`) and its assignment form (`??=`) for concise null checks.
* **Array Unpacking:** Use the spread operator (`...`) for array unpacking in function calls and array definitions.

## General Principles

* **Readability:** Write clear, understandable code. Use meaningful names for variables, functions, and classes.
* **DRY (Don't Repeat Yourself):** Avoid code duplication. Extract common logic into reusable functions, methods, classes, or traits.
* **KISS (Keep It Simple, Stupid):** Prefer simple, straightforward solutions over complex ones.
* **OOP Principles:** Follow SOLID principles where applicable when writing object-oriented PHP. Favor composition over inheritance.
* **Frameworks:** Consider using a well-established PHP framework (e.g., Laravel, Symfony) for larger applications to provide structure, common components, and security features. Follow the framework's specific best practices.
