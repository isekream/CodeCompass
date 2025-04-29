# Performance Testing Rules

## Purpose & Goals

* **Goal:** Evaluate the responsiveness, stability, scalability, and resource utilization of an application or system under expected or stressed load conditions. Identify performance bottlenecks and ensure the system meets defined performance criteria.
* **Why:** Ensure a positive user experience (speed, reliability), determine system capacity, identify infrastructure needs, prevent production failures under load, validate Service Level Agreements (SLAs), and build confidence before release.
* **Scope:** Define the scope clearly: Which user journeys, APIs, system components, or infrastructure elements will be tested? What are the specific performance goals (e.g., response time under X users, throughput target)?

## Types of Performance Tests

Conduct different types of tests based on objectives:

* **Load Testing:** Simulate expected user load (e.g., normal day, peak hour) to verify the system performs acceptably under anticipated conditions. Baseline performance metrics are often established here.
* **Stress Testing:** Push the system beyond its expected limits to find the breaking point and identify bottlenecks. Determine maximum capacity and how the system fails (gracefully or catastrophically). Assess recovery time after stress.
* **Endurance Testing (Soak Testing):** Run a sustained load test (typically expected load) over an extended period (hours or days) to detect issues like memory leaks, resource exhaustion, or performance degradation over time.
* **Spike Testing:** Simulate sudden, drastic increases and decreases in load to evaluate the system's ability to handle and recover from rapid changes in traffic.
* **Volume Testing (Flood Testing):** Test the system's performance with large volumes of data in databases or file systems to identify data handling bottlenecks.
* **Scalability Testing:** Determine how effectively the system scales (up or out) as load increases. Measure performance at different load levels to understand scaling characteristics and identify limits.
* **Component/Isolation Testing:** Test the performance of individual components or services in isolation to identify bottlenecks specific to that unit before system-wide testing.

## Key Performance Metrics

Define and monitor relevant metrics during tests:

* **Response Time:** Time taken from request submission to response completion (Average, Median, 90th/95th/99th Percentiles). Percentiles are often more important than averages.
* **Throughput:** Number of requests or transactions processed per unit of time (e.g., Requests Per Second - RPS, Transactions Per Second - TPS).
* **Error Rate:** Percentage of requests resulting in errors (e.g., HTTP 5xx errors). Should ideally be very low (close to 0%).
* **Concurrency:** Number of concurrent users or requests the system is handling.
* **Resource Utilization (Server-Side):**
    * CPU Usage (%)
    * Memory Usage (Absolute, %)
    * Disk I/O (Read/Write operations/sec, queue length, latency)
    * Network I/O (Bytes sent/received per sec)
* **Latency:** Often refers to the time taken for the *first* byte of the response, distinct from total response time. Important for perceived performance.

## Methodology & Planning

* **Define Clear Objectives & Goals:** Before testing, define specific, measurable, achievable, relevant, and time-bound (SMART) performance goals (e.g., "Average API response time must be < 500ms with 1000 concurrent users", "System must handle 500 RPS with < 1% error rate"). Base goals on business requirements and user expectations.
* **Identify Critical Scenarios:** Focus testing efforts on key business transactions, critical user journeys, and high-traffic endpoints.
* **Establish Baselines:** Run initial tests to establish baseline performance metrics against which future tests can be compared.
* **Realistic Test Scenarios:** Design test scripts that simulate realistic user behavior and workflows, not just simple endpoint hits. Include think time where appropriate.
* **Test Environment:**
    * Use a dedicated test environment that mirrors the production environment as closely as possible (hardware specs, software versions, network configuration, data volume/shape). Avoid testing performance on shared, under-powered environments.
    * Ensure the test environment itself (load generators, monitoring tools) does not become the bottleneck.
* **Test Data:** Prepare sufficient and realistic test data. Consider data uniqueness requirements and data privacy (use anonymized production data if possible and permitted). Plan for data cleanup or reset between test runs if necessary.

## Tools

* **Selection:** Choose performance testing tools based on:
    * Protocol support (HTTP, WebSockets, gRPC, DB protocols, etc.)
    * Scripting capabilities (GUI-based, code-based like JS, Scala, Python)
    * Scalability (ability to generate required load, distributed testing support)
    * Monitoring and reporting capabilities
    * Integration with CI/CD tools
    * Cost (open-source vs. commercial)
    * Team skillset
* **Common Tools:**
    * **Open Source:** Apache JMeter, k6, Locust, Gatling
    * **Commercial/Cloud:** LoadRunner, NeoLoad, BlazeMeter, Azure Load Testing, cloud provider native tools.
    * **APM Tools:** Use Application Performance Monitoring (APM) tools (Datadog, Dynatrace, New Relic, Application Insights) *during* tests to correlate application behavior with load test metrics and pinpoint bottlenecks in code or infrastructure.

## Integration & Automation (CI/CD)

* **Shift Left:** Integrate performance testing early and often in the development lifecycle, not just before release. Include simpler performance checks (e.g., single user tests, component-level tests) in CI pipelines.
* **Automate Execution:** Automate the execution of performance test suites within the CI/CD pipeline (e.g., nightly builds, pre-production deployments).
* **Quality Gates:** Define performance criteria (based on goals/baselines) as quality gates in the pipeline. Fail the build automatically if performance regressions are detected beyond acceptable thresholds.
* **Test Environment Provisioning:** Automate the setup and teardown of the performance test environment as part of the pipeline if possible.

## Analysis & Reporting

* **Analyze Results Thoroughly:** Don't just look at averages. Analyze percentile response times (p90, p95, p99), error patterns, and resource utilization metrics throughout the test duration. Correlate client-side metrics with server-side resource usage and APM data.
* **Identify Bottlenecks:** Use analysis to pinpoint the root cause of performance issues (e.g., inefficient code, slow database queries, resource saturation, network latency, configuration limits).
* **Clear Reporting:** Generate clear, concise reports summarizing test objectives, methodology, key findings (including metrics vs. goals), bottlenecks identified, and actionable recommendations. Tailor reports for different audiences (technical teams vs. management).
* **Track Trends:** Store and compare results over time to track performance trends, identify regressions, and verify improvements.
* **Retest:** After implementing optimizations or fixes, re-run the relevant performance tests to validate the impact.
