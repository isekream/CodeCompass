# Serverless Architecture Rules

## Function Design (FaaS)

* **Single Responsibility Principle (SRP):** Each function should perform one specific, well-defined task. Keep functions small and focused. Avoid creating monolithic functions that handle multiple unrelated operations.
* **Statelessness:** Functions should be stateless. Do not store persistent state within the function's execution environment between invocations. Any required state should be passed in via the event payload or retrieved from an external state store (database, cache).
* **Idempotency:** Design functions to be idempotent whenever possible, especially when triggered by event sources that might provide "at-least-once" delivery (e.g., SQS, EventBridge). Processing the same event multiple times should produce the same result or have no unintended side effects. Use techniques like tracking processed event IDs.
* **Clear Inputs/Outputs:** Define clear event structures/API contracts for function inputs and outputs. Use data validation on incoming event payloads.
* **Avoid Background Activities:** Do not initiate background tasks (e.g., launching threads, timers) that might continue running after the main function handler has returned a response. The execution environment might be frozen or terminated, leading to incomplete work or unexpected behavior. Use managed services (queues, step functions) for background processing.
* **Language Choice:** Choose a runtime language appropriate for the task, considering factors like cold start time, ecosystem, and team familiarity.

## State Management

* **Externalize State:** Store all persistent state outside the function in managed services like:
    * Databases (DynamoDB, Firestore, RDS Proxy, Azure SQL)
    * Key-Value Stores / Caches (Redis, Memcached, ElastiCache, Memorystore)
    * Object Storage (S3, Azure Blob Storage, GCS)
    * State Machines (AWS Step Functions, Azure Durable Functions/Logic Apps)
* **Session State:** For managing user sessions, use external stores (like DynamoDB with TTL, Redis) or token-based approaches (like JWTs stored client-side, potentially validated via an auth service). Avoid relying on in-memory session state within function instances.
* **Workflows:** For complex, multi-step processes requiring state coordination between functions, use dedicated orchestration services like AWS Step Functions or Azure Durable Functions.

## Performance & Cost Optimization

* **Cold Starts:** Be aware of cold starts (latency during the first invocation or after a period of inactivity). Strategies to mitigate:
    * Keep function deployment packages small (minimize dependencies).
    * Choose runtimes with faster startup (e.g., Go, Rust, Node.js, Python are often faster than Java/.NET on some platforms).
    * Initialize connections and heavy objects *outside* the main handler function (in the global scope) to reuse them across invocations within the same execution environment instance.
    * Enable provisioned/reserved concurrency (e.g., AWS Lambda Provisioned Concurrency, Azure Functions Premium Plan pre-warmed instances, Cloud Functions min instances) for latency-sensitive functions, balancing cost and performance.
    * Consider warm-up triggers for less frequently invoked functions if consistent low latency is critical.
* **Memory Allocation:** Configure function memory appropriately. More memory typically means more CPU allocation. Profile functions (e.g., using AWS Lambda Power Tuning) to find the optimal memory setting that balances cost and execution speed for your workload. Don't overprovision unnecessarily.
* **Timeouts:** Configure function timeouts reasonably. Set them long enough to complete the task under normal conditions plus some buffer, but short enough to prevent runaway functions from incurring excessive costs or holding resources. Default timeouts are often very long; adjust them down.
* **Concurrency Limits:** Understand and configure concurrency limits (e.g., reserved concurrency in Lambda) to manage downstream resource pressure (like databases) or control costs.
* **Dependencies:** Minimize the number and size of external dependencies included in the deployment package to reduce cold start times and potential security surface area. Use Lambda Layers or equivalent mechanisms for sharing common dependencies.
* **Connection Pooling:** Reuse database and network connections across invocations within the same execution environment instance (initialize outside the handler). Use connection pooling if supported by the runtime/SDK. For relational databases, consider using serverless-friendly proxies (like RDS Proxy).
* **Batching:** When processing events from stream/queue sources (SQS, Kinesis, Event Hubs), process messages in batches within a single function invocation for higher throughput and reduced cost.

## Security

* **Least Privilege (IAM):** Each function MUST have its own execution role (e.g., AWS IAM Role, GCP Service Account) with the minimum necessary permissions (principle of least privilege) to access other resources (databases, queues, APIs, etc.). Avoid overly broad permissions (e.g., `*:*`). Use tools like IAM Access Analyzer.
* **API Gateway Security:** If functions are triggered via HTTP, use an API Gateway (AWS API Gateway, Azure API Management, Google Cloud API Gateway/Endpoints) in front of them. Implement authentication (e.g., API Keys, JWT Authorizers, Cognito, Azure AD) and authorization at the gateway layer.
* **Input Validation:** Validate and sanitize all input/event data within the function to prevent injection attacks or unexpected behavior.
* **Secrets Management:** Store sensitive configuration (API keys, database credentials) securely using services like AWS Secrets Manager, Azure Key Vault, or Google Secret Manager. Access secrets dynamically at runtime via the function's execution role permissions. Do NOT hardcode secrets in code or environment variables accessible to function code if possible.
* **Dependencies:** Regularly scan function code and dependencies for vulnerabilities using tools like `npm audit`, `pip-audit`, or integrated cloud provider services. Keep dependencies updated.
* **Network Security:** Place functions within private networks (VPCs) if they need to access private resources. Configure security groups/firewall rules appropriately. Use VPC Endpoints/Private Endpoints to access other cloud services securely without traversing the public internet.
* **Function URLs (If Used):** If using direct function URLs (e.g., Lambda Function URLs, Cloud Functions HTTPS triggers with public access), ensure appropriate authentication (`AuthType: AWS_IAM` or custom authorizer) and authorization are configured directly on the function or URL, as the API Gateway layer is bypassed.

## Deployment & Infrastructure

* **Infrastructure as Code (IaC):** Define and deploy serverless resources (functions, event sources, databases, IAM roles) using IaC frameworks like AWS SAM (Serverless Application Model), Serverless Framework, AWS CDK, Terraform, or Pulumi. Store IaC templates in version control.
* **CI/CD:** Implement separate CI/CD pipelines for each service/function to enable independent deployments. Pipelines should include automated testing, security scanning, and deployment stages (dev, staging, prod).
* **Versioning & Aliases:** Use function versioning and aliases (e.g., Lambda Versions & Aliases) to manage deployments, enable gradual rollouts (Canary/Linear), and facilitate rollbacks.

## Observability (Logging, Metrics, Tracing)

* **Structured Logging:** Implement structured logging (e.g., JSON format) within functions. Include contextual information like request/event IDs and correlation IDs. Log key events and especially errors. Avoid logging sensitive data.
* **Centralized Logging:** Ensure function logs are shipped to a centralized logging platform (CloudWatch Logs, Azure Monitor Logs, Google Cloud Logging, ELK, Datadog).
* **Metrics:** Monitor standard platform metrics provided by the FaaS provider (invocations, errors, duration, concurrency, throttles). Add custom application-level metrics (e.g., business KPIs) using embedded metric formats or monitoring libraries.
* **Distributed Tracing:** Implement distributed tracing (e.g., using AWS X-Ray, Azure Application Insights, Google Cloud Trace, OpenTelemetry) to track requests across multiple functions and services, especially in event-driven or microservice architectures. Propagate trace context.
* **Alerting:** Set up alerts on key metrics (e.g., high error rates, high latency/duration, high throttle rates, low success rates) and critical log patterns.
