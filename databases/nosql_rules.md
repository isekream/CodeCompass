# NoSQL Database Rules

## General Principles

* **Choose the Right Type:** Select the NoSQL database type (Document, Key-Value, Column-Family, Graph) that best fits the application's data structure, access patterns, and scalability requirements. Don't default to one type for all use cases.
* **Understand Trade-offs:** Be aware of the trade-offs involved, particularly regarding the CAP theorem (Consistency, Availability, Partition Tolerance) and the BASE model (Basically Available, Soft state, Eventually consistent) common in NoSQL. Choose consistency levels appropriate for the application's needs.
* **Application-Driven Design:** Design NoSQL schemas primarily based on the application's query patterns and data access needs, rather than normalizing data purely based on entity relationships like in SQL.

## Data Modeling

* **Query-Driven Modeling:** Identify the most frequent and critical queries *first*, then design the data model to optimize for those queries.
* **Denormalization:** Embrace denormalization where appropriate. Duplicate data if it significantly improves read performance for common queries and avoids complex joins (which are often inefficient or unsupported). Be aware this increases storage and requires managing data consistency during updates.
* **Embedding vs. Referencing:**
    * **Embed** related data within a single document/record when data is accessed together, represents a "contains" relationship (one-to-few), and doesn't grow unboundedly. This optimizes reads by fetching related data in one operation.
    * **Reference** (using IDs/keys to link to other documents/records) when dealing with one-to-many or many-to-many relationships, when embedded data would become too large (exceeding document size limits, e.g., 16MB in MongoDB), or when referenced data is frequently updated independently.
* **Avoid SQL Habits:** Do not try to impose a relational/normalized SQL model directly onto a NoSQL database. Think in terms of aggregates, documents, or key-value structures appropriate for the chosen database type.
* **Schema Flexibility:** Leverage schema flexibility, but establish application-level consistency and validation where needed. While schemas can evolve easily, inconsistent data structures within the same collection/table can complicate querying and application logic. Consider schema validation features offered by some databases (e.g., MongoDB).
* **Primary Keys / Partition Keys:** Choose primary keys (or partition keys in distributed databases like Cassandra/ScyllaDB) carefully.
    * Ensure high cardinality (many unique values) for partition keys to distribute data evenly across nodes and avoid "hot spots".
    * Design keys based on query patterns. Use composite keys if queries often filter on multiple attributes.
* **Data Lifecycle:** Consider data access frequency and implement TTL (Time-To-Live) or archival strategies for data that becomes less relevant over time.

## Querying & Indexing

* **Index Appropriately:** Create indexes on fields used frequently in query filters, sort operations, or joins (if supported). Indexes are crucial for performance but add overhead to writes and consume storage.
* **Targeted Queries:** Write queries that are as specific as possible to leverage indexes effectively. Avoid full collection/table scans.
* **Understand Query Capabilities:** Be aware of the specific query capabilities and limitations of the chosen NoSQL database. Not all NoSQL databases support ad-hoc queries, complex aggregations, or joins efficiently.
* **Composite Indexes:** Use composite indexes for queries filtering or sorting on multiple fields. The order of fields in the index matters.
* **Avoid Over-Indexing:** Don't create indexes for every field. Analyze query patterns and index strategically. Remove unused indexes.

## Performance & Scalability

* **Horizontal Scaling:** Design data models and choose databases that support horizontal scaling (sharding/partitioning) effectively. Understand the partitioning strategy (e.g., hash-based, range-based) and its implications.
* **Caching:** Implement caching strategies (application-level, external caches like Redis/Memcached, database-integrated caching) to reduce load on the database for frequently accessed, rarely changing data.
* **Bulk Operations:** Use bulk write operations (batch inserts, updates, deletes) instead of single operations within a loop when processing multiple records, as this significantly reduces network overhead and improves throughput.
* **Connection Pooling:** Use connection pooling in the application layer to manage connections efficiently and avoid the overhead of establishing new connections for each query.
* **Monitoring:** Continuously monitor database performance metrics (latency, throughput, error rates, resource utilization, query execution times, node distribution). Use database-specific monitoring tools and general APM solutions.
* **Rightsizing:** Provision appropriate resources (CPU, RAM, disk IOPS) for database nodes based on monitoring data.

## Consistency

* **Understand Consistency Models:** Be aware of the consistency model offered by the database (e.g., strong consistency, eventual consistency, tunable consistency).
* **Tunable Consistency:** If the database offers tunable consistency (e.g., Cassandra, ScyllaDB), configure read and write consistency levels based on the application's tolerance for stale data versus availability requirements. For example, `QUORUM` reads/writes provide a balance.
* **Application Design:** Design applications to handle potential data staleness if using an eventually consistent model. Implement conflict resolution strategies if necessary.

## Security

* **Authentication:** Enable and enforce strong authentication mechanisms for accessing the database. Avoid default credentials.
* **Authorization:** Implement fine-grained access control (e.g., role-based access control - RBAC) based on the principle of least privilege. Grant users/applications only the permissions they need.
* **Encryption:**
    * Encrypt data in transit using TLS/SSL between the application and the database.
    * Encrypt sensitive data at rest using database-level encryption features or application-level encryption.
* **Input Validation & Sanitization:** Validate and sanitize all user input in the application layer *before* constructing queries to prevent NoSQL injection attacks. Use parameterized queries or safe query builders provided by drivers/ODMs whenever possible. Avoid constructing queries directly from raw user input.
* **Network Security:** Deploy databases within private networks (VPCs). Use firewalls or security groups to restrict network access to database ports only from trusted application servers.
* **Auditing:** Enable audit logging to track database access and administrative actions. Regularly review logs for suspicious activity.
* **Updates & Patching:** Keep the NoSQL database software, drivers, and underlying OS updated with the latest security patches.

## Specific Database Type Considerations

### Document Databases (e.g., MongoDB, Couchbase, DocumentDB)

* **Schema Design:**
    * Leverage nested documents and arrays for related data accessed together.
    * Be mindful of document size limits (e.g., 16MB in MongoDB). Avoid unbounded arrays within documents.
    * Use appropriate indexing for nested fields and arrays if queried frequently.
* **Performance:**
    * Utilize covered queries where possible (queries that can be satisfied entirely using an index).
    * For MongoDB, use projection (`{field: 1}`) to return only needed fields.
    * Consider data compression options to reduce storage and improve I/O performance.
* **Consistency:**
    * Understand write concerns (e.g., `w: "majority"`) and read concerns to balance between consistency and performance.
    * Use transactions for operations that must be atomic (if supported).

### Key-Value Stores (e.g., Redis, Memcached, DynamoDB)

* **Key Design:**
    * Design keys effectively for efficient lookups. Consider patterns like `object-type:id`.
    * Keep keys reasonably short but descriptive. Very long keys consume memory.
    * Consider key expiry and eviction policies for caching use cases.
* **Value Considerations:**
    * Understand the data types supported for values (simple strings, numbers, complex objects).
    * Be mindful of value size limits (e.g., 512MB in Redis, 1MB in DynamoDB).
* **Access Patterns:**
    * Primarily optimized for direct key lookups; complex queries or range scans might be inefficient or unsupported.
    * Often used for caching, session management, real-time data, rate limiting, and simple counters.
* **Persistence:**
    * Configure persistence options appropriately (e.g., Redis RDB snapshots, AOF logs).

### Column-Family Stores (e.g., Cassandra, ScyllaDB, HBase)

* **Table Design:**
    * Design wide rows for related data that will be accessed together.
    * Group data with similar access patterns in the same column family.
    * Choose partition keys and clustering columns based on query patterns.
* **Query Patterns:**
    * Design tables specifically for each query pattern, as ad-hoc queries are inefficient.
    * Avoid full table scans whenever possible.
* **Time-Series Data:**
    * Often well-suited for time-series data with high write throughput.
    * Consider TTL features for automatic data expiration.

### Graph Databases (e.g., Neo4j, JanusGraph, Neptune)

* **Graph Modeling:**
    * Focus on relationships (edges) as first-class entities with properties.
    * Use meaningful node and relationship types.
    * Avoid overly complex node properties; consider normalizing complex properties into related nodes.
* **Querying:**
    * Learn the specific query language (e.g., Cypher for Neo4j, Gremlin for JanusGraph).
    * Understand query optimization for traversals.
* **Use Cases:**
    * Best suited for highly connected data with complex relationships (social networks, recommendation engines, fraud detection, network analysis).

## Common Antipatterns to Avoid

* **Treating NoSQL Like SQL:** Forcing relational modeling techniques onto NoSQL databases.
* **One Collection/Table for Everything:** Using a single collection/table for multiple entity types with different access patterns.
* **Unbounded Growth:** Embedding data in arrays that can grow without limits, leading to performance issues and document size limits.
* **Insufficient Indexing:** Failing to index fields used in query conditions, especially in large collections.
* **Over-Indexing:** Creating too many indexes, which slows down write operations.
* **Ignoring Cardinality:** Using low-cardinality fields as partition keys in distributed databases.
* **Sequential IDs as Partition Keys:** Using sequential IDs as partition keys, creating hot spots on specific nodes.
* **Complex Transactions:** Relying on multi-document/record transactions in databases where they are not efficient.
* **Ignoring Consistency Requirements:** Not considering the consistency needs of the application when choosing a NoSQL database.
