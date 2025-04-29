# Ruby on Rails Rules

## General Principles & Conventions

* **Convention Over Configuration:** Embrace and follow Rails conventions for file structure, naming, and database mapping unless there's a compelling, documented reason not to. This enhances predictability and reduces boilerplate.
* **RESTful Design:** Design controllers and routes following RESTful principles. Use standard resource actions (`index`, `show`, `new`, `create`, `edit`, `update`, `destroy`).
* **Fat Model, Skinny Controller:** Keep controllers thin. Their primary responsibility is handling request/response cycles (parsing parameters, calling services/models, setting response variables, rendering views/JSON). Move complex business logic, data manipulation, and callbacks into models, service objects, or concerns.
* **DRY (Don't Repeat Yourself):** Avoid code duplication. Extract reusable logic into helper methods, concerns, service objects, or partial views.
* **Service Objects / Interactors:** For complex business logic involving multiple models or external services, consider using Service Objects or Interactors to encapsulate the workflow, keeping both controllers and models cleaner.
* **Environment Configuration:** Use Rails' environment files (`config/environments/*.rb`) and credentials (`config/credentials.yml.enc`) for environment-specific settings and secrets. Avoid hardcoding configuration values. Use environment variables where appropriate, especially for deployment configuration (database URLs, external API keys).

## Models (Active Record)

* **Naming:** Model class names should be singular and `PascalCase` (e.g., `User`, `Order`). Table names should be plural and `snake_case` (e.g., `users`, `orders`).
* **Associations:** Define associations (`belongs_to`, `has_many`, `has_one`, `has_and_belongs_to_many`, `has_many :through`) clearly to represent relationships between models. Use `dependent: :destroy` or other options appropriately to manage associated records.
* **Validations:** Implement validations within the model (`validates`, `validates_presence_of`, etc.) to ensure data integrity before saving to the database.
* **Callbacks:** Use ActiveRecord callbacks (`before_save`, `after_create`, etc.) judiciously. Avoid putting complex business logic or external API calls directly in callbacks, as they can make models harder to test and reason about. Consider service objects or explicit method calls instead. Use callbacks primarily for simple, model-internal state changes or data normalization directly related to the model's data.
* **Scopes:** Define named scopes (`scope :recent, -> { order(created_at: :desc).limit(5) }`) for common, reusable query conditions to keep controller queries clean and DRY.
* **Query Optimization:**
    * **Select Specific Columns:** Use `.select()` to retrieve only necessary columns, especially for read-heavy actions or large tables.
    * **Avoid N+1 Queries:** Use eager loading (`.includes()`, `.preload()`, `.eager_load()`) to load associated records efficiently when they will be accessed in a loop. Use tools like the `bullet` gem in development to detect N+1 queries.
    * **Batch Processing:** Use `.find_each` or `.find_in_batches` for processing large numbers of records to reduce memory consumption compared to loading all records with `.all.each`.
    * **Use Database Functions:** Utilize database functions (`count`, `sum`, `average`) via ActiveRecord calculation methods (`.count`, `.sum`, etc.) instead of loading records into memory to perform calculations.
    * **Check Existence Efficiently:** Use `.exists?` instead of `.present?`, `.any?`, or `count > 0` when you only need to check for the existence of a record, as `.exists?` is generally more performant.
* **Indexing:** Ensure database columns frequently used in `WHERE` clauses, `JOIN`s, or `ORDER BY` clauses have appropriate database indexes defined via migrations (`add_index`).

## Controllers

* **Naming:** Controller class names should be plural and `PascalCase`, ending with `Controller` (e.g., `UsersController`, `OrdersController`).
* **Standard Actions:** Stick to the standard RESTful actions (`index`, `show`, `new`, `create`, `edit`, `update`, `destroy`) where possible.
* **Skinny Controllers:** Controllers should orchestrate the request/response flow:
    * Receive and parse request parameters (using Strong Parameters).
    * Call appropriate model methods or service objects to perform actions/retrieve data.
    * Set instance variables (`@user`) for the view.
    * Render the appropriate view, redirect, or return a JSON response.
* **Strong Parameters:** Always use Strong Parameters (`require` and `permit`) to whitelist acceptable parameters for mass assignment in `create` and `update` actions, preventing mass assignment vulnerabilities.
    ```ruby
    def user_params
      params.require(:user).permit(:name, :email, :password, :password_confirmation)
    end
    ```
* **Before Actions:** Use `before_action` filters for common setup tasks (e.g., loading a resource based on `params[:id]`, authenticating users, checking authorization). Keep filters focused.
* **Response Format:** Respond appropriately based on the request format (HTML, JSON, etc.) using `respond_to` blocks or explicit `render :json` / `render :html` calls.

## Views (ERB / Haml / Slim etc.)

* **Keep Logic Minimal:** Avoid complex logic or database queries directly in views. Move logic to helpers, presenters/decorators, or prepare data in the controller.
* **Partials:** Extract reusable view segments into partials (`_partial.html.erb`) to keep views DRY. Pass local variables to partials explicitly.
* **Helpers:** Define view helper methods in `app/helpers` for complex formatting or presentation logic that doesn't belong in the model. Keep helpers organized (e.g., per controller or feature).
* **Presenters/Decorators (Optional):** For more complex view logic, consider using the Presenter or Decorator pattern (e.g., using Draper gem) to encapsulate presentation logic related to a model.
* **Asset Pipeline/Webpacker:** Utilize the Asset Pipeline or Webpacker/Shakapacker for managing CSS, JavaScript, and images efficiently (compilation, minification, fingerprinting).

## Routing (`config/routes.rb`)

* **Resourceful Routes:** Use `resources` or `resource` declarations for standard CRUD routes whenever possible, as it enforces RESTful conventions.
* **Nesting:** Use nested routes (`resources :posts do resources :comments end`) logically, but avoid nesting more than 1-2 levels deep to keep URLs manageable. Consider shallow nesting (`shallow: true`).
* **Member/Collection Routes:** Add custom actions to resourceful routes using `member do ... end` (acts on a single resource, e.g., `/posts/1/publish`) or `collection do ... end` (acts on the collection, e.g., `/posts/search`).
* **Readability:** Keep the routes file organized and readable. Group related routes or use comments.

## Testing (Minitest / RSpec)

* **Follow Testing Rules:** Adhere to the general testing best practices.
* **Rails Testing Tools:** Utilize Rails testing helpers and conventions.
* **Fixtures vs. Factories:** Prefer using factories (e.g., FactoryBot) over fixtures for creating test data, as factories are generally more flexible and maintainable. Use fixtures sparingly for stable, simple lookup data if needed.
* **Test Types:** Write model tests (validations, scopes, methods), controller tests/request specs (routing, actions, responses, parameter handling), system tests/feature specs (user workflows via browser interaction), and potentially helper/mailer/job tests.
* **Test Database:** Ensure tests run against a separate test database, typically managed via `rails db:test:prepare` or similar commands. Keep test data isolated between tests.
* **Mocking/Stubbing:** Use mocking/stubbing for external dependencies (e.g., API calls) in unit and controller tests.

## Security

* **Follow Security Rules:** Adhere to general security best practices.
* **Mass Assignment:** Use Strong Parameters diligently (see Controllers section).
* **SQL Injection:** Rely on ActiveRecord's parameterized query interface (see Models section). Avoid string interpolation in query conditions.
* **XSS Protection:** Rails enables output escaping by default in ERB. Be cautious when using `raw()` or `html_safe` – ensure the content is trusted or properly sanitized first. Use `sanitize()` helper for user-generated content.
* **CSRF Protection:** Rails enables CSRF protection (`protect_from_forgery`) by default. Ensure the CSRF token (`<%= csrf_meta_tags %>`) is included in layouts and handled correctly, especially with JavaScript requests.
* **Session Security:** Configure session storage securely (e.g., cookie store with encryption, or server-side stores). Use secure, HttpOnly cookies. Avoid storing sensitive data directly in sessions.
* **Secrets Management:** Use Rails Encrypted Credentials (`config/credentials.yml.enc` edited via `bin/rails credentials:edit`) for storing API keys, passwords, etc. Do not commit the master key (`config/master.key`) unless necessary for specific deployment workflows, and ensure it's properly secured.
* **Dependency Auditing:** Regularly run `bundle audit` to check for known vulnerabilities in gems. Keep gems updated.
* **Security Headers:** Consider using gems like `secure_headers` to easily configure HTTP security headers (CSP, HSTS, X-Frame-Options, etc.).
* **Authentication/Authorization:** Use robust gems like Devise for authentication and Pundit or CanCanCan for authorization logic.

## Performance

* **Caching:** Use Rails' caching mechanisms (fragment caching, page caching, low-level caching) to improve performance for expensive operations or frequently accessed data.
* **Background Jobs:** Move time-consuming tasks (email sending, file processing, API calls) to background jobs using ActiveJob with a backend like Sidekiq, Resque, or DelayedJob.
* **Database Optimization:** Beyond the model-level optimizations mentioned above, consider database-specific optimizations like composite indexes, partial indexes, and database-specific features.
* **Load Testing:** Perform load testing on critical endpoints to identify and address bottlenecks before they affect users.
* **Monitoring:** Implement application performance monitoring (APM) tools like New Relic, Scout APM, or Skylight to identify performance issues in production.

## Gems & Dependencies (Bundler)

* **Follow Bundler Rules:** Adhere to the general Ruby bundler best practices.
* **Minimize Dependencies:** Only add gems that provide significant value. Evaluate alternatives and consider the maintenance overhead and security surface area introduced by each gem.
* **Keep Updated:** Regularly update gems using `bundle update` (or preferably targeted updates `bundle update <gem_name>`) and test thoroughly. Use tools like Dependabot.
* **Group Gems:** Organize gems in the Gemfile by their purpose using groups (`:development`, `:test`, `:production`, etc.) to only load necessary gems in each environment.
* **Specify Versions:** Use version constraints in the Gemfile to prevent unexpected breaking changes. Use semantic versioning operators like `~>` appropriately.
