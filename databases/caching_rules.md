# Caching Rules (Redis/Memcached)

## General Principles

* **Identify Need:** Implement caching strategically where it provides clear benefits, such as for frequently accessed, rarely changing data, or computationally expensive results. Don't cache everything.
* **Data Access Patterns:** Analyze data access patterns (read/write ratio, frequency, data size) to determine what to cache and for how long.
* **Performance Goals:** Define clear performance goals (e.g., target latency reduction, reduced database load) to measure the effectiveness of the cache.
* **Stale Data Tolerance:** Understand the application's tolerance for stale data. This directly influences the choice of caching patterns and invalidation strategies.
* **Simplicity:** Start with simpler caching strategies (e.g., Cache-Aside) and evolve as needed. Avoid over-engineering.

## Cache Keys & Data

* **Key Naming Convention:** Use a consistent, descriptive, and well-structured naming convention for cache keys (e.g., `object-type:id:field`, `user:123:profile`). Use delimiters like `:` or `_` consistently.
* **Key Granularity:** Choose appropriate key granularity. Caching smaller, specific pieces of data can be more efficient than caching large, monolithic objects if only parts are needed.
* **Avoid Sensitive Data:** Do NOT store highly sensitive data (passwords, unencrypted PII) in the cache unless absolutely necessary and secured appropriately (see Security section).
* **Serialization:** Choose an efficient serialization format (e.g., JSON, MessagePack, Protobuf) if storing complex objects. Be mindful of serialization/deserialization overhead. Consider storing simple strings/numbers directly if possible.

## Caching Patterns

* **Cache-Aside (Lazy Loading):**
    * **Flow:** Application checks cache first. If miss, fetch from data source, store in cache, return data. If hit, return data from cache.
    * **Pros:** Resilient to cache failures, only requested data is cached. Most common pattern.
    * **Cons:** Cache misses result in higher latency (cache check + data source fetch). Potential for stale data between data source update and next cache miss.
    * **Use Case:** Read-heavy workloads where some data staleness is acceptable.
* **Read-Through:**
    * **Flow:** Application asks cache for data. Cache checks internally; if miss, cache fetches from data source, stores it, and returns to application.
    * **Pros:** Application logic is simpler (only talks to cache).
    * **Cons:** Requires cache provider support. Initial cache miss latency.
    * **Use Case:** Similar to cache-aside, often handled by ORMs or frameworks.
* **Write-Through:**
    * **Flow:** Application writes data to cache AND data source simultaneously (or sequentially, ensuring both succeed).
    * **Pros:** High data consistency between cache and data source.
    * **Cons:** Higher write latency (must write to both). Cache can fill with data that isn't read frequently.
    * **Use Case:** Applications requiring strong consistency and where write latency is acceptable.
* **Write-Back (Write-Behind):**
    * **Flow:** Application writes data only to the cache initially. Cache asynchronously writes data back to the data source later.
    * **Pros:** Very low write latency. Reduces load on the data source.
    * **Cons:** Higher risk of data loss if cache fails before data is persisted. Eventual consistency between cache and data source. More complex to implement correctly.
    * **Use Case:** Write-heavy workloads where occasional data loss on failure is tolerable and peak performance is critical.
* **Write-Around:**
    * **Flow:** Application writes data directly to the data source, bypassing the cache. Data is only loaded into the cache on a subsequent read miss (like cache-aside).
    * **Pros:** Reduces cache churn from write operations that aren't subsequently read.
    * **Cons:** Read misses for recently written data are slower.
    * **Use Case:** Workloads where recently written data is unlikely to be read immediately.

## Cache Invalidation & Eviction

* **Time-To-Live (TTL):** Set appropriate TTLs for cached items based on data volatility and tolerance for staleness. Avoid indefinite TTLs unless data truly never changes or has another invalidation mechanism.
* **Explicit Invalidation:** When the source data changes, actively invalidate or update the corresponding cache entry. This can be done via:
    * **Manual/Event-Driven:** Application logic explicitly deletes/updates cache keys upon data modification (e.g., after a successful database update).
    * **Write-Through/Write-Back:** These patterns handle updates implicitly.
* **Eviction Policies:** Configure appropriate eviction policies for when the cache reaches its memory limit (e.g., LRU - Least Recently Used, LFU - Least Frequently Used, FIFO - First-In, First-Out). Choose based on access patterns. Memcached typically uses LRU. Redis offers more options.
* **Consistency:** Understand that achieving perfect consistency between cache and data source can be complex, especially in distributed systems. Choose strategies balancing consistency needs with performance.

## Performance & Scalability

* **Cache Location:** Place the cache network-close to the application servers to minimize latency.
* **Connection Pooling:** Use connection pooling in your application to efficiently manage connections to the cache server(s). Avoid opening/closing connections per request.
* **Pipelining (Redis):** Use Redis pipelining to send multiple commands at once without waiting for individual replies, reducing network round-trip time.
* **Bulk Operations:** Use bulk operations (e.g., `MGET`/`MSET` in Redis) where possible to fetch/set multiple keys in one command.
* **Avoid Expensive Commands:** Be cautious with commands that scan large parts of the keyspace or perform complex operations on the cache server (e.g., `KEYS` in Redis - use `SCAN` instead; complex Lua scripts).
* **Distributed Caching:** For high availability and scalability, use distributed caching solutions (Redis Cluster, Memcached cluster via client-side hashing, managed cloud services).
* **Sharding/Partitioning:** Distribute cache keys across multiple nodes/shards to scale memory capacity and throughput. Use consistent hashing or other sharding strategies supported by the client/server.
* **Rightsizing:** Monitor cache memory usage, CPU, and network. Provision instances with appropriate resources. Don't overprovision, but ensure enough memory to hold the working set and handle overhead.

## Monitoring

* **Key Metrics:** Monitor essential cache metrics:
    * **Cache Hit Rate:** (Hits / (Hits + Misses)) - Aim for a high hit rate.
    * **Latency:** Average/percentile latency for cache operations (GET, SET).
    * **Memory Usage:** Total memory used, fragmentation (Redis), evictions.
    * **CPU Utilization:** Cache server CPU load.
    * **Network Traffic:** Bytes in/out.
    * **Connections:** Number of active client connections.
    * **Evictions/Expirations:** Rate of items being removed due to TTL or memory pressure.
* **Alerting:** Set up alerts for critical issues (e.g., low hit rate, high latency, high eviction rate, high memory usage, cache node unavailability).

## Security

* **Network Isolation:** Run cache servers in private networks (VPCs). Restrict access using firewalls/security groups to only allow connections from necessary application servers on specific ports.
* **Authentication:** Enable authentication mechanisms (e.g., Redis `AUTH` with strong passwords, SASL for Memcached).
* **Encryption in Transit:** Use TLS to encrypt communication between applications and the cache server if traffic crosses untrusted networks.
* **Encryption at Rest:** While typically in-memory, if using persistence features (Redis) or managed services, ensure data at rest is encrypted where required.
* **Principle of Least Privilege:** Configure cache users/ACLs (Redis 6+) with the minimum required permissions.
* **Command Restriction:** Disable or rename potentially dangerous commands (e.g., `FLUSHALL`, `KEYS`, `CONFIG`) in production environments if possible (Redis).

## Redis Specifics

* **Data Types:** Leverage Redis's rich data types (Hashes, Lists, Sets, Sorted Sets, Streams, HyperLogLogs, Bitmaps, Geospatial) beyond simple Strings when they fit the use case, as they often provide more efficient operations server-side.
* **Persistence:** Understand Redis persistence options (RDB snapshots, AOF logs) and configure them based on durability requirements vs. performance impact. Persistence is often disabled if Redis is used *only* as a volatile cache.
* **Replication & Sentinel/Cluster:** Use Redis Replication for read scaling and high availability. Use Redis Sentinel for automated failover in master-slave setups, or Redis Cluster for sharding and high availability.
* **Lua Scripting:** Use Lua scripting for atomic, complex operations that require multiple commands, reducing network latency.

## Memcached Specifics

* **Simplicity:** Primarily a simple, fast, distributed in-memory key-value store for strings/objects.
* **Multi-Threaded:** Designed with a multi-threaded architecture, potentially offering high throughput on multi-core machines for simple GET/SET operations compared to single-threaded Redis (though Redis has multi-threaded I/O now).
* **No Persistence:** Memcached offers no built-in data persistence. Data is lost on restart/failure.
* **Data Types:** Primarily stores string values (objects are typically serialized by the client). Lacks the rich server-side data structures of Redis.
* **Distribution:** Scaling and distribution are typically handled client-side using consistent hashing libraries across multiple Memcached nodes.
* **Memory Management:** Uses a slab allocator, which can be efficient but might lead to wasted memory if item sizes vary greatly and don't align well with slab classes. Monitor evictions.

## Best Practices by Use Case

### Session Storage
* Use appropriate TTLs matching your session expiration policy.
* Consider Redis for advanced features like session data updates and activity tracking.
* Implement proper session key generation that's not predictable.

### API Response Caching
* Use content-based keys to avoid caching personalized responses incorrectly.
* Set TTLs based on data volatility and API rate limits.
* Consider response size when determining what to cache.

### Database Query Results
* Cache expensive or frequently executed queries.
* Ensure invalidation or TTL strategies match data update patterns.
* Be mindful of storing large result sets; consider pagination and caching smaller chunks.

### High-Traffic Web Pages
* Use fragment caching for dynamic page components rather than full-page caching when appropriate.
* Implement cache warming for critical pages before traffic spikes.
* Layer caching (CDN, application, database) for maximum performance.

### Rate Limiting
* Use Redis's atomic operations (INCR/EXPIRE) for implementing rate limiting counters.
* Consider sliding window algorithms for more precise rate limiting.
* Set appropriate expiration times based on rate limit windows.
