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

## Architecture Rules

### General Architecture
- Follow SOLID principles (Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion).
- Maintain a consistent and logical project structure.
- Aim for high cohesion and low coupling between modules.
- Use established design patterns appropriately, but avoid overengineering.
- Separate business logic from infrastructure concerns.

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

### Testing
- Write unit tests for individual components in isolation.
- Write integration tests for component interactions.
- Structure unit tests using the Arrange-Act-Assert pattern.
- Keep tests fast, independent, repeatable, self-validating, and timely (FIRST principles).
- Use appropriate test doubles (mocks, stubs) for external dependencies.

### Development Workflow
- Write meaningful commit messages following the Conventional Commits specification.
- Create focused commits that represent logical changes.
- Review code for functionality, readability, maintainability, and security.
- Select dependencies based on active maintenance, license compatibility, and security.
- Use environment variables for configuration, especially secrets.

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
