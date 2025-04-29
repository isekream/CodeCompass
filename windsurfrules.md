# Windsurf Rules

You are an expert developer who values high-quality, maintainable code that follows established best practices. These rules guide your behavior when assisting with software development tasks.

## Core Principles

- `SF`: **Simplicity First** - Always choose the simplest practicable solution. Only introduce complex patterns if clearly justified.
- `RO`: **Readability Over Cleverness** - Code must be immediately understandable for humans and machines.
- `DRY`: **Don't Repeat Yourself** - Avoid code duplication. Extract repeated logic into reusable functions or components.
- `YAGNI`: **You Aren't Gonna Need It** - Implement only the requested functionality. Do not add features based on potential future needs.
- `KISS`: **Keep It Simple, Stupid** - Prefer straightforward, maintainable solutions over complex, overly-clever ones.

## Coding Practices

- Use consistent naming conventions across projects.
- Document public APIs, interfaces, and non-obvious code.
- Write tests for all non-trivial functionality.
- Always handle errors and edge cases appropriately.
- Avoid deeply nested code structures (more than 3-4 levels).
- Limit function/method size (ideally less than 30 lines).
- Follow the principle of least surprise in API and function design.
- Prefer automated formatting and linting tools.

### Code Comments
- Write comments that explain *why* code exists or works a certain way, not just *what* it does.
- Comment complex logic, workarounds, non-obvious intent, and significant design choices.
- Avoid commenting obvious code, restating the code, or including version control metadata.
- Use documentation comments (docstrings) for all public APIs, explaining purpose, parameters, returns, and exceptions.
- Follow language-specific conventions for documentation comments (Javadoc, JSDoc, docstrings).
- Use standardized tags like `TODO`, `FIXME`, or `HACK` for code needing attention, with context and issue references.
- Keep comments updated when code changes; outdated comments are worse than no comments.
- Remove commented-out code; use version control to track previous implementations.

## Language-Specific Rules

### Python Rules
- Follow PEP 8 guidelines for all Python code.
- Use snake_case for variables and functions, PascalCase for classes.
- Use list comprehensions where appropriate instead of explicit for loops.
- Always use the with statement when opening files.
- Use built-in functions and libraries for performance.
- Never use eval() or exec() on unsanitized user input.

### Python (Data Science)
- **Project Structure:** Organize data science projects with standardized directories for data (raw/interim/processed), notebooks, source code, and models.
- **Pandas:** Prioritize vectorized operations over loops. Use method chaining for readability. Select efficient data types for memory usage. Process large datasets in chunks.
- **NumPy:** Leverage vectorized operations and broadcasting. Use built-in functions for performance. Choose appropriate dtypes to conserve memory.
- **Machine Learning:** Use scikit-learn Pipelines to prevent data leakage. Always split data before transformations. Employ cross-validation for model evaluation. Set random_state for reproducibility.
- **Performance:** Profile code to identify bottlenecks. Use Cython/Numba for critical sections. Leverage parallel processing with Dask or Joblib for large computations.
- **Validation:** Test data transformations and model logic. Ensure reproducibility with fixed random seeds and versioned dependencies.

### JavaScript/TypeScript Rules
- Use const by default; use let only for variables that need reassignment. Avoid var.
- Always use strict equality (===, !==).
- Use modern ES6+ features like arrow functions, destructuring, template literals.
- Enable strict mode in TypeScript.
- Avoid using the any type. Use unknown with type guards when necessary.
- Maintain stable object shapes for V8 optimization.

### Node.js Rules
- Organize code into logical modules (routes, controllers, services, models, middleware, config, utils).
- Prefer async/await over callbacks and raw Promises for asynchronous operations.
- Implement centralized error handling middleware and proper exception handling.
- Avoid blocking the event loop with synchronous operations or CPU-intensive tasks.
- Use environment variables for configuration and secrets management.
- Implement proper security measures including input validation, rate limiting, and security headers.
- Leverage non-blocking I/O, caching, streams, and clustering for optimal performance.
- Write comprehensive tests (unit, integration, E2E) using established frameworks.

### Rust Rules
- Use `rustfmt` and `clippy` to maintain code quality and consistent formatting.
- Follow Rust's naming conventions: snake_case for variables/functions, PascalCase for types/traits.
- Embrace Rust's ownership system; prefer borrowing (`&T`, `&mut T`) over cloning when possible.
- Use `Result<T, E>` for recoverable errors and the `?` operator for clean error propagation.
- Avoid `.unwrap()` in production code; use proper error handling patterns instead.
- Minimize `unsafe` code; when necessary, encapsulate it within safe abstractions.
- Use appropriate concurrency primitives (`Arc`, `Mutex`, message passing, async/await).
- Leverage Rust's type system to make invalid states unrepresentable at compile time.

### Go (Golang) Rules
- Format all code with `gofmt` or `goimports`; enforce this with CI checks.
- Follow Go naming conventions: exported identifiers start with uppercase, unexported with lowercase.
- Use explicit error handling with immediate error checks; avoid ignoring errors.
- Wrap errors with `fmt.Errorf("%w", err)` to add context while preserving the original error.
- Prefer concurrency via goroutines and channels over shared memory and locks when appropriate.
- Use the `context` package for cancellation, deadlines, and request-scoped values.
- Keep functions focused and small; minimize nesting with early returns.
- Write tests using table-driven patterns and the standard `testing` package.

### Java Rules
- Follow Google Java Style Guide for formatting; use 4 spaces for indentation and K&R style braces.
- Follow standard naming conventions: `PascalCase` for classes, `camelCase` for methods/variables, `SCREAMING_SNAKE_CASE` for constants.
- Prefer composition over inheritance; use interfaces to define capabilities that unrelated classes can implement.
- Use `try-with-resources` for resources implementing `AutoCloseable`; avoid `finalize()` methods.
- Make classes and members as private as possible; favor immutability with `final` fields where appropriate.
- Validate method parameters early and throw appropriate standard exceptions (`IllegalArgumentException`, etc.).
- Return `Optional<T>` to represent potentially absent results, but not for fields or parameters.
- Use concurrent collections and high-level utilities from `java.util.concurrent` for thread safety.

### C# / .NET Core Rules
- Follow Microsoft C# Coding Conventions; use 4 spaces for indentation and Allman style braces (each brace on a new line).
- Use proper naming conventions: `PascalCase` for types/methods/properties, `camelCase` for parameters/variables, prefix interfaces with `I`.
- Use `var` only when type is obvious; use properties instead of public fields for encapsulation.
- Properly handle disposable resources with `using` statements/declarations to ensure timely cleanup.
- Use async/await for I/O operations; avoid mixing blocking and async code; propagate `CancellationToken` for cancellation support.
- Embrace dependency injection; follow appropriate service lifetimes (`Singleton`, `Scoped`, `Transient`).
- Use EF Core with proper optimization techniques; prefer async methods and avoid N+1 query problems.
- Build secure applications with thorough input validation, proper secrets management, and regular dependency updates.

### PHP Rules
- Follow PHP Standards Recommendations (PSRs): PSR-1 (Basic Coding), PSR-12 (Extended Coding Style), and PSR-4 (Autoloading).
- Use 4 spaces for indentation; follow consistent brace styles (next line for classes/methods, same line for control structures).
- Enforce standards with tools like PHP_CodeSniffer and PHP CS Fixer; use Composer for dependency management.
- Validate and sanitize ALL user input using filter functions; use prepared statements with PDO or MySQLi for database queries.
- Configure proper error reporting per environment: display errors in development, log errors (but don't display) in production.
- Avoid error suppression operator (`@`); use try-catch for exception handling instead.
- Use modern PHP security features like strong password hashing with Argon2id or bcrypt (`password_hash()`/`password_verify()`).
- Leverage PHP 7/8 features: strict type declarations, property types, constructor property promotion, match expressions, attributes.

### Ruby Rules
- Follow the community Ruby Style Guide; use 2 spaces for indentation and enforce with RuboCop or similar linters.
- Use `snake_case` for variables/methods, `PascalCase` for classes/modules, and `SCREAMING_SNAKE_CASE` for constants.
- Prefer iterators with blocks (`.each`, `.map`, `.select`) over traditional `for` loops; use `{...}` for single-line blocks, `do...end` for multi-line.
- Embrace Ruby's truthiness: only `false` and `nil` are falsy; use this directly in conditionals (e.g., `if user` instead of `if user != nil`).
- Use guard clauses (early returns) at the beginning of methods to avoid deep nesting and improve readability.
- Define small, focused classes and methods; use modules for namespacing and sharing behavior (mixins).
- Rescue specific exceptions rather than general `Exception` or `StandardError`; use `ensure` blocks for cleanup code.
- Use Bundler for dependency management; specify appropriate version constraints in `Gemfile` and always commit `Gemfile.lock` for applications.

### iOS/Swift Rules
- Follow Swift API Design Guidelines and use SwiftLint/SwiftFormat for consistent style enforcement.
- Prefer `struct` (value type) over `class` (reference type) for data models where identity isn't crucial.
- Declare variables as immutable (`let`) by default; only use `var` when mutation is required.
- Handle optionals safely with optional binding (`if let`, `guard let`) or nil-coalescing (`??`); avoid force unwrapping (`!`).
- Use Swift's strong type system; avoid `Any` or `AnyObject` unless absolutely necessary.
- Prefer modern Swift concurrency (`async`/`await`, `actor`) over callback-based approaches or GCD.
- Choose appropriate UI framework (UIKit vs SwiftUI) based on project requirements and target platforms.
- Prevent memory leaks by avoiding retain cycles; use `[weak self]` in closures and `weak var delegate` for delegates.
- Perform all UI updates on the main thread/actor; offload long-running tasks to background threads.
- Use Instruments for profiling (CPU, memory, energy); profile on real devices for accurate performance measurements.

### Android/Kotlin Rules
- Prefer Kotlin over Java for new Android development; follow official Kotlin Coding Conventions and Android Kotlin Style Guide.
- Embrace Kotlin's null safety features; use non-nullable types by default and avoid force unwrapping (`!!`).
- Prefer immutable variables (`val`) and collections; use `data class` for immutable data holders.
- Use scope functions (`let`, `run`, `apply`, `also`) appropriately and extension functions for utility methods.
- Follow recommended MVVM/MVI architecture with Android Architecture Components (ViewModel, LiveData/StateFlow, Room).
- Use dependency injection frameworks like Hilt or Koin to manage dependencies and improve testability.
- Prefer Jetpack Compose for new UIs; follow unidirectional data flow and proper state management principles.
- Use Kotlin Coroutines for asynchronous operations within appropriate lifecycle-aware scopes (`viewModelScope`, `lifecycleScope`).
- Use appropriate solutions for background tasks: Coroutines for scope-bound operations, WorkManager for deferrable work.
- Secure sensitive data with Android Keystore via Jetpack Security; use proper permissions and secure IPC mechanisms.

### CSS/SCSS Rules
- Use consistent formatting with 2-space indentation and meaningful whitespace.
- Prefer classes over IDs and tag selectors for styling components.
- Follow BEM (Block, Element, Modifier) or similar naming conventions for class names.
- Limit specificity and avoid using !important except as a last resort.
- Organize SCSS files into logical folders with partials for variables, components, and layouts.
- Limit nesting depth to 2-3 levels in SCSS to avoid specificity issues and complex selectors.
- Use variables for colors, spacing, and typography to maintain consistency.
- Optimize animations by using transform and opacity properties when possible.
- Ensure sufficient color contrast and proper focus styles for accessibility.
- Apply responsive design using a mobile-first approach with flexible layouts.

### Database Rules

#### Schema Design Rules
- **Purpose Driven:** Design schemas to represent the business domain and efficiently support primary data access patterns.
- **Naming Conventions:** Use consistent naming (prefer snake_case for SQL). Use clear, descriptive names for tables (singular nouns) and columns.
- **Data Types:** Choose appropriate data types that accurately represent the domain with minimal storage requirements.
- **Keys & Relationships:** Every table must have a primary key. Use foreign keys to enforce referential integrity. Implement relationships correctly.
- **Normalization:** Start with normalized schemas (up to 3NF) and denormalize strategically only when necessary for performance.
- **Indexing:** Index foreign keys and columns used in WHERE, ORDER BY, and GROUP BY. Create composite and covering indexes for critical queries.
- **Documentation:** Maintain schema documentation with ERDs, column descriptions, and explanations of constraints and relationships.
- **Version Control:** Keep schema definitions and migration scripts in version control. Review schema changes carefully before applying them.

#### PostgreSQL Rules
- Use lowercase snake_case for all database objects (tables, columns, functions).
- Follow consistent naming conventions (plural for tables, singular for columns).
- Use appropriate data types (e.g., TIMESTAMPTZ for timestamps, TEXT or VARCHAR for strings).
- Create proper indexes for columns used in WHERE, JOIN, and ORDER BY clauses.
- Write explicit column lists rather than SELECT *.
- Use parameterized queries to prevent SQL injection.
- Implement constraints (PRIMARY KEY, FOREIGN KEY, UNIQUE, CHECK) to enforce data integrity.
- Consider performance implications of schema design and query patterns.

#### NoSQL Rules
- Select the appropriate NoSQL database type (Document, Key-Value, Column-Family, Graph) based on access patterns.
- Design schemas based on application query patterns rather than normalized entity relationships.
- Embrace denormalization where appropriate to optimize read performance and avoid complex joins.
- Use embedding for related data accessed together; use references for one-to-many relationships.
- Create indexes on frequently queried fields but avoid over-indexing.
- Choose partition keys carefully in distributed databases to ensure even data distribution.
- Implement appropriate consistency levels based on application requirements.
- Use secure authentication, encryption, and input validation to prevent NoSQL injection.

#### Caching Rules (Redis/Memcached)
- Implement caching strategically for frequently accessed, rarely changing data, not everything.
- Use consistent key naming conventions (e.g., `object-type:id:field`) with appropriate granularity.
- Choose the right caching pattern (Cache-Aside, Read-Through, Write-Through, Write-Back) based on access patterns.
- Set appropriate TTLs based on data volatility and implement explicit invalidation strategies.
- Use connection pooling, pipelining, and bulk operations to optimize cache performance.
- Monitor key metrics like hit rate, latency, memory usage, and eviction rates.
- Configure proper security with network isolation, authentication, and encryption.
- Leverage Redis data types (Hashes, Lists, Sets) when they offer more efficient operations than simple strings.

#### Supabase Rules
- Leverage Supabase features (Auth, Storage, Realtime, Edge Functions) appropriately.
- Implement Row Level Security (RLS) policies on all tables containing sensitive data.
- Use `SECURITY INVOKER` for database functions by default, with caution for `SECURITY DEFINER`.
- Utilize Edge Functions for server-side logic and third-party integrations.
- Never expose Service Role Keys in client-side code.
- Manage schema changes via migrations rather than direct dashboard edits in production.
- Consider performance, security, and maintainability in your Supabase project structure.

### Cloud Services Rules

#### AWS Rules
- Follow the multi-account strategy using AWS Organizations for environment separation.
- Apply the AWS Well-Architected Framework principles to all workloads.
- Implement the principle of least privilege for all IAM users, roles, and policies.
- Use IAM roles instead of long-term access keys for service-to-service communication.
- Enable encryption at rest and in transit for all sensitive data.
- Implement comprehensive logging and monitoring (CloudTrail, CloudWatch, GuardDuty).
- Use infrastructure as code (CloudFormation, CDK, or Terraform) for all deployments.
- Apply consistent resource tagging for cost allocation, security, and automation.
- Optimize costs through right-sizing, appropriate pricing models, and resource lifecycle management.
- Implement multi-AZ deployments for high availability and disaster recovery planning.

#### GCP Rules
- Utilize the GCP resource hierarchy (Organization > Folders > Projects) effectively for logical organization.
- Avoid primitive roles (Owner, Editor, Viewer); use predefined or custom roles with minimal permissions.
- Use dedicated service accounts for specific workloads; avoid default service accounts.
- Minimize the use of service account keys; use alternate methods like Workload Identity when possible.
- Apply VPC security best practices (firewall rules, private IPs, VPC flow logs).
- Use Customer-Managed Encryption Keys (CMEK) for sensitive data.
- Enable Cloud Audit Logs and set up appropriate log exports and retention.
- Apply labels consistently for resource organization and cost tracking.
- Leverage appropriate discount models (CUDs, SUDs, Spot VMs) to optimize costs.
- Manage GCP resources using infrastructure as code (Terraform or Deployment Manager).

#### Azure Rules
- Utilize the Azure hierarchy (Management Groups, Subscriptions, Resource Groups) effectively.
- Apply Azure Policy to enforce organizational standards and compliance requirements.
- Use RBAC with least privilege, assigning roles at the narrowest scope possible.
- Implement Privileged Identity Management (PIM) for just-in-time privileged access.
- Enforce Multi-Factor Authentication (MFA) for all users, especially administrators.
- Use Managed Identities for Azure resources instead of storing credentials.
- Enable Microsoft Defender for Cloud across subscriptions with appropriate plans.
- Implement private endpoints for PaaS services to avoid public network exposure.
- Optimize costs through right-sizing, reservations, and auto-scaling resources.
- Document and enforce consistent tagging strategies across resources.

## DevOps & Operations Guidelines

### Docker Rules
- Use minimal, trusted base images with specific version tags instead of `latest`.
- Implement multi-stage builds to separate build dependencies from runtime environment.
- Order Dockerfile instructions from least to most frequently changing to optimize layer caching.
- Run containers as non-root users and implement appropriate security controls.
- Never hardcode secrets in Dockerfiles; use build-time secrets or external secret management.
- Include health checks and appropriate metadata labels in your images.
- Regularly scan container images for vulnerabilities before deployment.
- Set appropriate resource limits for containers to prevent resource exhaustion.
- Implement proper logging and monitoring for containerized applications.
- Use docker-compose for local development with proper service segmentation and volume management.

### Kubernetes Rules
- Use namespaces to logically partition cluster resources by environment, team, or application.
- Define all resources using declarative YAML manifests stored in version control.
- Use Deployments for stateless applications and StatefulSets for stateful applications.
- Configure appropriate resource requests and limits for all containers.
- Implement Security Contexts to enforce containers running as non-root with appropriate capabilities.
- Use ConfigMaps for non-sensitive configuration and Secrets (properly managed) for sensitive data.
- Define Network Policies to control pod-to-pod communication.
- Implement RBAC with least privilege for cluster access control.
- Use Services and Ingress resources to control traffic appropriately.
- Apply Pod Disruption Budgets to ensure availability during voluntary disruptions.
- Implement proper health checks (liveness, readiness, startup probes) for all applications.
- Follow GitOps principles for cluster configuration and application deployment.

### Terraform Rules
- Organize Terraform configurations logically with standard file structure (`main.tf`, `variables.tf`, `outputs.tf`).
- Use remote state backends with locking and encryption for collaboration and security.
- Create reusable modules following standard structure with proper documentation.
- Provide descriptive names, types, and descriptions for all variables and outputs.
- Mark sensitive data with `sensitive = true` and avoid hardcoding secrets in configuration files.
- Use specific version constraints for providers and modules to ensure consistency.
- Split large infrastructures into smaller, manageable configurations (by environment or component).
- Enforce code reviews and automated validation in CI/CD pipelines before applying changes.
- Use `count` or `for_each` appropriately for resource creation patterns.
- Apply consistent naming conventions and tagging strategies across resources.
- Run security scanning tools (tfsec, checkov) to identify misconfigurations before deployment.

### CI/CD Rules
- Automate everything: builds, tests, security scanning, deployment, and rollbacks.
- Structure pipelines into logical stages (build, test, security scan, deploy) with appropriate gates.
- Use containerized build environments for consistency and reproducibility.
- Store build artifacts in dedicated repositories with proper versioning.
- Implement security scanning (SAST, SCA, container scanning) as early as possible.
- Never hardcode secrets in pipeline definitions; use appropriate secrets management.
- Promote the same immutable artifact through different environments (dev → staging → prod).
- Implement appropriate deployment strategies (rolling, blue/green, canary) based on application needs.
- Add manual approval gates before deploying to production environments.
- Monitor deployments closely for errors and performance degradation immediately after release.
- Implement proper notifications and feedback loops to development teams.

### Monitoring & Logging Rules
- Implement the three pillars of observability: logs, metrics, and traces.
- Use structured logging (preferably JSON) with standardized fields and consistent log levels.
- Never log sensitive information (passwords, API keys, PII) in plain text.
- Define meaningful metrics for resource usage, application performance, and business outcomes.
- Implement distributed tracing for microservices architectures with context propagation.
- Create actionable alerts based on symptoms (user impact) rather than just causes.
- Set alert thresholds based on historical data and SLOs, not arbitrary static values.
- Establish clear severity levels and response expectations for different types of alerts.
- Create role-based dashboards that correlate logs, metrics, and traces for troubleshooting.
- Ship logs and metrics to centralized platforms with appropriate retention policies.
- Implement anomaly detection for identifying unusual patterns in performance or security.

### Chaos Engineering Rules
- Apply chaos engineering to proactively identify weaknesses by injecting controlled failures in systems.
- Follow a scientific approach: define steady state metrics, formulate specific hypotheses, and design controlled experiments.
- Focus on realistic failure modes: resource exhaustion, network issues, instance failures, and dependency problems.
- Start with small blast radius and gradually increase scope as confidence grows; use extreme caution in production.
- Implement automated stop conditions that halt experiments if metrics degrade beyond acceptable thresholds.
- Always have a clear rollback plan and maintain control plane access during experiments.
- Use dedicated chaos tools (e.g., Gremlin, Steadybit, LitmusChaos) instead of ad-hoc scripts for safe fault injection.
- Integrate chaos practices into the development lifecycle and CI/CD pipelines for pre-production environments.
- Conduct regular "Game Day" exercises to test incident response procedures and team communication.
- Document and share experiment results to improve collective understanding and system robustness.

### MLOps Rules
- **Automation:** Automate the entire ML lifecycle (data ingestion, validation, preprocessing, training, deployment, monitoring).
- **Reproducibility:** Version everything: code, data, models, and configurations to ensure reproducible ML processes.
- **Continuous Practices:** Implement CI/CD for ML code, Continuous Training (CT) for model retraining, and Continuous Monitoring (CM) for deployed models.
- **Data Management:** Use data validation, versioning, lineage tracking, and feature stores to ensure data quality and consistency.
- **Experiment Tracking:** Log experiment parameters, metrics, and artifacts using purpose-built tools (MLflow, W&B, etc.); use a model registry for versioning.
- **Deployment Strategies:** Implement safe model deployment with shadow deployments, canary releases, or blue/green deployments.
- **Monitoring:** Track data drift, model performance, and system metrics; establish feedback loops for ground truth collection.
- **Infrastructure:** Manage infrastructure as code; use containers and orchestration tools to standardize execution environments.
- **Collaboration:** Foster cross-functional teams (data science, ML engineering, operations) with shared tools and processes.

## Framework-Specific Guidelines

### React
- Use functional components with hooks rather than class components.
- Keep components small and focused on a single responsibility.
- Extract reusable logic into custom hooks.
- Use React.memo, useMemo, and useCallback judiciously to prevent unnecessary re-renders.
- Avoid dangerouslySetInnerHTML; sanitize content if absolutely necessary.

### Tailwind CSS
- Embrace the utility-first approach with single-purpose utility classes.
- Customize the design system via tailwind.config.js, using theme.extend to maintain defaults.
- Improve readability of class lists with formatters like prettier-plugin-tailwindcss.
- Extract repeated patterns into reusable components rather than creating custom CSS classes.
- Use @apply sparingly and only for highly reusable, complex components.
- Design with a mobile-first approach, using responsive prefixes (sm:, md:, etc.) for larger screens.
- Ensure proper content configuration for effective purging of unused styles in production.
- Leverage Tailwind's state variants (hover:, focus:, dark:) for interactive styling.

### Express.js
- Organize projects with clear separation of concerns: routes, controllers, services, models, middleware, and config.
- Use `express.Router()` to group related routes into modular files mounted at specific base paths.
- Apply middleware in the correct order; security, parsing, and logging early, error handling last.
- Implement centralized error handling middleware using `(err, req, res, next)` signature.
- Validate and sanitize all input using libraries like express-validator or joi.
- Apply security best practices with helmet, CORS configuration, rate limiting, and CSRF protection.
- Keep route handlers lean; move business logic to service layer components.
- Create standardized API responses with appropriate HTTP status codes and consistent formatting.
- Design clear, resource-oriented API endpoints following RESTful principles.
- Test comprehensively with unit, integration, and end-to-end API tests.

### Ruby on Rails
- Embrace convention over configuration; follow Rails naming conventions and file structure.
- Follow "fat model, skinny controller" principle; keep controllers focused on request handling only.
- Use RESTful design with standard resource actions (`index`, `show`, `new`, `create`, `edit`, `update`, `destroy`).
- Always use Strong Parameters with `require` and `permit` to prevent mass assignment vulnerabilities.
- Define clear associations in models (`has_many`, `belongs_to`) and use appropriate dependency options.
- Create named scopes in models for common query conditions and use eager loading (`includes`) to avoid N+1 queries.
- Extract complex business logic into service objects or concerns to keep models and controllers clean.
- Minimize view logic; use helpers, presenters, or partials for reusable view components.
- Use Rails' built-in security features: CSRF protection, secure parameter filtering, and encrypted credentials.
- Implement appropriate caching strategies and move time-consuming tasks to background jobs.

### ASP.NET Core
- Structure applications in logical layers; consider Clean Architecture or Onion Architecture for larger projects.
- Leverage the built-in Dependency Injection system with appropriate service lifetimes (Singleton, Scoped, Transient).
- Apply middleware in the correct sequence; place exception handling early and endpoint execution last.
- Use the Options pattern with strongly-typed POCO classes to access configuration from multiple sources.
- Secure applications with HTTPS, proper authentication/authorization, anti-forgery tokens, and security headers.
- Keep controllers/endpoints focused on HTTP concerns; move business logic to service classes.
- Use async/await for all I/O operations with proper cancellation token support.
- Implement response caching, compression, and other performance optimizations appropriately.
- Write integration tests using WebApplicationFactory to test the complete request pipeline.
- Configure environment-specific settings using environment variables and configuration files.

### Angular
- Follow the official Angular Style Guide for file organization, naming conventions, and code structure.
- Prefer standalone components (Angular 14+) over NgModule-based architecture for better tree-shaking.
- Use `OnPush` change detection strategy for components to improve performance.
- Maintain a clear separation between smart (container) and dumb (presentational) components.
- Use services with `providedIn: 'root'` for singleton application-wide state and business logic.
- Utilize Angular's dependency injection system properly; inject dependencies via constructors.
- Prevent memory leaks by properly unsubscribing from observables using `async` pipe or `takeUntil`.
- Organize code into Core, Shared, and Feature modules/directories with clear responsibilities.
- Implement lazy loading for feature modules or standalone components to improve initial load time.
- Use RxJS operators appropriately; avoid nested subscriptions in favor of higher-order mapping operators.

### Vue.js
- Follow the official Vue Style Guide, especially Priority A and B rules.
- Use Single File Components (`.vue` files) to encapsulate template, script, and style.
- Prefer Composition API with `<script setup>` for Vue 3 projects; maintain consistency in API choice.
- Define props clearly with object syntax including type, required, default, and validator properties.
- Always provide a unique `:key` with `v-for` directives for efficient rendering.
- Avoid `v-if` on the same element as `v-for`; filter data with computed properties instead.
- Move complex template logic to computed properties or methods for better readability.
- Use component naming conventions: multi-word `PascalCase` names with prefixes for base/common components.
- Leverage directive shorthands (`:` for `v-bind`, `@` for `v-on`, `#` for `v-slot`) for cleaner templates.
- Use Pinia for state management in Vue 3 projects; implement lazy loading for routes and components.

### Svelte & SvelteKit
- Use Svelte's compiler-based approach to write concise, declarative code with minimal boilerplate.
- Leverage reactive declarations (`$: value = expression`) for derived state; avoid unnecessary recalculations.
- Declare props using `export let propName` with default values when optional (`export let count = 0`).
- Dispatch events with `createEventDispatcher`; use `on:event-name` for forwarding events from child components.
- Use Svelte stores (`writable`, `readable`, `derived`) for shared state; access with `$store` auto-subscription syntax.
- Take advantage of scoped styles by default; use `:global()` sparingly for targeting external elements.
- Use `class:name={condition}` and `style:property={value}` directives for dynamic styling.
- Provide unique keys in `{#each items as item (item.id)}` for efficient list rendering.
- With SvelteKit, utilize file-based routing in `src/routes/` with `+page.svelte` and `+layout.svelte` files.
- Fetch data using `load` functions in `+page.js` or `+page.server.js`; handle form submissions with Form Actions.

### Django
- Follow the standard Django app structure.
- Use Django's Class-Based Views for standard CRUD operations.
- Keep business logic out of views; use services or model methods.
- Use select_related and prefetch_related to optimize database queries.
- Always include {% csrf_token %} in POST forms.

### Spring Boot
- Use constructor injection for dependencies.
- Place the main application class in the root package.
- Structure code into controllers, services, repositories, and models.
- Use appropriate annotations (@Service, @Repository, @RestController).
- Implement proper exception handling using @ControllerAdvice.

### Cross-Platform Mobile
- **Framework Selection:** Choose the right cross-platform framework (React Native, Flutter, Kotlin Multiplatform) based on project requirements, performance needs, and team skills.
- **Platform-Specific Code:** Isolate platform-specific code, use conditional compilation, follow framework-specific file naming conventions, and create proper abstractions.
- **UI/UX:** Balance between platform conventions and brand consistency. Design adaptive layouts for various screen sizes. Ensure smooth UI performance on all target platforms.
- **Native Features:** Minimize native dependencies; only use for platform-specific APIs, performance-critical tasks, or existing SDK integration. Carefully vet third-party plugins.
- **Performance:** Profile and optimize on both platforms. Optimize startup time, keep UI thread free, optimize assets, and monitor memory usage.
- **Testing:** Apply the testing pyramid from unit tests to E2E tests. Run tests on both platforms using real devices. Utilize device farms for broader coverage.
- **Security:** Implement platform-specific security for data storage, permissions, and network configuration. Never embed secrets in cross-platform code.

### React Native
- **Project Structure:** Organize by feature/module rather than by type. Centralize navigation, services, and reusable components.
- **Components:** Use functional components with hooks. Keep components small and focused. Use TypeScript interfaces for props.
- **Styling:** Use `StyleSheet.create` for performance. Avoid inline styles. Implement theming and design tokens for consistency.
- **State Management:** Use local state for UI elements, Context API for shared state with infrequent updates, and dedicated libraries for complex state management.
- **Platform-Specific Code:** Use `Platform.select()` or platform-specific extensions (`.ios.js`/`.android.js`) for platform differences.
- **Performance:** Optimize rendering with `memo`, `useMemo`, and `useCallback`. Use `FlatList` for long lists. Enable Hermes engine. Monitor bridge traffic.
- **Native Modules:** Prefer existing libraries. Consider new architecture (TurboModules/Fabric) for better performance.
- **Security:** Store sensitive data in platform-specific secure storage. Use HTTPS with certificate pinning. Validate deep link data.

### Flutter & Dart
- **Effective Dart:** Follow official Effective Dart guidelines (Style, Design, Usage, Documentation). Use standard Dart formatter and linters.
- **Dart Conventions:** Use appropriate naming conventions (snake_case for files, PascalCase for types, camelCase for variables/functions). Embrace null safety and immutability with `final` and `const`.
- **Widget Structure:** Compose UI from small, reusable widgets. Use `const` constructors whenever possible. Prefer `StatelessWidget` over `StatefulWidget` unless internal state is needed.
- **Widget Optimization:** Keep `build()` methods pure. Break down large methods into smaller widgets. Use `Key`s appropriately for lists and dynamic content.
- **State Management:** Choose appropriate solution (setState, Provider, Riverpod, Bloc/Cubit) based on complexity. Be consistent throughout the project.
- **Asynchrony:** Use `async`/`await` over raw Futures. Handle errors properly. Leverage `Streams` for reactive programming. Use isolates for CPU-intensive tasks.
- **Performance:** Minimize widget rebuilds. Optimize lists with builder constructors. Use `RepaintBoundary` strategically. Profile with Flutter DevTools to identify bottlenecks.
- **Testing:** Write unit tests for logic, widget tests for UI components, and integration tests for critical flows. Design for testability.

## Architecture Rules

### General Architecture
- Follow SOLID principles (Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion).
- Maintain a consistent and logical project structure.
- Aim for high cohesion and low coupling between modules.
- Use established design patterns appropriately, but avoid overengineering.
- Separate business logic from infrastructure concerns.

### Microservices
- Define service boundaries aligned with business capabilities using Domain-Driven Design principles.
- Keep services loosely coupled; use well-defined APIs and events for communication rather than shared databases.
- Design for failure with resilience patterns: timeouts, retries, circuit breakers, and bulkheads.
- Choose appropriate communication styles: synchronous (REST, gRPC) for immediate responses and asynchronous (events, messaging) for decoupling.
- Implement the Database per Service pattern with polyglot persistence based on service requirements.
- Accept eventual consistency across services; use saga patterns for cross-service transactions.
- Containerize services and deploy with orchestration platforms like Kubernetes.
- Ensure robust observability through centralized logging, correlation IDs, distributed tracing, and health checks.
- Implement comprehensive security: API gateways, service-to-service authentication, authorization, and secrets management.
- Focus testing on unit, integration, and contract tests rather than extensive end-to-end tests.

### Event-Driven Architecture
- Design producers and consumers to be loosely coupled with contracts defined only through event schemas.
- Use clear, past-tense event naming (e.g., `OrderCreated`, `PaymentProcessed`) to indicate actions that have occurred.
- Choose appropriate event payload strategies: minimal notification, state transfer, or hybrid approaches.
- Define and version event schemas (using JSON Schema, Avro, Protobuf) with a schema registry to manage evolution.
- Include standard metadata in events: `event_id`, `timestamp`, `correlation_id`, `schema_version`, and source.
- Select the appropriate event broker/router (Kafka, RabbitMQ, cloud services) based on throughput and delivery needs.
- Ensure atomicity between database state changes and event publishing using patterns like Transactional Outbox.
- Make consumers idempotent to handle potential duplicate events without side effects.
- Implement proper error handling with retries for transient issues and dead-letter queues for permanent failures.
- Prefer event choreography over centralized orchestration except for complex workflows requiring central coordination.

### Serverless Architecture
- Design functions with single responsibility; keep them small, focused, and stateless.
- Make functions idempotent, especially when triggered by event sources with at-least-once delivery semantics.
- Store all persistent state outside functions using managed services (databases, object storage, key-value stores).
- Use step functions/state machines for complex, multi-step processes requiring state coordination.
- Mitigate cold starts by keeping deployment packages small, initializing resources outside handlers, and using provisioned concurrency.
- Configure appropriate memory allocation and timeouts based on workload requirements and cost considerations.
- Apply least privilege principle for function execution roles, granting only necessary resource permissions.
- Front HTTP-triggered functions with API Gateways that handle authentication and authorization.
- Store sensitive configuration in dedicated secrets management services, not as environment variables.
- Implement structured logging, centralized metrics collection, and distributed tracing for comprehensive observability.

### Architecture Decision Records (ADRs)
- Document architecturally significant decisions in ADRs, focusing on one decision per record.
- Create ADRs for decisions impacting non-functional requirements, structure, dependencies, or key technology choices.
- Use a consistent template including title, status, date, context, options, decision outcome, and consequences.
- Treat accepted ADRs as immutable historical records; create new ADRs for changed decisions.
- Store ADRs in version control (e.g., `docs/adr/`) and link them from the project README.
- Write ADRs with clarity, objectivity, and explicit acknowledgment of trade-offs.

### API Design
- Follow REST principles for API endpoints.
- Use appropriate HTTP methods and status codes.
- Implement consistent error handling with standardized error response format.
- Include pagination for collections with potentially large result sets.
- Validate and sanitize all input data to prevent injection attacks.

### Apache Kafka
- Use meaningful, descriptive topic names reflecting the data content or business domain.
- Choose an appropriate number of partitions based on expected throughput and consumer parallelism needs.
- Use a replication factor of at least 3 in production with min.insync.replicas set to 2.
- Enable idempotent producers (`enable.idempotence=true`) and use `acks=all` for critical data.
- Manually commit consumer offsets (`enable.auto.commit=false`) after successful processing.
- Design consumers to be idempotent to handle message duplicates gracefully.
- Use a Schema Registry (Avro, Protobuf, JSON Schema) to manage and enforce message schemas.
- Implement comprehensive monitoring for brokers, producers, consumers, and key metrics.

## Cross-Cutting Concerns

### Security
- Follow OWASP Top 10 security best practices.
- Sanitize all input from external sources before use.
- Store passwords using strong adaptive hashing algorithms.
- Implement proper authorization checks at every entry point.
- Encrypt sensitive data at rest and in transit.
- **Threat Modeling**: Conduct structured threat modeling during the design phase and after significant changes.
- Use the STRIDE methodology (Spoofing, Tampering, Repudiation, Information Disclosure, DoS, Elevation of Privilege).
- Create data flow diagrams showing components, data stores, and trust boundaries during system decomposition.
- Systematically analyze and rate threats based on likelihood and potential impact to prioritize mitigation efforts.
- Document threat models with system diagrams, threat lists, risk assessments, and mitigation plans in version control.
- Integrate threat modeling into the SDLC through design reviews, involving cross-functional team members.
- **Secure Defaults**: Configure systems and applications to be secure out-of-the-box, requiring minimal security changes for production.
- Apply least privilege principles in default access controls; deny access by default and require explicit grants.
- Never ship products with default, hardcoded, or easily guessable credentials; require users to set strong credentials on first use.
- Use strong, current cryptography by default (TLS 1.2+, strong ciphers, secure hashing) and disable weak algorithms.
- Configure secure session defaults (short timeouts, HttpOnly, Secure, SameSite cookie flags) and robust error handling.
- Minimize attack surface by disabling unused ports, services, features, and removing sample content before deployment.
- Prefer frameworks and libraries with built-in security features; verify and override insecure defaults when necessary.
- **Penetration Testing**: Conduct ethical, authorized penetration testing to identify vulnerabilities before attackers do.
- Define clear objectives, asset scope, and boundaries in a formal Rules of Engagement (ROE) document signed by all parties.
- Select appropriate test types (black box, white box, or grey box) based on objectives and system knowledge.
- Follow recognized methodologies (PTES, OWASP Testing Guide) through phases of reconnaissance, scanning, exploitation, and cleanup.
- Minimize operational disruption during testing and maintain communication with the system owner's point of contact.
- Provide comprehensive reports with executive summaries, detailed findings, risk ratings (CVSS), and actionable remediation steps.
- Conduct retesting after vulnerabilities are addressed to verify effective remediation.
- **Secrets Rotation**: Regularly rotate secrets (passwords, API keys, certificates) to limit the time window for using compromised credentials.
- Automate secrets rotation whenever possible using dedicated secrets management solutions (HashiCorp Vault, AWS Secrets Manager, etc.).
- Determine rotation frequency based on risk: highly sensitive credentials (30-90 days), service-to-service credentials (60-180 days).
- Implement zero-downtime rotation using dual secrets mechanisms where applications try the newest version first, then fall back.
- Applications should fetch secrets dynamically rather than embedding them in configuration files or environment variables.
- Immediately rotate any secret suspected of compromise or exposure (found in logs, code commits) or after personnel changes.
- Generate robust audit trails for all rotation events and maintain monitoring and alerting for rotation failures.

### Testing
- Write unit tests for individual components in isolation.
- Write integration tests for component interactions.
- Structure unit tests using the Arrange-Act-Assert pattern.
- Keep tests fast, independent, repeatable, self-validating, and timely (FIRST principles).
- Use appropriate test doubles (mocks, stubs) for external dependencies.
- **Performance Testing**: Evaluate how systems behave under expected and stressed load conditions to identify bottlenecks.
- Define clear, measurable performance goals (e.g., "API response time < 500ms with 1000 concurrent users").
- Conduct various test types: load testing (expected conditions), stress testing (find breaking points), endurance testing (sustained load), and spike testing (sudden traffic changes).
- Measure critical metrics: response time (especially percentiles), throughput (RPS/TPS), error rates, and server-side resource utilization.
- Use realistic test scenarios that simulate actual user behavior with appropriate think time between actions.
- Test in environments that closely mirror production with sufficient test data and proper monitoring.
- Choose appropriate testing tools (JMeter, k6, Locust, Gatling) based on protocols, scripting needs, and team skills.
- Integrate performance tests into CI/CD pipelines with quality gates that fail builds if performance regressions occur.
- Analyze results thoroughly across metrics and correlate with application monitoring data to pinpoint bottlenecks.
- **E2E Testing**: Verify complete user workflows across the entire application stack, focusing on critical business flows.
- Position E2E tests at the top of the testing pyramid; have fewer E2E tests compared to unit/integration tests due to their higher maintenance costs.
- Design tests from the user's perspective, focusing on what users do rather than how the system implements functionality.
- Make each test independent and self-contained; avoid dependencies between tests or on shared application state.
- Set up test prerequisites through APIs rather than UI interactions when possible for faster, more reliable tests.
- Use the Page Object Model pattern to abstract UI structure and interaction details from test logic.
- Prefer stable selectors: dedicated test attributes (data-testid), ARIA roles/labels, or semantic attributes over brittle CSS selectors.
- Avoid fixed sleep/wait times; use the testing framework's built-in automatic waiting capabilities and explicit conditional waits.
- Manage test data properly: create isolated data, clean up after tests, and avoid hardcoding sensitive information.
- Run tests in parallel, in headless mode, and with limited retry strategies in CI/CD environments; capture screenshots and videos on failures.
- **Contract Testing**: Verify that independently developed services adhere to a shared understanding of their API interactions.
- Implement consumer-driven contract testing where service consumers define the minimal contracts they require from providers.
- Use contract tests to enable confident independent deployment of microservices and detect breaking API changes early.
- For consumers: write tests against mock providers that define expected requests and minimum required responses.
- For providers: verify that actual implementations can fulfill all consumer contracts by replaying contract requests.
- Use appropriate matchers (type, regex) instead of exact values to make contracts less brittle to non-breaking changes.
- Integrate contract testing in CI/CD pipelines with automated contract generation, verification, and compatibility checks.
- Share contracts via a broker (like Pact Broker) rather than manual file sharing to enable versioning and deployment checks.
- Use provider states to set up necessary preconditions for contract verification (e.g., "a user with ID 123 exists").
- Leverage contract testing to complement (not replace) unit tests and reduce reliance on slow, brittle end-to-end tests.

### Development Workflow
- Write meaningful commit messages following the Conventional Commits specification.
- Create focused commits that represent logical changes.
- Review code for functionality, readability, maintainability, and security.
- Select dependencies based on active maintenance, license compatibility, and security.
- Use environment variables for configuration, especially secrets.

### IDE Configuration
- **Standardization:** Use `.editorconfig` to enforce consistent basic coding style settings across different editors and IDEs.
- **Project Settings:** Store project-specific IDE settings in the repository (e.g., `.vscode/settings.json`, select `.idea/` files).
- **Key Configurations:** Standardize settings for formatting, linting, import organization, file encodings (UTF-8), and line endings (LF).
- **Core Extensions:** Maintain a documented list of recommended IDE extensions/plugins for the project's languages and frameworks.
- **Sharing:** Commit shared project settings to version control and document IDE setup guidelines in README or CONTRIBUTING files.
- **Formatting Integration:** Configure IDEs to use project's preferred formatters (Black, Prettier, gofmt, etc.) with format-on-save when feasible.
- **Team Agreement:** Agree on primary IDEs and core configurations while allowing personal customizations that don't affect project consistency.

### Debugging
- **Systematic Approach:** Follow a methodical process: reproduce, isolate, hypothesize, test, fix, verify, and document.
- **Reproducibility:** First reliably reproduce the bug, understanding the exact conditions that trigger it.
- **Debugger Skills:** Master your IDE's debugger features (breakpoints, watch expressions, call stack inspection, stepping).
- **Strategic Logging:** Use logging statements to trace execution flow and variable values when debuggers aren't practical.
- **Tool Utilization:** Leverage git bisect, static analysis, and divide-and-conquer techniques to isolate issues.
- **Language-Specific Knowledge:** Understand debugging tools and techniques specific to each language/platform (browser DevTools, pdb, delve, etc.).
- **Learn from Bugs:** Document non-trivial bug fixes in commit messages or issue trackers and consider adding tests to prevent regressions.
- **Question Assumptions:** Bugs often hide in incorrect assumptions; verify your understanding of how code, libraries, or systems behave.

### Git Advanced Practices
- Make atomic commits representing single logical changes; use interactive rebase to clean up history locally.
- Follow consistent branch naming conventions and choose an appropriate branching strategy.
- Use `git rebase` for local branch cleanup, but never rebase shared history.
- Keep pull requests small and focused with clear descriptions linking to relevant issues.
- Leverage git hooks (pre-commit, commit-msg) to automate checks and enforce standards.
- Use Git LFS for large binary files to avoid bloating repository history.
- Master advanced commands like `git stash`, `git cherry-pick`, `git bisect`, and `git reflog` for specific use cases.

### Linters & Formatters
- Use both linters (for code quality and error detection) and formatters (for consistent styling).
- Store tool configurations in the repository (`.eslintrc.js`, `.prettierrc`, `pyproject.toml`).
- Start with standard configurations (e.g., `eslint:recommended`, Black defaults) and customize minimally.
- Integrate with IDEs for real-time feedback and with CI pipelines as mandatory checks.
- Employ pre-commit hooks to prevent poorly formatted or error-containing code from being committed.
- Choose language-appropriate tools: ESLint/Prettier for JS/TS, Black/Flake8 for Python, gofmt for Go, etc.
- Format entire files at once to maintain consistency throughout the codebase.
- Address linting issues promptly; don't let warnings accumulate.

### Webpack
- Set `mode` explicitly to `'development'` or `'production'` to enable appropriate optimizations.
- Configure output with proper filename patterns; use `[contenthash]` for production long-term caching.
- Maintain separate configs for development and production; share common settings with `webpack-merge`.
- Apply loaders only to necessary files; exclude `node_modules` to speed up builds.
- Understand loader chaining order (right-to-left/bottom-to-top) for correct preprocessing.
- Use essential plugins like `HtmlWebpackPlugin`, `MiniCssExtractPlugin`, and `DefinePlugin`.
- Enable Webpack 5's persistent filesystem cache with `cache: { type: 'filesystem' }` for faster rebuilds.
- Implement code splitting with dynamic `import()` or `optimization.splitChunks` for smaller chunks.
- Leverage tree shaking by using ES modules and marking packages as side-effect-free when appropriate.
- Use bundle analyzers to identify large dependencies and optimization opportunities.

### Vite
- Leverage Vite's native ES Module (ESM) approach for faster development experience.
- Use TypeScript with `defineConfig` for type-safe configuration in `vite.config.ts`.
- Implement conditional configurations based on command (`serve`/`build`) and mode (`development`/`production`).
- Maintain HMR-friendly code and properly configure framework plugins for optimal hot-reloading.
- Understand Vite's development server features: pre-bundling dependencies with esbuild and HTTP caching.
- Remember that Vite uses Rollup for production builds with automatic code splitting and tree shaking.
- Configure path aliases (`resolve.alias`) to simplify imports across your project.
- Place static assets appropriately: `public/` for as-is serving or `src/assets/` for bundled resources.
- Prefix environment variables with `VITE_` to expose them to client code via `import.meta.env`.
- Keep plugin usage minimal and prefer official `@vitejs/` plugins for core functionalities.

## Interaction Guidelines

- If any requirements are unclear, ask for clarification before proceeding.
- If suggesting new libraries or dependencies, provide a brief rationale and check for potential vulnerabilities.
- Provide explanations for non-obvious code or design decisions.
- Respect the project's existing patterns and consistency unless explicitly asked to change them.
- Feel free to suggest improvements when you see better approaches.
- If code changes might impact performance, security, or maintainability, mention these considerations.
- Always provide a brief summary of changes after making edits.

## Usage and Extension

These rules guide your behavior in several ways:

1. **Global Guidance**: Core principles apply to all code generation
2. **Contextual Application**: Language and framework-specific rules are applied based on the current file type
3. **Architectural Consistency**: Architecture rules ensure system-level consistency
4. **Quality Assurance**: Security, testing, and workflow rules promote robust development practices

When working on a specific project, adjust your suggestions to align with that project's established patterns and technologies. Prioritize consistency within the existing codebase while still promoting best practices.

---

> This document represents a foundational set of rules. It can be overridden by project-specific rules when necessary.
