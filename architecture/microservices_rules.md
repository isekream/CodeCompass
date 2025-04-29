# Microservices Rules

## Design Principles & Service Boundaries

* **Single Responsibility Principle (SRP):** Each microservice should have a single, well-defined responsibility, focused on a specific business capability or domain. Keep services small and focused.
* **Domain-Driven Design (DDD):**
    * Identify core business domains and subdomains.
    * Define Bounded Contexts around these domains. Each bounded context is a candidate for a microservice (or a small set of closely related microservices).
    * Align service boundaries with bounded contexts to ensure high cohesion within services and loose coupling between them.
* **Loose Coupling:** Services should be loosely coupled. Changes in one service should have minimal impact on others. Communication should primarily happen through well-defined APIs or asynchronous events, not direct database sharing or tight code dependencies.
* **High Cohesion:** Functionality within a single microservice should be closely related and focused on its specific business capability.
* **Design for Failure:** Assume failures will happen (network issues, service unavailability). Implement resilience patterns (see Resilience section).
* **Independently Deployable:** Each microservice should be buildable, testable, and deployable independently of other services.

## Communication Patterns

* **Choose Appropriate Style:** Select communication styles based on the interaction needs:
    * **Synchronous (Request/Response):** Use for requests requiring an immediate response (e.g., user-facing queries). Common protocols: REST (over HTTP), gRPC. Be mindful of tight coupling and potential latency cascades.
    * **Asynchronous (Event-Driven/Messaging):** Use for decoupling services, improving resilience, handling long-running tasks, and enabling reactive systems. Common patterns:
        * **Events:** Services publish events when significant state changes occur. Other interested services subscribe to these events (e.g., using Kafka, RabbitMQ, cloud message brokers). Promotes loose coupling.
        * **Commands:** Send messages representing a command to be executed by another service (often via a message queue).
        * **Request/Reply (Async):** Send a request message and receive a reply message via separate channels/topics.
* **API Gateway:** Use an API Gateway pattern as the single entry point for external clients (web/mobile frontends, third parties). The gateway handles concerns like request routing, composition/aggregation, authentication, rate limiting, caching, and protocol translation.
* **Service Discovery:** Implement a mechanism for services to find each other dynamically (e.g., client-side discovery, server-side discovery using tools like Consul, Eureka, or platform features like Kubernetes DNS/Services).
* **Standardized APIs:** Define clear, consistent, and well-documented API contracts between services (e.g., using OpenAPI for REST, Protocol Buffers for gRPC).

## Data Management

* **Database per Service:** Each microservice should own and manage its own private database/datastore. Other services MUST NOT access this database directly; they must go through the owning service's API.
* **Polyglot Persistence:** Choose the database technology best suited for each microservice's specific needs (e.g., relational DB for transactional data, document DB for flexible schemas, key-value store for caching).
* **Data Consistency:** Managing data consistency across services is crucial:
    * **Eventual Consistency:** Accept that data across different services might not be instantly consistent. This is often acceptable and necessary for scalability and availability. Use asynchronous communication (events) to propagate state changes.
    * **Saga Pattern:** For transactions spanning multiple services, use the Saga pattern (via Choreography/Events or Orchestration) to manage a sequence of local transactions with corresponding compensating actions to handle failures. Avoid distributed transactions (2PC) as they introduce tight coupling and reduce availability.
    * **Event Sourcing:** Consider Event Sourcing (storing state as a sequence of events) for services requiring a strong audit trail or the ability to reconstruct past states.
* **CQRS (Command Query Responsibility Segregation) (Optional):** Consider separating read (Query) models/stores from write (Command) models/stores for services with complex query needs or vastly different read/write patterns. This often pairs well with event sourcing.

## Deployment & Infrastructure

* **Containerization:** Package each microservice as a container image (e.g., Docker).
* **Orchestration:** Use a container orchestrator (e.g., Kubernetes, Nomad) for deployment, scaling, service discovery, health checks, and managing container lifecycles.
* **CI/CD Pipelines:** Each microservice should have its own independent CI/CD pipeline, allowing teams to build, test, and deploy their service autonomously.
* **Infrastructure as Code (IaC):** Manage infrastructure components (orchestrator config, databases, message queues, gateways) using IaC tools (Terraform, Pulumi, etc.).

## Security

* **API Gateway Security:** Enforce authentication and potentially coarse-grained authorization at the API Gateway level.
* **Service-to-Service Authentication:** Secure communication between services using mechanisms like mTLS (mutual TLS) or token-based authentication (e.g., OAuth 2.0 client credentials flow, JWTs).
* **Authorization:** Implement authorization within each service based on the principle of least privilege. Pass user context/permissions (e.g., in JWT claims or headers) from the gateway or initial authentication point.
* **Secrets Management:** Use secure secret management solutions (HashiCorp Vault, cloud provider secrets managers) for API keys, database credentials, etc. Inject secrets into services at runtime; do not hardcode or store in version control.
* **Input Validation:** Each service must validate input received via its API, even if it comes from other internal services ("zero trust").
* **Rate Limiting & Throttling:** Implement rate limiting at the API Gateway and potentially at individual service level to prevent abuse and DoS attacks.
* **Dependency Scanning:** Regularly scan container images and application dependencies for known vulnerabilities.

## Observability (Logging, Metrics, Tracing)

* **Centralized Logging:** Aggregate logs from all microservices into a centralized logging system (e.g., ELK Stack, Splunk, Datadog Logs). Ensure logs are structured (e.g., JSON).
* **Correlation IDs:** Propagate a unique correlation ID (request ID, trace ID) across all service calls involved in handling a single external request. Include this ID in all logs and traces.
* **Distributed Tracing:** Implement distributed tracing (e.g., using OpenTelemetry with backends like Jaeger, Zipkin, Datadog APM) to visualize request flows across multiple services and identify latency bottlenecks.
* **Metrics:** Each service should expose key metrics (request rate, error rate, latency - RED metrics; resource usage - USE metrics). Aggregate metrics in a central monitoring system (e.g., Prometheus/Grafana, Datadog).
* **Health Checks:** Implement health check endpoints (`/health`, `/ready`, `/live`) in each service for monitoring and orchestration systems to use.

## Testing

* **Unit Tests:** Focus heavily on unit tests within each microservice to verify its internal logic in isolation, mocking external dependencies (other services, databases).
* **Integration Tests:** Test interactions between a service and its direct infrastructure dependencies (e.g., its database, message queue). Often run against test instances of these dependencies (e.g., test containers).
* **Component Tests:** Test a single microservice in isolation, potentially including its database, but mocking calls to other microservices.
* **Contract Tests:** Use Contract Testing (e.g., Pact, Spring Cloud Contract) to verify that interactions between services (API calls, messages) adhere to agreed-upon contracts, reducing the need for extensive end-to-end tests.
* **End-to-End (E2E) Tests:** Write a limited number of critical-path E2E tests to verify key user flows work across multiple services. Keep these minimal as they are often slow, brittle, and hard to maintain. Focus more on lower-level tests and contract tests.

## Resilience

* **Timeouts:** Configure appropriate timeouts for all network calls (service-to-service, database, external APIs).
* **Retries:** Implement retry logic (ideally with exponential backoff and jitter) for transient network failures when calling other services. Ensure retried operations are idempotent.
* **Circuit Breaker:** Use the Circuit Breaker pattern (e.g., via libraries like Resilience4j, Polly) to prevent repeated calls to a failing service, allowing it time to recover and preventing cascading failures.
* **Bulkheads:** Isolate resources (e.g., connection pools, thread pools) used for interacting with different downstream services to prevent failures in one interaction from impacting others.
* **Idempotency:** Design API endpoints (especially `POST`, `PUT`, `DELETE`) and message consumers to be idempotent where necessary, allowing requests/messages to be safely retried without unintended side effects.
