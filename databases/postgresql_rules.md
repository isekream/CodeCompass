# PostgreSQL Rules

## Style and Formatting

* Use lowercase `snake_case` for all object names (tables, columns, functions, indexes, etc.). PostgreSQL converts unquoted identifiers to lowercase automatically.
* Keep object names descriptive but concise, generally not exceeding 63 characters. Avoid unclear abbreviations.
* Separate words in object names with underscores (`_`).
* Use 4 spaces for indentation in SQL/PL/pgSQL code; do not use tabs.
* Limit line length, ideally around 80-100 characters.
* Use UTF-8 encoding for files.
* Write keywords (`SELECT`, `UPDATE`, `WHERE`, etc.) in uppercase for clarity, although lowercase is also acceptable if consistent. Function names and data types should generally be lowercase.
* Use whitespace judiciously for readability:
  * Place each major clause (SELECT, FROM, WHERE, etc.) on a new line
  * Indent subqueries and CTEs
  * Use spaces around operators 
  * Align ON clauses in JOIN statements
* Format complex queries consistently with line breaks and indentation for readability.
* Include comments (`--` or `/* ... */`) for non-obvious code sections. Keep comments updated.
* Use docstrings (`"""..."""`) for PL/pgSQL functions, explaining purpose, parameters, and return values.

## Naming Conventions

* **Tables:** Use plural nouns (e.g., `users`, `orders`). Avoid prefixes like `tbl_`.
* **Columns:** Use singular nouns (e.g., `user_id`, `order_date`). Be descriptive. Avoid naming a column `id` if a more descriptive name is available.
* **Primary Keys:** Name primary key columns as `{table_name_singular}_id` (e.g., `user_id`, `product_id`) or just `id` if preferred and unambiguous. Use `BIGINT` or `SERIAL`/`BIGSERIAL` (auto-incrementing integers). UUIDs are also a good option.
* **Foreign Keys:** Name foreign key columns as `{referenced_table_singular}_id` (e.g., `user_id` in the `orders` table referencing `users`).
* **Indexes:** Use a consistent prefix like `idx_` or `ix_`, followed by the table name and column(s) (e.g., `idx_users_email`, `ix_orders_customer_id_order_date`).
* **Functions/Procedures:** Use lowercase `snake_case`. Names should ideally contain a verb for procedures.
* **Constraints:** Use consistent naming patterns:
  * Primary key: `pk_{table_name}` (e.g., `pk_users`)
  * Foreign key: `fk_{table_name}_{referenced_table_name}` (e.g., `fk_orders_users`)
  * Unique: `uq_{table_name}_{column(s)}` (e.g., `uq_users_email`)
  * Check: `ck_{table_name}_{constraint_description}` (e.g., `ck_products_price_positive`)
* **Triggers:** Use `trg_{table_name}_{event}_{action}` (e.g., `trg_users_before_update_set_modified_at`).
* **Views:** Use descriptive, purpose-indicating names (e.g., `active_users_view`, `monthly_order_summary`).
* **Avoid Reserved Words:** Do not use SQL reserved keywords as object names. Check `pg_get_keywords()`.

## Schema Design

* Use schemas to logically group related database objects:
  * `public` - Core application tables
  * `auth` - Authentication and authorization
  * `audit` - Audit logging
  * `reporting` - Views and functions for reports
* Consider versioning schemas for major API changes (e.g., `api_v1`, `api_v2`).
* Design with extensibility in mind—anticipate future changes where reasonable.
* Use junction/pivot tables for many-to-many relationships with descriptive names (e.g., `user_roles` for users-to-roles relationship).
* Consider "soft deletes" (using an `is_deleted` boolean or `deleted_at` timestamp) for important data that shouldn't be permanently removed.
* Add metadata columns consistently:
  * `created_at` (TIMESTAMPTZ NOT NULL DEFAULT NOW())
  * `updated_at` (TIMESTAMPTZ)
  * `created_by` (reference to users table where applicable)
  * `updated_by` (reference to users table where applicable)

## Data Types

* Use the most specific and appropriate data type for each column:
* **Text:**
  * Prefer `VARCHAR` or `TEXT` over `CHAR(n)`. Use `VARCHAR(n)` only when a specific length limit is strictly required.
  * Use `TEXT` for unlimited length text without performance penalty.
  * Consider `CITEXT` for case-insensitive text storage and comparison.
* **Numeric:**
  * Use `INTEGER` or `BIGINT` for whole numbers. Avoid `SMALLINT` unless storage is critical.
  * Use `NUMERIC(p,s)` for exact precision decimals (e.g., monetary values), specifying precision and scale. `MONEY` type can also be used.
  * Use `FLOAT` (8-byte) or `REAL` (4-byte) only when approximate precision is acceptable (e.g., scientific measurements, coordinates). Avoid equality checks (`=`) with floating-point types.
* **Boolean:** 
  * Use `BOOLEAN` for true/false values.
  * Consider naming boolean columns as questions (e.g., `is_active`, `has_subscription`).
* **Dates/Times:**
  * Use `TIMESTAMP WITH TIME ZONE` (`TIMESTAMPTZ`) for most timestamp needs to avoid ambiguity.
  * Use `DATE` for dates only.
  * Use `TIME WITH TIME ZONE` for times that need timezone awareness.
  * Store dates/times in UTC where possible.
* **Specialized Types:**
  * Use `UUID` for unique identifiers where appropriate, especially for distributed systems.
  * Use `ENUM` types for columns with a fixed, limited set of possible string values.
  * Use `JSONB` for storing JSON data; it's generally preferred over `JSON` due to indexing capabilities and efficiency.
  * Use `ARRAY` types when appropriate (e.g., tags, categories) but be cautious about querying complexity.
  * Consider `INET`/`CIDR` for IP addresses, `MAC` for MAC addresses.
  * Use `POINT`, `LINE`, `POLYGON` and other geometric types for spatial data.

## Querying & SQL Practices

* Avoid `SELECT *`. Explicitly list the columns you need. This improves readability, performance, and reduces risks if the table structure changes.
* Use parameterized queries (prepared statements) in application code to prevent SQL injection. Most ORMs and database libraries handle this automatically.
* Use aliases for table names in joins for clarity, especially in complex queries. Choose meaningful aliases:
  ```sql
  SELECT u.username, o.order_date
  FROM users u
  JOIN orders o ON u.user_id = o.user_id
  ```
* Prefer `JOIN` syntax over older comma-based joins in the `WHERE` clause.
* Use `UNION ALL` instead of `UNION` if duplicate removal is not necessary, as it's more performant.
* Use `WHERE` clauses effectively to filter data as early as possible.
* Avoid functions or complex operations on indexed columns in `WHERE` clauses, as this can prevent index usage.
* Use Common Table Expressions (CTEs) with `WITH` for complex queries to improve readability:
  ```sql
  WITH active_users AS (
    SELECT user_id, username 
    FROM users 
    WHERE is_active = true
  )
  SELECT * FROM active_users WHERE ...
  ```
* Use window functions (e.g., `ROW_NUMBER()`, `RANK()`) instead of subqueries when appropriate.
* Use `RETURNING` clause with `INSERT`/`UPDATE`/`DELETE` statements to get affected rows.
* Consider `LATERAL` joins for correlated subqueries.
* Use `EXPLAIN ANALYZE` to understand query plans and identify performance bottlenecks.
* For large result sets, use `CURSOR` or `LIMIT`/`OFFSET` with pagination.
* Avoid excessive use of `OR` conditions when `IN` or `UNION` would be more readable and efficient.

## Indexing

* Index columns frequently used in `WHERE` clauses, `JOIN` conditions (`ON`), and `ORDER BY` clauses.
* Avoid indexing very large columns directly. Consider functional indexes on hashes or partial indexes. Limit indexed column size where possible.
* Use multi-column (composite) indexes for queries filtering on multiple columns simultaneously. Order matters in composite indexes—place most selective columns first.
* Consider partial indexes for large tables where queries frequently target a specific subset of rows:
  ```sql
  CREATE INDEX idx_orders_user_id_completed ON orders(user_id) WHERE status = 'completed';
  ```
* Use `GIN` indexes for `JSONB`, full-text search, and array columns.
* Use `GIST` indexes for geometric data types and exclusion constraints.
* Use `CREATE INDEX CONCURRENTLY` to avoid locking the table during index creation on production systems.
* Consider `BRIN` indexes for very large tables with naturally ordered data (e.g., timestamps).
* Use functional indexes for queries that filter on expressions:
  ```sql
  CREATE INDEX idx_users_lower_email ON users(lower(email));
  ```
* Regularly review index usage with `pg_stat_user_indexes` and drop unused indexes, as they incur write overhead.
* Monitor index size and bloat with `pgstatindex`.

## Constraints

* Use constraints (`PRIMARY KEY`, `FOREIGN KEY`, `UNIQUE`, `CHECK`, `NOT NULL`) to enforce data integrity at the database level.
* Define `FOREIGN KEY` constraints with appropriate `ON DELETE` / `ON UPDATE` actions (e.g., `CASCADE`, `SET NULL`, `RESTRICT`).
* Use `CHECK` constraints to enforce business rules:
  ```sql
  ALTER TABLE products ADD CONSTRAINT ck_products_price_positive CHECK (price > 0);
  ```
* Use `EXCLUSION` constraints for complex uniqueness requirements (e.g., time period overlaps).
* Consider deferrable constraints for complex transaction scenarios.
* Add `NOT NULL` constraints to all columns unless nulls have explicit meaning.
* Use triggers to implement complex constraints that can't be expressed with declarative constraints.

## Functions & Procedures (PL/pgSQL)

* Use PL/pgSQL for complex logic that needs to reside within the database (e.g., triggers, complex calculations).
* Keep functions focused on a single task.
* Use explicit `RETURN` types.
* Declare variables with appropriate types. Use `_` prefix for local variables/parameters to avoid clashes with column names.
* Handle exceptions using `BEGIN...EXCEPTION...END` blocks where necessary.
* Use `RAISE NOTICE/INFO/WARNING/EXCEPTION` appropriately for messaging and error handling.
* Consider performance implications of functions. Set `COST` and `ROWS` appropriately for functions that will be used in query planning.
* Use `IMMUTABLE`, `STABLE`, or `VOLATILE` function attributes appropriately.
* Add appropriate security context with `SECURITY DEFINER` or `SECURITY INVOKER` (default).
* Comment functions comprehensively, including examples.

## Transactions & Concurrency

* Use explicit transactions with `BEGIN`, `COMMIT`, and `ROLLBACK` for multi-statement operations.
* Set appropriate isolation levels for transactions based on needs:
  * `READ COMMITTED` (default) - No dirty reads, but non-repeatable reads and phantom reads possible
  * `REPEATABLE READ` - No dirty or non-repeatable reads, but phantom reads possible
  * `SERIALIZABLE` - No dirty reads, non-repeatable reads, or phantom reads
* Use optimistic locking with version columns for high-concurrency applications.
* Consider advisory locks for application-level locking.
* Be aware of and handle deadlock scenarios (`ERROR: deadlock detected`).
* Use `SELECT FOR UPDATE` or `SELECT FOR SHARE` when needed to lock rows during a transaction.
* Keep transactions as short as practical to minimize lock contention.

## Security

* Follow the principle of least privilege for database user roles. Grant only necessary permissions.
* Create roles for different access patterns (read-only, admin, application).
* Use row-level security policies for multi-tenant applications or fine-grained access control.
* Consider column-level privileges for sensitive data.
* Sanitize all input in application code before incorporating it into queries (Parameterization is key).
* Use `pg_hba.conf` to restrict network access appropriately.
* Never store plain-text passwords - use cryptographic hashing with salt (e.g., with pgcrypto's `crypt()` function).
* Keep PostgreSQL server and client libraries updated with security patches.
* Use strong, unique passwords for database users.
* Consider encrypting sensitive data at rest (e.g., using `pgcrypto`) and always use SSL/TLS for connections in transit.
* Audit sensitive operations using triggers or logging.
* Regularly review database permissions with `\\dp` or `pg_catalog.pg_tables`.

## Performance & Maintenance

* Normalize database design to reduce data redundancy (usually up to 3NF is sufficient).
* Consider selective denormalization only when proven performance benefits outweigh data integrity risks.
* Understand transaction isolation levels and use the appropriate level for your application's needs.
* Run `VACUUM` and `ANALYZE` regularly (Autovacuum is usually enabled and recommended). Monitor autovacuum activity. Never run `VACUUM FULL` casually as it locks the table.
* Configure appropriate PostgreSQL settings based on hardware:
  * `shared_buffers` (typically 25% of RAM)
  * `effective_cache_size` (typically 75% of RAM)
  * `work_mem` (depends on query complexity)
  * `maintenance_work_mem` (for maintenance operations)
  * `max_connections` (based on application needs)
* Use connection pooling (e.g., PgBouncer, Odyssey) for applications with many connections.
* Monitor database performance using tools like:
  * `pg_stat_statements` for query statistics
  * `pg_stat_activity` for current activity
  * `pg_stat_user_tables` for table statistics
  * `pg_stat_user_indexes` for index usage
* For bulk data loading, consider:
  * Disabling constraints/indexes temporarily
  * Using `COPY` instead of multiple INSERT statements
  * Adjusting `maintenance_work_mem` and `max_wal_size` temporarily
  * Using partitioning for very large tables
* Implement appropriate backup strategies (pg_dump, WAL archiving, PITR).
* Set up streaming replication for high availability and read scaling.
* Consider table partitioning (table inheritance or declarative partitioning) for very large tables.
* Use connection pooling to manage database connections efficiently.

## PostgreSQL-Specific Features

* Leverage PostgreSQL's rich set of extensions:
  * `PostGIS` for geospatial data
  * `pg_stat_statements` for query statistics
  * `pgcrypto` for cryptographic functions
  * `uuid-ossp` for UUID generation
  * `pg_trgm` for trigram-based text search
  * `hstore` for key-value storage
  * `ltree` for hierarchical data
* Use materialized views for complex reporting queries.
* Consider logical replication for selective data replication.
* Use table inheritance or partitioning for time-series or categorical data.
* Implement full-text search using `tsvector`, `tsquery`, and GIN indexes.
* Utilize foreign data wrappers (FDW) to integrate with external data sources.
* Implement custom aggregates, operators, and types as needed.

## Migrations and Schema Evolution

* Version control your database schema using migration tools.
* Make schema changes backward compatible when possible.
* Prefer additive changes (adding columns, tables) over destructive ones.
* For production changes, consider:
  * Impact on existing queries and application code
  * Locking implications during changes
  * Data migration requirements
  * Rollback procedures
* Use `ALTER TABLE ... ADD COLUMN ... DEFAULT ...` for non-NULL columns to avoid table rewrites.
* Test migrations thoroughly in staging environments.
* Have a clear rollback plan for every schema change.
* Document schema changes and maintain a schema version number.
