# Event-Driven Architecture (EDA) Rules

## General Principles

* **Loose Coupling:** Design services (producers and consumers) to be loosely coupled. Producers should not need knowledge of specific consumers, and consumers should not depend on producer implementation details beyond the event contract.
* **Asynchronous Communication:** Embrace asynchronous communication via events as the primary interaction pattern between services where appropriate, improving resilience and scalability over synchronous request/response.
* **Responsiveness:** Design systems to react to events in near real-time where needed.
* **Scalability:** Leverage the decoupled nature of EDA to scale producer and consumer services independently based on load.
* **Domain-Driven Design (DDD):** Align events with significant occurrences (Domain Events) within your business domains and bounded contexts.

## Event Design & Schema

* **Event Naming:**
    * Use clear, descriptive, past-tense names for events indicating something that *has happened* (e.g., `OrderCreated`, `PaymentProcessed`, `InventoryUpdated`).
    * Adopt a consistent naming convention (e.g., `PascalCase`, `snake_case`) and potentially use namespaces (e.g., `ecommerce.order.OrderCreated`).
* **Event Granularity:** Strive for events that represent meaningful business state changes. Avoid overly chatty events (too small) or monolithic events (too large).
* **Event Payload:** Decide on the payload strategy:
    * **Event Notification:** Minimal data (e.g., IDs, resource URL). Consumers must query the source service for full details. Maximizes decoupling, but increases load on source service and latency for consumers.
    * **Event-Carried State Transfer:** Include relevant data directly within the event payload needed by most consumers. Reduces need for consumers to call back to the source, but increases coupling to the event schema and payload size.
    * **Hybrid:** A common approach is to include key identifiers and frequently needed data, but not necessarily the *entire* state.
* **Schema Definition:**
    * Define clear schemas for event payloads (e.g., using JSON Schema, Avro, Protobuf).
    * Use a Schema Registry (like Confluent Schema Registry, Apicurio) to manage, version, and validate schemas, especially in polyglot environments or large systems.
* **Schema Evolution:** Plan for schema evolution. Use schema versioning and define compatibility rules (e.g., Backward, Forward, Full) in the Schema Registry to allow producers and consumers to evolve independently without breaking each other. Favor additive changes and provide default values for new optional fields. Avoid renaming or deleting required fields without careful coordination.
* **Standard Metadata:** Include standard metadata in all events (e.g., `event_id`, `event_type`, `source_service`, `timestamp`, `correlation_id`, `schema_version`, `data_classification`).
* **Avoid Sensitive Data:** Do not include highly sensitive data (raw passwords, PII unless strictly necessary and secured) directly in event payloads if possible. Consider referencing sensitive data or using tokenization if needed by consumers.

## Broker / Router / Bus

* **Choose Appropriately:** Select an event broker/router (e.g., Kafka, RabbitMQ, Pulsar, AWS EventBridge, Google Pub/Sub, Azure Event Grid/Service Bus) based on requirements like throughput, latency, ordering guarantees, persistence, delivery semantics, and operational complexity.
* **Configuration:** Configure the broker appropriately for durability, availability, and retention based on the criticality of the event streams.
* **Topics/Channels:** Use logical topic/channel names following established conventions (see Topic Naming in Kafka rules if applicable).

## Producers

* **Atomicity:** Ensure that publishing an event is atomic with the state change that triggered it. Use patterns like Transactional Outbox if strong guarantees are needed, especially when dealing with database updates.
* **Delivery Guarantees:** Understand the delivery guarantees offered by the producer client and broker (at-most-once, at-least-once, exactly-once - often requires specific producer/consumer/broker configuration). Configure appropriately based on requirements.
* **Error Handling:** Implement retry logic (with backoff) for transient broker connection/publishing errors. Consider store-and-forward mechanisms or alerting for persistent publishing failures.

## Consumers

* **Idempotency:** Consumers MUST be idempotent. Design message processing logic such that processing the same event multiple times produces the same end result (or at least has no unintended side effects). Common techniques include:
    * Tracking processed event IDs in a persistent store.
    * Using database constraints (e.g., unique keys) or conditional updates.
    * Designing operations to be naturally idempotent (e.g., setting a value rather than incrementing it based on an event).
* **Error Handling:**
    * Differentiate between transient errors (e.g., temporary network issue, downstream service unavailable) and permanent errors (e.g., malformed message, invalid business logic).
    * Implement retry logic (with exponential backoff and jitter) for transient errors.
    * For permanent errors or after exhausting retries, move the problematic message to a Dead-Letter Queue (DLQ) for later inspection and manual intervention. Do not block processing indefinitely.
* **Concurrency:** Process events concurrently where possible to improve throughput, but ensure ordering requirements (if any) are maintained, often by processing partitions sequentially within a consumer group (as in Kafka).
* **State Management:** Manage consumer state (e.g., last processed offset in Kafka) reliably. Prefer broker-managed or atomic commits where available.

## Observability

* **Distributed Tracing:** Propagate correlation IDs (Trace IDs, Span IDs) through events to enable distributed tracing across asynchronous service boundaries. Include IDs in event metadata.
* **Monitoring:** Monitor key metrics for the event broker (throughput, latency, error rates, queue depths) and for consumers (processing lag, error rates, throughput).
* **Logging:** Log key information during event production and consumption, including correlation IDs and event IDs, to aid debugging. Centralize logs.
* **Alerting:** Set up alerts for critical conditions like high consumer lag, high error rates, DLQ accumulation, or broker unavailability.

## Choreography vs. Orchestration

* **Choreography (Preferred for EDA):** Services react independently to events published by other services. There is no central controller dictating the workflow. Promotes high decoupling and autonomy. Can be harder to visualize and debug end-to-end flows.
* **Orchestration:** A central service (orchestrator) explicitly coordinates the interaction between services, often by sending commands and waiting for replies. Easier to manage complex workflows and handle errors centrally, but introduces coupling to the orchestrator, which can become a bottleneck or single point of failure.
* **Choice:** Prefer choreography for standard event-driven flows to maximize decoupling. Consider orchestration only for complex, long-running business processes that require explicit state management and centralized control (e.g., using Saga orchestrators).
