# Supabase Rules

## General Principles

* Leverage Supabase features (Auth, Storage, Realtime, Edge Functions) where appropriate, but avoid implementing core business logic solely in client-side code if it requires secure validation or complex orchestration.
* Follow PostgreSQL best practices (see `postgresql_rules.md`) for database schema design, querying, and indexing, as Supabase uses PostgreSQL.
* Prioritize security: Implement Row Level Security (RLS) policies diligently.
* Structure your project logically, separating concerns (e.g., UI components, shared logic, API integration, Supabase-specific code).
* Use TypeScript for type safety with Supabase client libraries.
* Prefer server-side operations for sensitive or complex business logic.
* Maintain a clear separation between data access, business logic, and presentation layers.

## Project Structure and Organization

* Organize Supabase-related code in a dedicated directory/module (e.g., `lib/supabase` or `services/supabase`).
* Create typed interfaces for your database schema (tables, views).
* Define database types using Supabase's generated types or manually created TypeScript interfaces.
* Abstract Supabase interactions behind service/repository layers for better testability and separation of concerns.
* Consider creating custom hooks (React) or composables (Vue) for common Supabase operations.

## Database & Schema

* Use the Supabase dashboard for initial setup and exploration but manage schema changes via migrations for staging/production environments.
* Use Supabase database migrations (`supabase db diff`, `supabase migration new`, `supabase db push/remote commit`) for managing schema changes across environments (local, staging, prod). Avoid direct changes to production via the dashboard.
* Design your schema carefully, considering data relationships and access patterns.
* Enable Point-in-Time Recovery (PITR) for production databases, especially if expected to grow beyond 4GB.
* Use database seeds (`supabase db seed`) for initial data and testing.
* Consider using Postgres schemas to organize tables by domain (e.g., `auth`, `public`, `billing`).
* Use foreign key constraints to maintain referential integrity.
* Add appropriate indexes for frequently queried columns.
* Utilize database triggers for maintaining derived data or audit logs.
* Consider table partitioning for very large tables (time-series data, logs).
* Use transaction blocks for operations that must be atomic.

## Row Level Security (RLS)

* Enable RLS on all tables containing sensitive or user-specific data.
* Create specific RLS policies for `SELECT`, `INSERT`, `UPDATE`, `DELETE` operations based on user roles and ownership.
* Use built-in Supabase Auth functions (`auth.uid()`, `auth.role()`, `auth.email()`) within RLS policies.
* Default to restrictive policies (denying access) and explicitly grant permissions (`USING` clause for access, `WITH CHECK` clause for insert/update validation).
* Test RLS policies thoroughly with different user roles and scenarios.
* Consider implementing multi-tenancy using RLS policies based on `tenant_id` or similar.
* Create helper functions for common RLS checks to reduce duplication.
* Document RLS policies clearly, explaining the access control logic.
* Verify RLS policies after schema changes that might affect them.
* Use Supabase's policy simulation in the dashboard to test policies.
* Consider implementing application-level authorization in addition to RLS for complex permission scenarios.

## Database Functions (PL/pgSQL)

* Use database functions for operations that need to run atomically, require elevated privileges (using `SECURITY DEFINER`), or benefit from being close to the data (e.g., complex data manipulation, triggers).
* **Default to `SECURITY INVOKER`:** Functions run with the permissions of the calling user, respecting RLS policies.
* **Use `SECURITY DEFINER` with caution:** Only use when the function needs to bypass RLS or perform actions the calling user might not have direct permission for. Clearly document why `SECURITY DEFINER` is needed.
* When using `SECURITY DEFINER`, **always set `search_path` to an empty string** (`SET search_path = '';`) at the beginning of the function to prevent potential security risks from unintended schema resolution. Use fully qualified names (e.g., `public.users`) for all objects.
* Adhere to PostgreSQL function best practices (typing, stability declarations - `IMMUTABLE`, `STABLE`, `VOLATILE`).
* Use functions to encapsulate complex queries or reusable logic accessed via RPC calls from client libraries.
* Return well-defined data structures from functions (tables, records, custom types).
* Handle errors explicitly within functions.
* Use transactions appropriately when a function modifies multiple tables.
* Document function parameters, return types, and behavior.
* Use parameter names consistently across related functions.
* Consider performance implications for functions that process large datasets.

## Edge Functions

* Use Edge Functions for server-side logic, integrating with third-party services, handling webhooks, or performing tasks requiring secrets/service keys.
* Write Edge Functions preferably in TypeScript. JavaScript is also supported.
* Use hyphens for function names (e.g., `send-welcome-email`) as they are URL-friendly.
* Organize functions logically, potentially using a `_shared` directory for common code (e.g., Supabase client initialization, CORS headers).
* Use the Supabase Admin client (`createClient` with `service_key`) within Edge Functions for operations requiring elevated privileges (bypassing RLS). Handle the service key securely via environment variables.
* Handle different HTTP methods (`GET`, `POST`, `PUT`, etc.) appropriately within a single function if creating RESTful endpoints.
* Implement proper error handling using try-catch blocks and return meaningful error responses (e.g., `new Response(JSON.stringify({ error: '...' }), { status: 500 })`). Utilize Supabase client error types (`FunctionsHttpError`, etc.) when invoking functions.
* Manage JWT verification: By default, functions require a valid user JWT. Disable JWT verification (`--no-verify-jwt` locally or in `config.toml`) only when necessary (e.g., for public webhooks like Stripe) and understand the security implications.
* Set appropriate CORS headers if the function will be called from web browsers.
* Implement request validation using schemas (Zod, Yup, etc.).
* Add appropriate logging for debugging and monitoring.
* Consider implementing rate limiting for public functions.
* Structure Edge Functions with clear separation of concerns (handlers, services, utilities).
* Test Edge Functions thoroughly, potentially using mock databases.
* Optimize cold start times by keeping dependencies minimal.
* Use environment variables for configuration, especially for secrets.

## Authentication & Authorization

* Utilize Supabase Auth for user management (signup, login, password reset, providers).
* Store user profiles in a dedicated `profiles` table linked to `auth.users` via the user ID.
* Use RLS policies based on `auth.uid()` and potentially custom roles/claims for authorization.
* Implement email verification workflows if appropriate for your application.
* Configure password policies and account lockout settings.
* Use appropriate authentication providers based on your user base (Email/Password, OAuth providers, SAML, etc.).
* Consider implementing multi-factor authentication for sensitive applications.
* Define custom user roles beyond the basic Supabase roles if needed.
* Handle authentication state properly in client applications, including token refresh.
* Implement proper session management and logout functionality.
* For mobile applications, consider device-specific authentication flows.
* Set appropriate token expiration times based on your security requirements.
* Use secure cookies and proper CORS settings for web applications.

## Storage

* Use Supabase Storage for user uploads (avatars, documents, etc.).
* Configure storage buckets with appropriate access rules (public vs. private).
* Use RLS policies on the `storage.objects` table for fine-grained access control to private files.
* Implement file validation (file types, size limits) both client-side and server-side.
* Generate and use signed URLs for temporary access to private files.
* Set appropriate CORS policies for buckets accessed directly from browsers.
* Consider implementing folder structures for organizing files.
* Use appropriate content types when uploading files.
* Implement cleanup procedures for orphaned files.
* Consider using transformations for images where available.
* Validate file content if security is critical (not just file extensions).
* Implement virus/malware scanning for uploaded files in critical applications.

## Realtime

* Use Supabase Realtime for features requiring live updates (chat, presence, collaborative editing).
* Listen to database changes using `.subscribe()` on the client.
* Ensure RLS policies are correctly configured, as Realtime respects them. Broadcast data selectively based on user permissions.
* Consider using channels for topic-based subscriptions.
* Handle connection state changes appropriately (`CONNECTING`, `CONNECTED`, `DISCONNECTED`).
* Implement proper error handling and reconnection logic.
* Optimize payload size by selecting only necessary columns.
* Consider implementing client-side data merging for better user experience.
* Monitor realtime connections and usage in production.
* Test realtime functionality across different network conditions.
* Design your schema with realtime considerations in mind (e.g., denormalization).

## Client-Side (JS/TS)

* Use the official `supabase-js` library.
* Initialize the client using environment variables for the Supabase URL and Anon Key. Never expose the Service Role Key on the client-side.
* Handle errors properly from Supabase client calls (e.g., checking the `error` object).
* Use `async/await` for interacting with Supabase functions.
* Fetch only the data needed (`select('column1, column2')`). Avoid fetching entire tables unnecessarily.
* Optimize queries with appropriate filters, limits, and ordering.
* Consider implementing client-side caching for frequently accessed data.
* Use type-safe query builders if available.
* Structure Supabase client code to be easily testable.
* Implement proper loading states and error handling in the UI.
* Consider implementing offline capabilities for critical functionality.
* Use pagination for large result sets.
* Organize Supabase client code logically (repositories, services, etc.).

## Security (General)

* Never expose your Service Role Key or database secrets in client-side code. Use environment variables for server-side code (Edge Functions, backend).
* Implement rate limiting where applicable (e.g., in Edge Functions exposed publicly).
* Keep Supabase libraries and dependencies updated.
* Regularly review RLS policies and bucket security rules.
* Sanitize user input before displaying it or using it in database operations (though RLS and parameterized queries provide primary defense).
* Implement proper CORS policies for APIs and storage buckets.
* Consider implementing Content Security Policy (CSP) for web applications.
* Audit database access patterns and permissions regularly.
* Monitor failed authentication attempts and suspicious activities.
* Implement proper session management and token handling.
* Consider using signed URLs with expiration for sensitive operations.
* Implement appropriate logging for security events.
* Consider regular security penetration testing for critical applications.

## Performance Optimization

* Use appropriate indexes for frequently queried columns.
* Optimize queries to select only needed columns and limit result sets.
* Consider implementing caching strategies for frequently accessed data.
* Use connection pooling for server-side Supabase clients.
* Monitor query performance using Supabase's dashboard or PostgreSQL tools.
* Optimize large data operations (consider batching or background processing).
* Use appropriate database partitioning for very large tables.
* Implement pagination for large result sets.
* Consider using materialized views for complex reporting queries.
* Optimize RLS policies for performance (avoid complex calculations).
* Profile client-side code for Supabase interactions.

## Development Workflow

* Use multiple environments (local, staging, production).
* Use the Supabase CLI for local development, migrations, and function deployment.
* Implement automated testing for database functions, edge functions, and RLS policies.
* Set up continuous integration/deployment pipelines.
* Use version control for schema migrations and Edge Functions.
* Document your Supabase setup, including schema, functions, and RLS policies.
* Implement database seeders for development and testing environments.
* Consider using Supabase's local development environment for offline work.
* Create scripts for common development tasks (migrations, deployments).
* Implement proper logging and monitoring in production.
* Set up backup strategies appropriate for your data sensitivity.
* Document environment setup procedures for new team members.
* Consider implementing feature flags for staged rollouts.

## Monitoring and Debugging

* Set up appropriate logging in Edge Functions and database functions.
* Monitor database performance with Supabase dashboard metrics.
* Set up alerts for critical error rates or performance degradation.
* Implement structured error handling and reporting.
* Consider using observability tools compatible with Supabase.
* Monitor authentication failures and security events.
* Track usage metrics for resource planning.
* Set up regular database health checks.
* Document common error scenarios and troubleshooting procedures.
* Consider implementing application performance monitoring for end-to-end visibility.
