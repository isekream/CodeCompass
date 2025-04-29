# Database Schema Design Rules (General / Relational)

## General Principles

* **Purpose Driven:** Design the schema to accurately represent the business domain and efficiently support the application's primary data access patterns and queries.
* **Clarity & Simplicity:** Aim for a clear, understandable, and simple schema design. Avoid unnecessary complexity.
* **Data Integrity:** Ensure data integrity through the use of appropriate data types, constraints (NOT NULL, UNIQUE, CHECK), primary keys, and foreign keys.
* **Consistency:** Maintain consistency in naming, data types, and design patterns across the entire schema.
* **Scalability & Performance:** Consider scalability and performance implications during design (e.g., indexing, normalization/denormalization trade-offs).

## Naming Conventions

* **Consistency:** Choose *one* naming convention and apply it consistently across all database objects (tables, columns, indexes, constraints, views, etc.). Common conventions include `snake_case` (preferred by many for SQL) or `PascalCase`.
* **Clarity:** Use clear, descriptive, and unambiguous names. Avoid excessive abbreviations or cryptic names. Prefer full words (e.g., `order_id` instead of `oid`).
* **Case:** Prefer using lowercase for `snake_case` (e.g., `customer_orders`) or `PascalCase` (e.g., `CustomerOrders`) consistently. Avoid mixed case identifiers that might require quoting.
* **Table Names:**
    * Use singular nouns (e.g., `customer`, `order`, `product`). A table represents a collection of a single type of entity.
    * Avoid prefixes like `tbl_` unless mandated by a specific legacy context.
* **Column Names:**
    * Use singular nouns describing the attribute (e.g., `first_name`, `order_date`, `total_amount`).
    * Include units in names for numeric values where ambiguity exists (e.g., `weight_kg`, `duration_seconds`).
* **Primary Key Columns:** Consistently name primary key columns (e.g., `id` or `table_name_id` like `customer_id`). Using `id` is common and often simpler.
* **Foreign Key Columns:** Name foreign key columns consistently, typically referencing the table and column they point to (e.g., `customer_id` in the `orders` table references the `id` column in the `customer` table).
* **Avoid Reserved Words:** Do not use SQL reserved keywords (like `ORDER`, `TABLE`, `GROUP`, `SELECT`) as object names.
* **Avoid Spaces & Special Characters:** Do not use spaces or special characters (other than underscore in `snake_case`) in object names to avoid the need for quoting identifiers.

## Data Types

* **Choose Appropriately:** Select the most specific and smallest data type that accurately represents the domain of the attribute and accommodates all valid current and future values.
    * **Integers:** Use `TINYINT`, `SMALLINT`, `INT`, `BIGINT` based on the required range. Use `UNSIGNED` if negative values are impossible (if supported and appropriate).
    * **Exact Numerics:** Use `DECIMAL` or `NUMERIC` for monetary values or where exact precision is required. Define precision and scale correctly.
    * **Approximate Numerics:** Use `FLOAT` or `REAL` (double precision) only for scientific calculations where approximation is acceptable. Avoid for monetary values.
    * **Strings:** Use `VARCHAR(n)` for variable-length strings, specifying a sensible maximum length `n`. Avoid overly large `n` values if not needed. Use `CHAR(n)` only for fixed-length strings. Use `TEXT` types for very large strings, but understand potential performance implications. Use Unicode types (`NVARCHAR`, `NCHAR`) when international character support is required.
    * **Dates/Times:** Use specific date/time types (`DATE`, `TIME`, `TIMESTAMP`, `TIMESTAMPTZ` - timestamp with time zone). Prefer `TIMESTAMPTZ` (or equivalent storing timezone info/UTC) for storing points in time to avoid ambiguity.
    * **Booleans:** Use native `BOOLEAN` type if available; otherwise, use `BIT` or a constrained `SMALLINT`/`TINYINT` (e.g., 0 or 1).
    * **Binary Data:** Use `BLOB` or `BYTEA` types for binary large objects. Consider storing large files outside the database (e.g., object storage) and referencing them if appropriate.
    * **UUIDs:** Use native `UUID` type if available for universally unique identifiers.
    * **JSON:** Use native `JSON` or `JSONB` types if storing semi-structured JSON data within a column. `JSONB` (binary format) is often preferred for indexing and query performance.
* **Consistency:** Use consistent data types for the same logical attribute across different tables (e.g., `customer_id` should have the same integer type in `customers` and `orders` tables).

## Keys & Relationships

* **Primary Keys (PK):**
    * Every table MUST have a primary key to uniquely identify each row.
    * Prefer simple, single-column, numeric (e.g., `INT` or `BIGINT` using sequences/auto-increment) or `UUID` primary keys over composite keys or natural keys (like email addresses or usernames) unless there's a strong domain reason. Surrogate keys are generally easier to manage and perform better for joins.
* **Foreign Keys (FK):**
    * Use foreign key constraints to enforce referential integrity between related tables.
    * Ensure the data type of the foreign key column exactly matches the data type of the referenced primary key column.
    * Define appropriate `ON DELETE` and `ON UPDATE` actions (e.g., `CASCADE`, `SET NULL`, `RESTRICT`, `NO ACTION`) based on business rules. Be cautious with `CASCADE`.
* **Relationships:** Clearly define relationships (one-to-one, one-to-many, many-to-many). Implement many-to-many relationships using a junction/join table containing foreign keys to the two related tables.

## Normalization & Denormalization

* **Start Normalized:** Design schemas initially following normalization principles (typically up to Third Normal Form - 3NF) to reduce data redundancy and improve data integrity.
    * **1NF:** Ensure all column values are atomic (no repeating groups or multi-valued cells). Each row is unique (requires a PK).
    * **2NF:** Meet 1NF, and ensure all non-key attributes are fully functionally dependent on the *entire* primary key (relevant for composite keys).
    * **3NF:** Meet 2NF, and ensure no non-key attribute is transitively dependent on the primary key (i.e., non-key attributes shouldn't depend on other non-key attributes).
* **Denormalize Strategically:** Introduce denormalization (adding redundant data or pre-calculated values) *selectively* and *purposefully* only when necessary to optimize specific, critical read query performance after profiling indicates a bottleneck caused by joins or aggregations.
    * **Common Cases:** Adding frequently needed data from a related table to avoid joins in high-frequency queries; storing calculated/aggregated values (e.g., order total) that are expensive to compute on the fly.
    * **Document:** Clearly document any denormalization, the reason for it, and the mechanisms used to keep the redundant data consistent (e.g., triggers, application logic, scheduled jobs).
    * **Trade-offs:** Understand that denormalization increases storage requirements, complicates data updates (requiring updates in multiple places), and increases the risk of data inconsistency if not managed carefully.

## Indexing

* **Index Primary Keys:** Databases automatically index primary keys.
* **Index Foreign Keys:** Create indexes on foreign key columns to significantly improve join performance. Some databases do this automatically, but verify.
* **Index Common Query Columns:** Create indexes on columns frequently used in `WHERE` clauses, `ORDER BY` clauses, and `GROUP BY` clauses to speed up filtering, sorting, and aggregation.
* **Selectivity:** Prefer indexing columns with high selectivity (high cardinality / many unique values). Indexes on low-cardinality columns (e.g., boolean flags, gender) are often ineffective or even detrimental unless specific query patterns benefit (e.g., covering indexes, bitmap indexes if supported/appropriate).
* **Composite Indexes:** Create multi-column (composite) indexes for queries that filter or sort on multiple columns simultaneously. The order of columns in the index definition matters greatly and should match the query patterns (most selective columns often first, or columns used in equality predicates first).
* **Covering Indexes:** Consider creating covering indexes (where all columns needed by a specific query are included in the index itself) to allow the database to answer the query from the index alone without accessing the table data.
* **Avoid Over-Indexing:** Do not index every column. Each index consumes storage space and adds overhead to write operations (`INSERT`, `UPDATE`, `DELETE`). Regularly review index usage and drop unused indexes.
* **Analyze Query Plans:** Use the database's query plan analysis tools (`EXPLAIN`, `EXPLAIN ANALYZE`) to understand how queries are executed and whether indexes are being used effectively.

## Documentation & Maintenance

* **Document the Schema:** Maintain clear documentation for the database schema. This can include:
    * An Entity-Relationship Diagram (ERD).
    * Descriptions for tables and columns (use database comment features if available).
    * Explanations of relationships, constraints, and business rules.
    * Documentation for any denormalization or non-standard design choices.
* **Version Control:** Keep schema definitions and migration scripts under version control (e.g., using database migration tools like Flyway, Liquibase, or framework-specific tools like Alembic, EF Core Migrations, Rails Migrations).
* **Review Changes:** Treat schema changes like code changes. Review proposed changes (especially migrations) carefully before applying them, particularly in production environments.
* **Regular Review:** Periodically review the schema design to ensure it still meets application needs and performance requirements as the application evolves.
