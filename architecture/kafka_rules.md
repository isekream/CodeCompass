# Apache Kafka Rules

## General Principles

* **Use Cases:** Understand Kafka's strengths (high-throughput, durable, distributed commit log) and use it for appropriate scenarios like event sourcing, log aggregation, stream processing, messaging, and decoupling services.
* **Idempotence:** Design consumers and producers to handle potential message duplicates gracefully (e.g., using idempotent producers, designing consumers to be idempotent).
* **Monitoring:** Implement comprehensive monitoring for brokers, producers, consumers, ZooKeeper/KRaft, and key metrics (latency, throughput, lag, errors).

## Topics

* **Naming Convention:**
    * Use meaningful, descriptive names reflecting the data content or business domain (e.g., `orders.processed`, `user.signup.events`).
    * Adopt a consistent convention (e.g., `snake_case` or `camelCase`).
    * Consider using namespaces/prefixes for environments or departments (e.g., `dev.orders.processed`, `prod.inventory.updates`).
    * Avoid sensitive information in topic names.
* **Partitioning:**
    * Choose an appropriate number of partitions based on expected throughput and consumer parallelism goals. More partitions allow higher parallelism (up to the number of consumers in a group).
    * Consider the partitioning key carefully. Keys determine which partition a message goes to. Use keys that distribute data evenly to avoid hot spots (e.g., `user_id`, `order_id`). If no key is provided, messages are typically round-robin distributed.
    * Messages with the same key always go to the same partition, guaranteeing order *within that partition* for that key.
    * Be mindful that increasing partitions later is easy, but decreasing them is difficult. Start reasonably and monitor. A number divisible by common consumer counts (e.g., 2, 3) can help balance load.
* **Replication:**
    * Set an appropriate replication factor (`replication.factor`) for fault tolerance. A minimum of 3 is recommended for production environments to tolerate at least one broker failure without data loss (assuming `min.insync.replicas=2`).
    * Understand the trade-off: higher replication increases durability and availability but also increases network traffic and storage requirements.
* **Retention:**
    * Configure data retention policies (`log.retention.hours`, `log.retention.bytes`) based on business needs, storage capacity, and compliance requirements. Default is often 7 days.
    * Consider using log compaction for topics where only the latest value for each key is important (e.g., user profiles, entity state).
* **Single Topic Per Application/Event Type:** Prefer using distinct topics for different types of events or applications rather than putting unrelated data into a single topic and relying solely on message content for filtering.

## Producers

* **Acknowledgements (`acks`):**
    * `acks=0`: Fire and forget. Lowest latency, no guarantee of delivery. Risk of data loss.
    * `acks=1`: Leader broker acknowledges. Default. Good balance, but data loss can occur if the leader fails before replication completes.
    * `acks=all` (or `-1`): Leader and all in-sync replicas (ISRs) acknowledge. Highest durability guarantee, highest latency. Use with `min.insync.replicas` broker setting (typically >= 2). **Recommended for critical data.**
* **Retries:** Configure `retries` (e.g., a high value like `Integer.MAX_VALUE`) and `delivery.timeout.ms` (e.g., `60000` or higher) to handle transient broker failures and leader elections without losing messages when using `acks=all`. Set `retry.backoff.ms` (e.g., `200`) to avoid overwhelming brokers during retries.
* **Idempotent Producer (`enable.idempotence=true`):** Enable idempotence (requires `acks=all`, `retries > 0`, `max.in.flight.requests.per.connection <= 5`). Ensures messages are written exactly once per producer session, preventing duplicates caused by retries. **Highly recommended.**
* **Batching (`batch.size`, `linger.ms`):**
    * Tune `batch.size` (e.g., 64KB, 128KB) and `linger.ms` (e.g., 5-25ms) to improve throughput. Larger batches reduce network overhead but increase latency. `linger.ms` forces sending after a timeout even if the batch isn't full. Avoid `linger.ms=0` in high-throughput scenarios.
* **Compression (`compression.type`):** Enable compression (`gzip`, `snappy`, `lz4`, `zstd`) to reduce network bandwidth and storage usage, especially for text-based data (JSON, XML). This trades CPU cycles for bandwidth/storage. `lz4` or `snappy` often provide a good balance.
* **Partitioning Key:** Choose a meaningful key for messages if ordering for related messages is required or if you want to control data locality. If no key is provided, messages are distributed round-robin across partitions.
* **Error Handling:** Handle errors returned by the `send()` call (e.g., via callbacks or returned Futures) to detect and potentially react to non-retriable errors.

## Consumers

* **Consumer Groups (`group.id`):**
    * Consumers processing the same logical stream of data should share the same `group.id`.
    * Kafka distributes partitions among consumers within the same group. Each partition is consumed by only *one* consumer in the group at any given time.
    * Use unique `group.id`s for different applications or logical consumer roles reading from the same topic(s).
* **Partition Assignment:** The number of consumers in a group should ideally be less than or equal to the number of partitions in the topic(s) they subscribe to. Consumers exceeding the partition count will remain idle. Keep consumer count consistent if possible, as adding/removing consumers triggers a rebalance, pausing consumption briefly.
* **Offset Management:**
    * `enable.auto.commit=true` (default): Offsets are committed automatically at intervals defined by `auto.commit.interval.ms`. Convenient but risks data loss (if consumer crashes after fetch but before processing) or duplicate processing (if consumer crashes after processing but before commit).
    * `enable.auto.commit=false`: **Recommended.** Manually commit offsets using `consumer.commitSync()` or `consumer.commitAsync()` *after* messages have been successfully processed. This provides "at-least-once" processing guarantees. Implementing "exactly-once" often requires transactional patterns or idempotent consumers.
* **Starting Position (`auto.offset.reset`):**
    * `latest` (default): Start consuming from the end of the log (only new messages) if no committed offset exists for the group.
    * `earliest`: Start consuming from the beginning of the log if no committed offset exists.
    * `none`: Throw an exception if no committed offset exists.
    * Choose based on application requirements. `latest` is common for real-time processing; `earliest` for reprocessing or initial data loading.
* **Polling Loop:** Process messages fetched via `consumer.poll()` efficiently. Avoid long-blocking operations within the poll loop, as this can delay heartbeats and cause the consumer to be considered dead, triggering a rebalance. Offload heavy processing to separate threads/async tasks if necessary.
* **Heartbeating & Session Timeout:** Tune `session.timeout.ms` (how long broker waits for heartbeat before deeming consumer dead) and `heartbeat.interval.ms` (how often consumer sends heartbeats). `heartbeat.interval.ms` should be significantly smaller than `session.timeout.ms` (e.g., 1/3). `max.poll.interval.ms` limits the time between `poll()` calls before the consumer is considered failed. Ensure message processing time is less than `max.poll.interval.ms`.
* **Error Handling:** Implement robust error handling within the consumer's processing logic. Decide on strategies for retrying transient errors (e.g., exponential backoff) or handling permanent errors (e.g., sending to a dead-letter queue). Handle deserialization errors gracefully.
* **Idempotent Consumers:** Design consumer logic to be idempotent (processing the same message multiple times yields the same result) if using at-least-once delivery semantics.

## Brokers & Cluster

* **Hardware:** Provision brokers with sufficient RAM (especially for page cache), CPU, fast disks (SSDs recommended for logs), and network bandwidth.
* **Replication (`min.insync.replicas`):** Set `min.insync.replicas` at the broker or topic level (e.g., to 2 for a replication factor of 3) to ensure writes are acknowledged by a minimum number of replicas when using `acks=all`, guaranteeing durability even if one replica fails.
* **JVM Tuning:** Tune JVM heap size (`-Xms`, `-Xmx`) and potentially garbage collection settings (G1GC often recommended) for brokers based on load and monitoring.
* **KRaft Mode:** Prefer running Kafka in KRaft mode (without ZooKeeper) for simplified architecture, faster controller failover, and better scalability, especially for new deployments (available in recent Kafka versions).
* **Monitoring:** Monitor key broker metrics: UnderReplicatedPartitions, IsrShrinksPerSec/IsrExpandsPerSec, ActiveControllerCount, Request latency (Produce, Fetch), NetworkProcessorAvgIdlePercent, Disk usage, CPU usage, JVM GC times.

## Security

* **Network Segmentation:** Run Kafka brokers in a private network segment (VPC). Limit access via firewalls/security groups.
* **Encryption:**
    * **In Transit:** Enable SSL/TLS encryption for communication between clients and brokers, and between brokers themselves.
    * **At Rest:** Use disk-level or filesystem-level encryption for data stored on broker disks. Kafka itself doesn't encrypt data at rest directly within the log files.
* **Authentication:** Enable client authentication using SASL (SCRAM, Kerberos/GSSAPI, OAuth) or mTLS (mutual TLS). Avoid unauthenticated access in production.
* **Authorization:** Configure Authorizers (using ACLs - Access Control Lists) to enforce fine-grained permissions (Read, Write, Create, Describe, etc.) on topics, consumer groups, and cluster resources based on the principle of least privilege. Deny access by default (`allow.everyone.if.no.acl.found=false`).
* **Regular Updates:** Keep Kafka brokers, clients, and dependencies updated with the latest security patches.

## Schema Management

* **Use Schemas:** Define clear schemas for messages, especially when producers and consumers are developed independently or evolve over time.
* **Schema Registry:** Use a Schema Registry (like Confluent Schema Registry or Apicurio) to manage and enforce schemas (Avro, Protobuf, JSON Schema).
    * Avro is often recommended due to its support for schema evolution and compact binary format.
* **Schema Evolution:** Define schema compatibility rules (Backward, Forward, Full, None) in the Schema Registry to manage changes without breaking existing producers or consumers.
    * `BACKWARD` (consumers using new schema can read old data - common default)
    * `FORWARD` (consumers using old schema can read new data)
    * `FULL` (both backward and forward compatible)
* **Best Practices for Evolution:** Add default values for fields you might remove later. Add aliases instead of renaming fields. Never delete required fields without careful planning and default values.

## Kafka Streams & ksqlDB

* **Stateless vs. Stateful Operations:** Understand the difference between stateless operations (map, filter) and stateful operations (aggregate, join, windowing). Stateful operations require state stores and have implications for fault tolerance.
* **Scaling:** Scale Kafka Streams applications by running multiple instances of the same application with the same `application.id`. Tasks (logical partitions in a topology) will be distributed among available instances.
* **State Stores:** Configure RocksDB state store parameters (`rocksdb.config.setter`) for optimal performance if using stateful operations. Monitor disk usage and performance of state stores.
* **Exactly-Once Semantics:** Enable exactly-once semantics (`processing.guarantee=exactly_once_v2`) for critical applications where duplicate processing is unacceptable, understanding the performance trade-offs.
* **Caching & Commits:** Tune `cache.max.bytes.buffering` and `commit.interval.ms` to balance throughput vs. latency. Larger cache and longer commit intervals improve throughput but increase potential data loss on failure.
* **Interactive Queries:** Expose state stores via Interactive Queries API when other applications need read-only access to the processed state.

## Kafka Connect

* **Connectors:** Use managed connectors from Confluent Hub or established community sources when possible instead of custom implementations.
* **Configuration:** Set appropriate batch sizes, poll intervals, and task counts based on throughput requirements and source/sink capabilities.
* **Converters:** Choose appropriate converters (e.g., Avro, JSON Schema, Protobuf) that align with your schema management strategy.
* **Dead Letter Queue:** Configure error handling and dead letter queues for handling problematic records without failing the entire connector.
* **Distributed Mode:** Run Connect in distributed mode for production environments to enable scaling and fault tolerance.
* **Monitoring:** Monitor connector tasks, task restarts, and lag metrics.
