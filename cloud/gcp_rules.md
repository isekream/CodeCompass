# GCP Rules

## General Principles & Organization

* **Resource Hierarchy:** Utilize the GCP resource hierarchy (Organization > Folders > Projects) effectively.
    * Use Folders to group projects by environment (dev, staging, prod), team, application, or business unit.
    * Use Projects as trust and resource boundaries. Isolate applications or environments into separate projects where appropriate.
* **Corporate Accounts:** Use fully managed corporate Google accounts (e.g., via Google Workspace or Cloud Identity) for managing GCP resources. Avoid using personal Gmail accounts.
* **Least Privilege:** Apply the principle of least privilege for all IAM roles and permissions.
* **Labels & Tags:** Implement consistent labeling and tagging strategies for resource organization, cost allocation, automation, and policy enforcement (see Labeling & Tagging section).
* **Infrastructure as Code (IaC):** Manage GCP resources using IaC tools like Terraform or Google Cloud Deployment Manager for consistency, repeatability, and version control.
* **Documentation:** Document your GCP architecture, network configurations, IAM policies, security configurations, and operational procedures.

## Identity and Access Management (IAM)

* **Avoid Primitive Roles:** Do not grant basic/primitive roles (`roles/owner`, `roles/editor`, `roles/viewer`) in production environments unless absolutely necessary. They provide broad permissions across many services.
* **Use Predefined & Custom Roles:** Grant the most limited predefined roles or create custom roles that meet specific needs. Use IAM Recommender to help identify appropriate roles or replace primitive roles.
* **Least Privilege:** Grant roles at the smallest necessary scope (e.g., resource level instead of project level, project level instead of folder level).
* **Groups:** Grant roles to Google Groups instead of individual users whenever possible. Manage group membership instead of updating numerous IAM policies.
* **Service Accounts:**
    * Use dedicated service accounts for specific applications or workloads. Do not use the default Compute Engine service account if possible, as it often has broad project editor permissions by default.
    * Grant minimal necessary permissions to service accounts.
    * Do NOT use user-managed service account keys if alternative methods (e.g., Workload Identity Federation, attaching service accounts to resources) are available. They are a significant security risk if not managed properly.
    * If keys are necessary, rotate them regularly (e.g., every 90 days) and manage them securely. Do not embed keys in code or check them into version control. Use Secret Manager.
    * Enforce separation of duties for roles related to service account management (e.g., Service Account User vs. Service Account Key Admin).
* **Multi-Factor Authentication (MFA):** Enforce MFA (including Security Keys for admins) for all user accounts, especially privileged ones.
* **Audit IAM:** Regularly audit IAM policies and Cloud Audit Logs (Admin Activity and Data Access logs) to track changes and access patterns. Remove unused permissions or accounts promptly.
* **Access Approval & Transparency:** Enable Access Approval (require explicit approval for Google support access) and Access Transparency (logs of Google support access) for sensitive environments.
* **Organization Policies:** Use Organization Policy Service constraints to enforce security postures across your organization/folders/projects (e.g., restrict public IPs, enforce uniform bucket-level access).

## Security

* **Shared Responsibility Model:** Understand the GCP Shared Responsibility Model.
* **Network Security (VPC):**
    * Use VPC networks to create isolated network environments.
    * Use Firewall Rules to control traffic between resources and networks. Apply least privilege principles (default deny, allow specific traffic). Use network tags or service accounts for targeting rules instead of IP addresses where possible.
    * Prefer private IP addresses for resources; minimize the use of external IPs. Use Cloud NAT for outbound internet access from private instances.
    * Use Private Google Access to allow resources with internal IPs to reach Google APIs and services.
    * Use VPC Network Peering or Shared VPC for controlled network connectivity between VPCs or projects.
    * Enable VPC Flow Logs for network traffic monitoring and analysis.
* **Data Encryption:**
    * Data is encrypted at rest and in transit by default within GCP.
    * Use Customer-Managed Encryption Keys (CMEK) via Cloud KMS for resources like GCS buckets, Compute Engine disks, Cloud SQL, etc., when you need control over the encryption keys.
    * Use Customer-Supplied Encryption Keys (CSEK) if you need to manage your keys entirely outside GCP (use with caution).
    * Enforce SSL/TLS for connections (e.g., require SSL for Cloud SQL instances).
* **Key Management (Cloud KMS):**
    * Use Cloud KMS to manage cryptographic keys (CMEK).
    * Apply separation of duties for KMS roles (e.g., viewer vs. encrypter/decrypter vs. admin).
    * Rotate encryption keys periodically (e.g., automatically every 90 days).
* **Logging & Monitoring:**
    * Enable Cloud Audit Logs (Admin Activity, Data Access, System Event) for visibility into GCP activity. Configure log sinks to export logs for long-term retention or analysis (e.g., to Cloud Storage, BigQuery, Pub/Sub).
    * Use Cloud Monitoring for metrics, dashboards, and alerting on resource performance and potential issues.
    * Use Security Command Center (Premium/Enterprise tier recommended) for centralized security posture management, threat detection (Event Threat Detection, Container Threat Detection), vulnerability scanning, and compliance reporting.
    * Configure log retention policies and use Bucket Lock if immutability is required.
* **Secrets Management:** Use Secret Manager to store and manage API keys, passwords, certificates, and other secrets securely. Access secrets via IAM permissions.
* **Vulnerability Management:** Use Security Command Center's Web Security Scanner and Container Threat Detection features. Keep VM instances and container images patched and up-to-date.
* **API Security:** Secure Cloud Endpoints or Apigee API Management instances with authentication, API keys, quotas, and potentially Cloud Armor for WAF/DDoS protection.
* **Compute Security:**
    * Enable Shielded VMs features (Secure Boot, vTPM, Integrity Monitoring) for verifiable instance integrity. Consider Confidential VMs for memory encryption.
    * Disable or restrict project-wide SSH keys. Use OS Login for finer-grained SSH access management integrated with IAM.
    * Disable connecting to serial ports unless strictly necessary.

## Cost Optimization

* **Visibility & Reporting:** Use Cloud Billing Reports, Cost Table, and Cost Breakdown to understand spending patterns. Analyze costs by project, label, service, etc. Export billing data to BigQuery for detailed analysis.
* **Budgeting & Alerts:** Set Cloud Billing Budgets with alert thresholds to proactively monitor and control spending.
* **Labels:** Apply labels consistently to resources for cost allocation tracking.
* **Rightsizing:** Use Recommender (e.g., Idle VM Recommender, VM Rightsizing Recommender) to identify and eliminate waste (idle resources, oversized VMs/disks).
* **Pricing Models:**
    * Leverage Committed Use Discounts (CUDs) (1 or 3 years) for predictable workloads (Compute Engine, Cloud SQL, GKE, etc.) for significant savings over on-demand pricing. Analyze CUD analysis reports to maximize commitment coverage. Use spend-based CUDs for flexibility across certain services.
    * Leverage Sustained Use Discounts (SUDs) which apply automatically for Compute Engine resources running for a significant portion of the month.
    * Use Spot VMs (formerly Preemptible VMs) for fault-tolerant, batch processing workloads for the largest discounts (up to 90%), understanding they can be preempted.
* **Scheduling:** Shut down non-production resources (e.g., development VMs) outside of business hours using automation (e.g., Cloud Scheduler, instance schedules).
* **Autoscaling:** Use autoscaling features (Compute Engine MIGs, GKE Cluster Autoscaler, Cloud Run) to automatically adjust resource capacity based on demand.
* **Storage Optimization:**
    * Choose the appropriate Cloud Storage class (Standard, Nearline, Coldline, Archive) based on access frequency and retention needs. Use Object Lifecycle Management to automatically transition data to cheaper tiers or delete it.
    * Delete unused persistent disks, snapshots, and other storage resources.
* **Networking Costs:** Optimize network egress costs by choosing appropriate Network Service Tiers (Premium vs. Standard), using Cloud CDN, and understanding data transfer pricing between regions or to the internet.
* **Serverless & Managed Services:** Evaluate Cloud Functions, Cloud Run, and other managed services which can be cost-effective by paying only for execution time/requests rather than idle VMs.
* **Regular Review:** Continuously monitor costs and review recommendations. Adopt new cost-saving features or services as they become available. Decommission unused projects and resources.

## Labeling & Tagging Strategy

* **Labels vs. Tags:** Understand the difference: Labels are key-value pairs for organization and cost/usage breakdown. Tags (Resource Manager Tags) are key-value pairs used for programmatic policy enforcement (e.g., firewall rules, conditional IAM).
* **Standardization (Labels):** Define a consistent, organization-wide labeling policy.
    * Use a standard case format (e.g., lowercase).
    * Keys should be descriptive (e.g., `environment`, `team`, `application`, `cost-center`, `data-classification`).
    * Values should be chosen from a predefined set where possible (e.g., `environment=prod | staging | dev`).
* **Consistency (Labels):** Apply labels consistently and programmatically (via IaC tools) where possible.
* **Purpose (Labels):** Use labels for cost allocation, resource organization/filtering, identifying ownership, and potentially triggering automation.
* **Avoid PII (Labels):** Do NOT store Personally Identifiable Information (PII) or sensitive data in labels.
* **Limit (Labels):** Each resource can have up to 64 labels. Don't create excessive unique labels.
* **Tags (Resource Manager):** Use tags for policy enforcement.
    * Requires specific IAM permissions (`tagAdmin`, `tagUser`, `tagViewer`).
    * Tags are created at the Organization or Folder level (Keys) and then values are defined. Bindings attach Tag Values to specific resources.
    * Tags support IAM conditions and Organization Policy constraints based on whether a resource has a specific tag.
    * Tags support inheritance down the resource hierarchy.
* **Governance:** Document the labeling/tagging strategy. Use Organization Policies to potentially enforce certain label requirements if needed (though Tag Policies are more robust for enforcement). Audit label/tag usage.

## Infrastructure as Code (IaC)

* **Use IaC:** Manage GCP infrastructure using IaC tools like Terraform (recommended) or Google Cloud Deployment Manager.
* **Version Control:** Store IaC code in version control (e.g., Git).
* **Review Changes:** Implement review processes for infrastructure changes (e.g., pull requests).
* **Modular Design:** Use modules (Terraform) or templates (Deployment Manager) for reusability.
* **Secrets Management:** Do not hardcode secrets. Use references to secrets stored in Secret Manager.
* **State Management (Terraform):** Use a remote backend (like a GCS bucket with locking) for Terraform state management, especially for teams.

## Service-Specific Guidelines

### Compute

* **Compute Engine:**
  * Choose appropriate machine types and customize if needed (CPU, memory).
  * Use managed instance groups for autoscaling and high availability.
  * Leverage committed use discounts for stable workloads.
  * Use preemptible/spot VMs for batch/fault-tolerant workloads.
  * Enable Shielded VM options for enhanced security.

* **Google Kubernetes Engine (GKE):**
  * Use GKE Enterprise for advanced security and multi-cluster management.
  * Enable Workload Identity for secure pod authentication to GCP services.
  * Use node auto-provisioning and cluster autoscaler for optimal resource usage.
  * Implement appropriate pod security policies and network policies.
  * Use private clusters where possible to limit public exposure.

* **Cloud Run:**
  * Design for statelessness and idempotency.
  * Set appropriate concurrency levels and memory limits.
  * Use container startup health checks for reliability.
  * Implement proper error handling and retries.
  * Consider Cloud Run for Anthos for hybrid deployments.

* **Cloud Functions:**
  * Keep functions focused on a single responsibility.
  * Set appropriate memory and timeout values.
  * Use environment variables for configuration.
  * Implement proper error handling and retry logic.
  * Consider cold start impacts on latency-sensitive applications.

### Storage

* **Cloud Storage:**
  * Choose the appropriate storage class based on access patterns.
  * Use uniform bucket-level access (recommended) over ACLs.
  * Implement Object Lifecycle Management for cost optimization.
  * Consider bucket lock for compliance requirements.
  * Set appropriate IAM permissions at bucket and object levels.

* **Persistent Disk/Cloud Filestore:**
  * Choose appropriate disk types (standard, balanced, SSD, extreme).
  * Use snapshots for backups with appropriate retention policies.
  * Resize disks as needed to avoid overprovisioning.
  * Use regional persistent disks for high-availability workloads if needed.

### Databases

* **Cloud SQL:**
  * Enable high availability for production instances.
  * Set up automated backups with appropriate retention.
  * Use private IP connectivity where possible.
  * Implement appropriate maintenance windows.
  * Configure read replicas for read-scaling and potential failover.

* **Spanner:**
  * Design schema for optimal performance with appropriate primary keys.
  * Size instance configuration based on workload requirements.
  * Use interleaved tables for parent-child relationships.
  * Monitor query performance and adjust indexes as needed.
  * Implement appropriate backup strategies.

* **Firestore/Datastore:**
  * Design entity groups and indexes carefully for query performance.
  * Avoid hotspotting by designing appropriate entity keys.
  * Use transactions appropriately for data consistency.
  * Be mindful of indexing costs for write-heavy workloads.
  * Consider Firestore's native offline support for mobile applications.

* **BigQuery:**
  * Design table partitioning and clustering for query performance and cost.
  * Set appropriate table expiration for temporary datasets.
  * Use authorized views for secure data sharing.
  * Optimize query performance by selecting only needed columns.
  * Consider materialized views for frequently accessed query results.

### Networking

* **VPC Design:**
  * Plan IP address ranges carefully to avoid overlaps.
  * Use Private Service Connect or VPC Service Controls for enhanced security.
  * Implement Shared VPC for multi-project resources that need to communicate.
  * Use Cloud Routers and VPN/Interconnect for hybrid connectivity.
  * Implement proper firewall rules with service accounts as targets.

* **Load Balancing:**
  * Choose appropriate load balancer type (global vs. regional, external vs. internal).
  * Configure health checks appropriate for your application.
  * Use Cloud CDN with external HTTP(S) load balancers for cacheable content.
  * Implement SSL policies with appropriate security levels.
  * Use load balancer logging for monitoring and troubleshooting.

* **Cloud Armor:**
  * Deploy Cloud Armor with application load balancers for WAF protection.
  * Implement appropriate security policies (e.g., IP allowlisting, geographic restrictions, attack protection).
  * Use preconfigured rules to protect against common vulnerabilities.
  * Monitor and adjust rule thresholds based on traffic patterns.
  * Consider adaptive protection for automated rule generation.

### Security & Identity

* **Cloud IAM:**
  * Implement custom roles for precise permission management.
  * Use service account impersonation instead of key files where possible.
  * Regularly audit permissions with IAM Recommender.
  * Implement conditional access where appropriate.
  * Use policy analyzer to understand effective permissions.

* **Secret Manager:**
  * Store all sensitive credentials in Secret Manager.
  * Implement appropriate rotation policies for secrets.
  * Use CMEK encryption for highly sensitive secrets.
  * Grant access to secrets at the secret-version level where appropriate.
  * Implement audit logging for secret access.

* **Security Command Center:**
  * Deploy Premium tier for comprehensive security monitoring.
  * Configure custom findings and notifications.
  * Implement automated remediation for certain finding types.
  * Regularly review vulnerability reports and security health analytics.
  * Integrate with your SIEM or ticketing system for findings management.

### Operations & Monitoring

* **Cloud Monitoring:**
  * Set up appropriate dashboards for service health and performance.
  * Configure alerts for key metrics with appropriate thresholds.
  * Use uptime checks for external service monitoring.
  * Implement custom metrics for application-specific monitoring.
  * Consider SLO monitoring for critical services.

* **Cloud Logging:**
  * Configure appropriate log retention and routing.
  * Use log-based metrics for monitoring specific events.
  * Implement structured logging in applications.
  * Set up log-based alerts for critical events.
  * Export logs to appropriate destinations (GCS, BigQuery, Pub/Sub) for analysis.

* **Cloud Trace and Profiler:**
  * Implement distributed tracing for microservices architectures.
  * Use Profiler in production for continuous CPU and heap profiling.
  * Set sampling rates appropriate for your traffic volume.
  * Analyze trace data for performance bottlenecks.
  * Integrate OpenTelemetry where appropriate.

### DevOps & Continuous Delivery

* **Cloud Build:**
  * Implement CI/CD pipelines with appropriate triggers.
  * Use Cloud Build's built-in Docker support for container builds.
  * Store build artifacts in Artifact Registry.
  * Implement appropriate IAM roles for build service accounts.
  * Consider using Cloud Build's integration with GitHub or GitLab.

* **Artifact Registry:**
  * Use for all container images, language packages, and other artifacts.
  * Implement vulnerability scanning for container images.
  * Set up appropriate IAM permissions for artifact access.
  * Consider regional or multi-regional repositories based on needs.
  * Implement appropriate retention policies for artifacts.

* **Cloud Deploy:**
  * Use for managed continuous delivery to GKE, Cloud Run, or Anthos.
  * Implement appropriate approval processes for production deployments.
  * Configure rollback capabilities for failed deployments.
  * Use canary or blue-green deployment strategies for risk mitigation.
  * Integrate with notification channels for deployment status.
