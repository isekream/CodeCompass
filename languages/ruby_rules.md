# Ruby Rules

## Style & Formatting

* **Style Guide:** Follow the community Ruby Style Guide (often associated with RuboCop's defaults) for consistency.
* **Formatting:**
    * Use 2 spaces for indentation, never tabs.
    * Use Unix-style line endings (`\n`).
    * Limit lines to 120 characters (a common RuboCop default, though 80 was traditional).
    * Avoid trailing whitespace.
    * Use UTF-8 source file encoding.
    * Use spaces around operators (`+`, `-`, `*`, `/`, `=`, `==`, etc.), after commas, colons, semicolons, and around `{` and before `}`.
    * Avoid spaces after `(`, `[` and before `]`, `)`.
    * Use empty lines to separate logical paragraphs within methods, and between method definitions.
    * Avoid semicolons to separate statements; use one statement per line.
* **Tooling:** Use RuboCop or similar linters/formatters to automatically check and enforce style guide rules. Configure it via `.rubocop.yml` in the project root.

## Naming Conventions

* **Variables & Methods:** Use `snake_case` (e.g., `user_name`, `calculate_total`).
* **Classes & Modules:** Use `PascalCase` (also known as `CamelCase` in Ruby context) (e.g., `UserService`, `PaymentProcessor`).
* **Constants:** Use `SCREAMING_SNAKE_CASE` (all uppercase with underscores) (e.g., `MAX_RETRIES`, `DEFAULT_PORT`).
* **Files:** Use `snake_case` for filenames (e.g., `user_service.rb`).
* **Directories:** Use `snake_case` for directories (e.g., `app/models`).
* **Predicate Methods:** Method names ending in `?` should return a boolean (`true` or `false`) (e.g., `user.admin?`, `order.paid?`).
* **Potentially Dangerous Methods:** Method names ending in `!` ("bang methods") indicate the method modifies the receiver object in place or could raise an exception where a non-bang version might not. Use them deliberately.

## Idiomatic Ruby

* **Blocks & Iterators:** Prefer using iterators (`.each`, `.map`, `.select`, `.reject`, `.reduce`/`.inject`, etc.) with blocks over traditional `for` loops. Use `{...}` for single-line blocks and `do...end` for multi-line blocks.
* **Truthiness:** Understand Ruby's truthiness: only `false` and `nil` are falsy; everything else (including `0`, empty strings `""`, empty arrays `[]`, etc.) is truthy. Use this in conditional statements directly (e.g., `if user` instead of `if user != nil`).
* **String Quotes:** Prefer single quotes (`'`) for string literals when no interpolation or special escape sequences (`\n`, `\t`) are needed. Use double quotes (`"`) when interpolation (`#{expression}`) or escape sequences are required.
* **Symbols:** Use symbols (`:symbol_name`) for identifiers, hash keys, and enum-like values where appropriate. They are immutable and often more memory-efficient than strings for keys.
* **Conditional Assignment:** Use `||=` for assigning a default value if a variable is `nil` or `false` (e.g., `options[:user] ||= 'default_user'`).
* **Guard Clauses:** Use guard clauses (early returns or exceptions for invalid conditions) at the beginning of methods to avoid deep nesting.
    ```ruby
    def process_order(order)
      return unless order&.valid? # Guard clause
      # ... proceed with processing ...
    end
    ```
* **Method Returns:** Avoid explicit `return` at the end of methods; Ruby implicitly returns the value of the last evaluated expression. Use explicit `return` for early exits (guard clauses).
* **Avoid Perl-isms:** Avoid cryptic Perl-style special variables (like `$;`, `$&`, etc.), except perhaps `$_` in very localized contexts if clear.
* **Enumerable Module:** Leverage the powerful methods provided by the `Enumerable` module for collection manipulation.
* **Use `unless` for Negative Conditions:** Prefer `unless condition` over `if !condition` for simple negative conditions.
* **Ternary Operator:** Use the ternary operator (`condition ? value_if_true : value_if_false`) for simple conditional assignments, but avoid nesting them.
* **Safe Navigation Operator (`&.`):** Use the safe navigation operator (`&.`) to avoid `NoMethodError` on `nil` when chaining methods (e.g., `user&.address&.street`).

## Classes & Modules

* **Single Responsibility Principle (SRP):** Classes should have a single, well-defined responsibility. Keep classes small and focused.
* **Methods:** Keep methods short, focused on one task (ideally < 10-15 lines).
* **Accessibility:** Use `public`, `protected`, and `private` keywords appropriately to control method visibility. Default is `public`. Helper methods intended only for internal use should be `private`.
* **Modules for Namespacing:** Use modules to group related classes and constants, preventing naming collisions (e.g., `module Payments; class Gateway; end; end`).
* **Modules for Mixins:** Use modules (`include` or `extend`) to share behavior (methods) between classes (composition over inheritance). Create small, focused mixin modules.
* **`attr_accessor`, `attr_reader`, `attr_writer`:** Use these helpers to generate getter/setter methods instead of writing them manually. Prefer `attr_reader` if the attribute should not be publicly settable after initialization.

## Error Handling (Exceptions)

* **Use Exceptions for Exceptional Cases:** Raise exceptions (`raise StandardError` or a more specific subclass) for genuine error conditions or exceptional events, not for normal control flow.
* **Rescue Specific Exceptions:** Rescue (`rescue SpecificError => e`) specific exception classes rather than generic `Exception` or `StandardError` unless you intend to catch a broad category. Rescuing `Exception` is strongly discouraged as it can catch system signals like `Interrupt`.
* **Rescue Blocks:** Keep `rescue` blocks concise. They should typically log the error, perhaps perform minimal cleanup, and either re-raise the exception, raise a different exception, or return an appropriate value indicating failure.
* **Custom Exceptions:** Define custom exception classes (inheriting from `StandardError` or its subclasses) for application-specific error conditions to allow for more granular rescuing.
* **`ensure` Block:** Use the `ensure` block for code that *must* run whether an exception occurred or not (e.g., closing files, releasing locks).
* **Retry Logic:** Implement retry logic (often with exponential backoff) within `rescue` blocks for transient errors (e.g., network timeouts), but limit the number of retries.

## Concurrency

* **Threads:** Understand Ruby's Global Interpreter Lock (GIL) in MRI/CRuby means that only one thread can execute Ruby code at a time, limiting CPU-bound parallelism. Threads are still effective for I/O-bound tasks where threads wait for external resources.
* **Thread Safety:** Be cautious when accessing shared mutable state across threads. Use synchronization primitives like `Mutex`, `Queue`, `ConditionVariable` from the `thread` library or concurrent-ruby gem structures.
* **Avoid Data Races:** Protect shared data with mutexes or use thread-safe data structures.
* **Fibers:** Consider using Fibers for lightweight, cooperative concurrency, especially for managing I/O within a single thread (often used by async frameworks).
* **Ractors (Ruby 3+):** Explore Ractors for true parallelism (bypassing GIL) by providing isolated execution contexts that communicate via message passing. Ractors have restrictions on shared objects.
* **Concurrency Libraries:** Leverage gems like `concurrent-ruby` for higher-level abstractions (thread pools, futures, promises, atomic references).

## Dependency Management (Bundler)

* **`Gemfile`:** Declare all project dependencies in the `Gemfile`.
* **Specify Versions:** Use optimistic version constraints (`~> 1.2`) or pessimistic constraints (`~> 1.2.3`) for dependencies to allow compatible patch/minor updates while preventing breaking changes. Avoid locking to exact versions unless necessary. Define the required Ruby version.
* **Groups:** Use groups (`group :development, :test do ... end`) to separate dependencies needed only for specific environments. Install production gems using `bundle install --without development test`.
* **`Gemfile.lock`:** **Always** commit `Gemfile.lock` to version control for applications. This ensures all developers, CI, and production environments use the exact same gem versions. Libraries typically do *not* commit `Gemfile.lock`.
* **`bundle exec`:** Prefix commands (`rake`, `rspec`, `rails`) with `bundle exec` to ensure they run with the exact gem versions specified in `Gemfile.lock`.
* **Update Dependencies:** Update dependencies deliberately using `bundle update <gemname>` or `bundle update`. Test thoroughly after updates. Use tools like Dependabot or Depfu to manage updates.
* **Audit:** Regularly audit dependencies for security vulnerabilities using `bundle audit` or similar tools.

## Rails Best Practices

* **Controller Actions:** Keep controller actions simple and focused. Move complex business logic to service objects, model methods, or other appropriate places.
* **Skinny Controllers, Fat Models:** Keep controllers minimal, focused on handling HTTP requests/responses. Put business logic in models, but don't overload models with too many responsibilities.
* **Service Objects:** Create service objects for complex operations that don't naturally fit into a model.
* **Concerns:** Use concerns to extract shared functionality between models or controllers. Keep concerns focused on a single responsibility.
* **Background Jobs:** Move time-consuming tasks to background jobs (using Active Job with Sidekiq, Resque, or similar). Ensure jobs are idempotent where possible.
* **N+1 Queries:** Avoid N+1 query problems using eager loading (`includes`, `preload`, `eager_load`) when fetching related records.
* **Query Objects:** Use query objects for complex database queries to keep models clean.
* **Strong Parameters:** Use strong parameters to whitelist attributes that can be mass-assigned.
* **Flash Messages:** Use flash messages consistently for user feedback.

## Security

* **Input Validation:** Validate and sanitize all external input (user parameters, API requests, file uploads).
* **SQL Injection:** Use ActiveRecord (Rails) or other ORMs/libraries that provide parameterized queries or safe quoting mechanisms. Avoid building SQL strings with raw user input.
* **Cross-Site Scripting (XSS):** Use frameworks (like Rails) that provide automatic output escaping for HTML templates. Manually escape data displayed in views if necessary (`ERB::Util.html_escape` or `h`).
* **Cross-Site Request Forgery (CSRF):** Use framework mechanisms (like Rails `protect_from_forgery`) to prevent CSRF attacks.
* **Mass Assignment:** Use strong parameters (Rails) or explicit attribute assignment to prevent mass assignment vulnerabilities.
* **Command Injection:** Avoid executing shell commands with user input. If unavoidable, use methods that explicitly handle arguments separately (e.g., `system(command, arg1, arg2)`) and validate inputs strictly.
* **File Uploads:** Validate file types, sizes, and filenames. Store uploaded files securely (e.g., outside the web root or on cloud storage) with appropriate permissions.
* **Secrets Management:** Do not commit secrets (API keys, passwords) to version control. Use environment variables, encrypted credentials (Rails Credentials), or external secret management systems.

## Testing

* **Frameworks:** Use standard testing frameworks like Minitest (built-in) or RSpec.
* **Coverage:** Aim for good test coverage, especially for business logic and models.
* **Types:** Write unit tests (testing individual methods/classes in isolation), integration tests (testing interaction between components), and potentially system/acceptance tests.
* **Test Doubles:** Use mocks and stubs effectively to isolate units under test.
* **Readability:** Write clear, descriptive tests. Follow Arrange-Act-Assert patterns where applicable.
