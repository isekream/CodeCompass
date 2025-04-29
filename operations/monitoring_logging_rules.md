# Monitoring & Logging Rules

## General Principles

* **Observability Pillars:** Implement monitoring and logging covering the three pillars of observability:
    * **Logs:** Detailed, timestamped records of discrete events.
    * **Metrics:** Aggregated, time-series numerical data representing system health and performance.
    * **Traces:** Records of the path of a request as it travels through distributed systems.
* **Actionable Insights:** Focus monitoring and logging efforts on generating actionable insights for troubleshooting, performance optimization, security analysis, and business intelligence. Avoid collecting data that isn't used.
* **Correlation:** Ensure logs, metrics, and traces can be correlated (e.g., using common request IDs, timestamps, and consistent metadata/tags) to provide a unified view of system behavior.
* **Automation:** Automate the collection, aggregation, analysis, and alerting processes as much as possible.
* **Scalability:** Choose monitoring and logging solutions that can scale with your application and infrastructure growth.

## Logging

* **Standardized Format:** Log messages should follow a standardized, machine-parseable format, preferably structured logging (e.g., JSON). Include common fields like timestamp, severity level, service name, request ID, user ID (if applicable), and the actual message.
* **Log Levels:** Use standard severity levels consistently (e.g., DEBUG, INFO, WARN, ERROR, FATAL/CRITICAL).
    * **DEBUG:** Fine-grained information for diagnosing specific problems. Usually disabled in production.
    * **INFO:** Routine information about normal operations (e.g., service startup, request handling).
    * **WARN:** Indicates potential issues or unusual situations that don't necessarily cause errors but should be noted.
    * **ERROR:** Indicates errors that prevented a specific operation from completing but the application can continue running.
    * **FATAL/CRITICAL:** Severe errors that likely cause the application to terminate.
* **Meaningful Messages:** Log messages should be clear, concise, and provide sufficient context to understand the event without needing to read the source code. Log key events like application startup/shutdown, handling of requests, significant state changes, and errors.
* **Contextual Information:** Include relevant contextual information in logs, such as request IDs, transaction IDs, user identifiers (where appropriate and secure), and relevant function/module names.
* **Avoid Sensitive Data:** NEVER log sensitive information like passwords, API keys, tokens, credit card numbers, or Personally Identifiable Information (PII) in plain text. Sanitize or mask data if necessary.
* **Centralization:** Ship logs from all applications, services, and infrastructure components to a centralized logging system (e.g., ELK Stack, Splunk, Datadog Logs, CloudWatch Logs, Google Cloud Logging).
* **Performance:** Logging should have minimal impact on application performance. Use asynchronous logging where possible. Avoid excessive logging at high verbosity levels (like DEBUG) in production unless actively troubleshooting.
* **Log Rotation & Retention:** Implement log rotation on hosts/containers to prevent disk exhaustion. Configure appropriate retention policies in the centralized logging system based on compliance and operational needs.
* **Security Event Logging:** Log security-relevant events such as successful/failed logins, authorization failures, significant configuration changes, and detected security anomalies.
* **Log Protection:** Protect log files and the centralized logging system from unauthorized access, modification, or deletion.

## Metrics

* **Key Metric Categories:** Monitor key categories of metrics:
    * **Resource Metrics:** CPU utilization, memory usage, disk I/O, network bandwidth (system-level).
    * **Application Performance Metrics (APM):** Request latency (average, percentiles like p95, p99), request rate/throughput, error rates.
    * **Business Metrics:** User signups, orders placed, revenue generated, etc. (where applicable).
    * **Dependency Metrics:** Latency and error rates for calls to external services (databases, APIs).
* **Standardization:** Use standardized metric naming conventions (e.g., `service.component.metric_name.unit`). Use consistent labels/tags for dimensions (e.g., `environment`, `region`, `service`, `host`).
* **Collection:** Use appropriate tools for metric collection (e.g., Prometheus exporters, StatsD, OpenTelemetry collectors, cloud provider agents).
* **Aggregation & Storage:** Store metrics in a time-series database (TSDB) optimized for numerical data (e.g., Prometheus, InfluxDB, Datadog Metrics, CloudWatch Metrics, Google Cloud Monitoring).
* **Granularity & Retention:** Choose appropriate metric collection intervals (granularity) and retention periods based on operational needs and cost considerations.

## Distributed Tracing

* **Implement Tracing:** Instrument applications (especially in microservices architectures) to generate and propagate trace context (Trace IDs, Span IDs) across service boundaries. Use libraries supporting standards like OpenTelemetry or OpenTracing/OpenCensus.
* **Trace Collection:** Send trace data to a distributed tracing backend (e.g., Jaeger, Zipkin, Datadog APM, AWS X-Ray, Google Cloud Trace).
* **Sampling:** Configure appropriate trace sampling strategies (head-based or tail-based) to manage volume and cost, especially in high-traffic systems. Ensure critical or erroneous requests are sampled.
* **Context Propagation:** Ensure trace context is propagated across process boundaries, including asynchronous communication (message queues).

## Alerting

* **Actionable Alerts:** Alerts should be actionable and indicate a real problem requiring intervention. Avoid noisy alerts that lead to alert fatigue.
* **Symptom-Based Alerting:** Primarily alert on user-facing symptoms (e.g., high error rates, increased latency for critical user journeys) rather than just underlying causes (e.g., high CPU). Alerting on causes can supplement symptom-based alerts.
* **Severity Levels:** Define clear severity levels for alerts (e.g., P1/Critical, P2/Warning, P3/Info) with corresponding response expectations.
* **Clear Notifications:** Alert notifications should clearly state what is wrong, the severity, the impacted service/system, and potentially link to relevant dashboards or runbooks.
* **Thresholds:** Set meaningful alert thresholds based on historical data, baseline performance, and Service Level Objectives (SLOs). Avoid static, arbitrary thresholds. Consider alerting on rates of change or deviations from normal behavior.
* **Silencing & Maintenance:** Provide mechanisms for temporarily silencing alerts during planned maintenance or known issues.
* **On-Call Rotation:** Establish clear on-call schedules and escalation policies for responding to critical alerts.
* **Security Alerts:** Set up specific alerts for critical security events detected in logs or security monitoring tools.

## Visualization & Dashboards

* **Centralized Dashboards:** Use dashboarding tools (e.g., Grafana, Kibana, Datadog, CloudWatch Dashboards, Google Cloud Monitoring Dashboards) to visualize key metrics, logs, and sometimes traces.
* **Role-Based Dashboards:** Create dashboards tailored to different audiences (e.g., operations team, developers, business stakeholders).
* **Key Performance Indicators (KPIs):** Highlight KPIs and Service Level Indicators (SLIs) clearly on dashboards. Track performance against Service Level Objectives (SLOs).
* **Correlation:** Design dashboards to facilitate correlation between metrics, logs, and traces for easier troubleshooting.
* **Time Ranges:** Allow easy selection and comparison of different time ranges.

## Service Level Objectives (SLOs)

* **Define SLOs:** Establish clear Service Level Objectives defining target reliability levels for critical user journeys (e.g., "99.9% of API requests should complete successfully within 500ms").
* **Select SLIs:** Choose appropriate Service Level Indicators (SLIs) to measure performance against SLOs (e.g., availability, latency, error rate, throughput).
* **Error Budgets:** Calculate and track error budgets (the amount of allowable downtime or errors based on SLOs) to help balance reliability and feature development.
* **SLO-Based Alerting:** Configure alerts based on burning error budget too quickly rather than just instantaneous threshold breaches.
* **Review and Adjust:** Regularly review SLOs and adjust as necessary based on business requirements and technical capabilities.

## Security Monitoring

* **SIEM Integration:** Consider integrating log data into a Security Information and Event Management (SIEM) system for security monitoring.
* **Audit Logs:** Maintain comprehensive audit logs of administrative actions, authentication events, and access to sensitive resources.
* **Anomaly Detection:** Implement anomaly detection for unusual patterns in user behavior, network traffic, or resource usage that may indicate security threats.
* **Compliance Monitoring:** Configure monitoring to support compliance requirements (e.g., PCI DSS, HIPAA, SOC2, GDPR) where applicable.
* **Vulnerability Monitoring:** Track newly discovered vulnerabilities in your technology stack and dependencies.

## Infrastructure & Platform Monitoring

* **Host Monitoring:** Monitor health and performance of all servers, VMs, or container hosts (CPU, memory, disk, network).
* **Network Monitoring:** Monitor network performance, throughput, packet loss, and latency between system components.
* **Container Monitoring:** For containerized applications, monitor container-specific metrics (container CPU/memory usage, restarts, replicas).
* **Database Monitoring:** Track database performance (query latency, connection counts, lock wait times, buffer usage, replication lag).
* **Message Queue Monitoring:** Monitor queue lengths, publish/consume rates, and message processing latency.
* **Cloud Service Monitoring:** Track usage, performance, and availability of cloud-provided services (object storage, managed databases, etc.).

## Implementation Best Practices

* **Consistent Implementation:** Use standardized libraries, frameworks, or agents across services for consistent observability implementation.
* **Automate Setup:** Automate the setup of monitoring and logging as part of infrastructure provisioning and application deployment.
* **Documentation:** Document monitoring and alerting setup, including explanation of key metrics, important dashboards, and alert response procedures.
* **Regular Review:** Regularly review and update monitoring configurations, alert thresholds, and dashboards based on system changes and operational experiences.
* **Post-Incident Learning:** After incidents, evaluate if existing monitoring and alerting were sufficient. Identify gaps and improve coverage.
* **Cost Management:** Monitor and manage the cost of your observability stack, especially for log storage and high-cardinality metrics.

## Tool Selection Considerations

* **Integration:** Choose tools that integrate well with your existing technology stack and across the three observability pillars (logs, metrics, traces).
* **Scalability:** Ensure solutions can handle your current and projected data volume without excessive cost or performance impact.
* **Retention:** Consider data retention needs for different types of observability data based on operational and compliance requirements.
* **Query Performance:** Evaluate the performance and capabilities of query languages and interfaces for your observability data.
* **Ease of Use:** Consider the learning curve and usability for the entire team who will interact with the monitoring and logging systems.
* **Open Standards:** Where possible, prefer tools compatible with open standards (OpenTelemetry, Prometheus exposition format) to avoid vendor lock-in.
