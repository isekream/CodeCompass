# Chaos Engineering Rules

## Core Principles

* **Purpose:** Chaos Engineering is the discipline of experimenting on a system in order to build confidence in the system's capability to withstand turbulent conditions in production. It's about proactively identifying weaknesses by intentionally injecting controlled failures.
* **Improve Resilience:** The ultimate goal is to uncover hidden dependencies, weaknesses in fallback mechanisms, monitoring/alerting gaps, and other issues *before* they cause user-impacting outages, thereby improving system resilience.
* **Hypothesis-Driven:** Experiments MUST be based on a specific hypothesis about how the system *should* behave under failure conditions, compared against a measurable steady state.
* **Real-World Events:** Experiments should simulate realistic failure modes (e.g., server/instance failure, dependency unavailability, network latency/loss, resource exhaustion).
* **Controlled Experiments:** Chaos experiments MUST be conducted in a controlled manner, with safety mechanisms in place to limit the potential impact (blast radius).

## Process & Methodology

1.  **Define Steady State:**
    * Identify key business and system metrics (e.g., requests per second, error rate, latency percentiles, order completion rate) that represent normal, healthy operation.
    * Establish a measurable baseline for this steady state *before* running experiments.
2.  **Formulate Hypothesis:**
    * Based on understanding the system, hypothesize what will happen when a specific failure condition is introduced (e.g., "If database node A fails, read traffic will shift to node B within 10 seconds with no increase in error rate, and p95 latency will increase by less than 50ms").
    * The hypothesis should be specific and measurable.
3.  **Design Experiment:**
    * Choose the smallest, simplest experiment that can test the hypothesis.
    * Identify the target system(s)/component(s).
    * Define the specific failure to inject (e.g., CPU spike, latency injection, instance termination).
    * Define the magnitude and duration of the injection.
    * Identify the metrics needed to validate the hypothesis (both system metrics and business KPIs).
4.  **Run Experiment:**
    * Communicate planned experiments to relevant teams.
    * Execute the experiment, starting with the smallest possible blast radius (see Safety section).
    * Continuously monitor steady-state metrics and system health during the experiment.
5.  **Analyze Results:**
    * Compare observed behavior and metrics against the hypothesis.
    * Did the system behave as expected? Did monitoring/alerting fire correctly? Was there unexpected impact?
    * Identify weaknesses, bottlenecks, or incorrect assumptions revealed by the experiment.
6.  **Learn & Improve:**
    * Document findings, including successes (resilience confirmed) and failures (vulnerabilities found).
    * Prioritize and fix identified weaknesses.
    * Refine the system, monitoring, alerting, or runbooks based on learnings.
    * Share learnings across teams.
    * Increase the experiment scope/magnitude or design new experiments based on findings.

## Experiment Design

* **Focus on Real-World Failures:** Prioritize experiments simulating common or high-impact failure modes relevant to your specific architecture and dependencies (e.g., loss of an availability zone, dependency API latency, database failover, resource exhaustion).
* **Common Experiment Types:**
    * **Resource Exhaustion:** CPU, Memory, Disk I/O, Network Bandwidth saturation.
    * **Network Issues:** Latency injection, packet loss, blackholing traffic (blocking specific endpoints/ports), DNS failures.
    * **State/Instance Failures:** Terminating VMs/containers, stopping critical processes, clock skew.
    * **Dependency Failures:** Simulating slow responses or errors from internal or external dependencies (databases, APIs).
* **Vary Conditions:** Run experiments under different load conditions (low vs. peak traffic) if feasible.

## Safety & Blast Radius

* **Start Small:** Always begin experiments with the smallest possible blast radius (e.g., one instance, a small subset of users/traffic, non-critical environment). Gradually increase the scope as confidence grows.
* **Run in Production (Carefully):** While the ultimate goal is to build confidence in production resilience, **extreme caution** is required.
    * Gain significant confidence through experiments in pre-production environments first.
    * When running in production, use a very limited blast radius initially.
    * Ensure robust monitoring and automated rollback/stop mechanisms are in place.
    * Have manual "stop" buttons readily available.
    * Communicate clearly with stakeholders and on-call teams before, during, and after production experiments.
* **Automated Stop Conditions:** Implement automated mechanisms to halt the experiment immediately if key steady-state metrics degrade beyond predefined acceptable thresholds (e.g., error rate exceeds X%, latency p99 exceeds Y ms).
* **Rollback Plan:** Always have a clear plan and mechanism to revert the injected failure and return the system to its normal state. Ensure control plane access is maintained.

## Tooling

* **Use Dedicated Tools:** Employ specialized Chaos Engineering tools (e.g., Gremlin, Steadybit, LitmusChaos, AWS Fault Injection Simulator, Azure Chaos Studio) to safely inject faults, manage experiments, and often integrate safety mechanisms. Avoid ad-hoc scripts for fault injection where possible.
* **Tool Integration:** Integrate chaos engineering tools with monitoring/observability platforms to correlate experiment execution with system behavior and metrics.

## Integration & Culture

* **Integrate into SDLC:** Embed Chaos Engineering practices into the development lifecycle, not just as an occasional operational exercise. Consider running automated chaos experiments in CI/CD pipelines against test/staging environments.
* **Cross-Functional Collaboration:** Chaos Engineering requires collaboration between Development, Operations (SRE), QA, and potentially Security teams. Define roles and responsibilities.
* **Game Days:** Conduct regular "Game Day" exercises where teams manually run through failure scenarios (simulated using chaos tools) to test incident response, runbooks, monitoring, and communication.
* **Blameless Culture:** Foster a blameless culture where failures revealed by chaos experiments are treated as opportunities for learning and improvement, not reasons for blame.
* **Training & Awareness:** Educate teams on Chaos Engineering principles, practices, and the specific tools being used.

## Metrics & Learning

* **Focus on Steady State:** Track key business and system metrics defining the steady state before, during, and after experiments.
* **Measure Resilience:** Quantify improvements in resilience over time (e.g., reduced MTTR for specific failure types, fewer incidents caused by previously tested failure modes, successful validation of failover mechanisms).
* **Document & Share:** Document experiment plans, hypotheses, results, and learnings clearly. Share these findings broadly to improve collective understanding and system robustness.
