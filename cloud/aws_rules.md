# AWS Rules

## General Principles & Account Management

* **Multi-Account Strategy:** Use AWS Organizations to manage multiple AWS accounts. Set up separate accounts for different environments (dev, staging, prod), business units, or security domains (e.g., security tooling, logging). This provides better security isolation, billing separation, and resource limits.
* **Well-Architected Framework:** Design workloads following the principles of the AWS Well-Architected Framework (Operational Excellence, Security, Reliability, Performance Efficiency, Cost Optimization, Sustainability). Regularly review workloads against the framework using the AWS Well-Architected Tool.
* **Region Selection:** Choose AWS Regions strategically based on latency requirements, service availability, compliance needs, and cost.
* **Resource Tagging:** Implement a consistent and comprehensive tagging strategy across all resources (see Tagging section below).
* **Documentation:** Document your AWS architecture, configurations, security policies, and operational procedures.
* **Continuous Learning:** Keep teams updated on AWS services, features, and security best practices through training and documentation.

## Identity and Access Management (IAM)

* **Secure Root User:**
    * Do NOT use the root user for everyday tasks. Lock away root user credentials.
    * Enable Multi-Factor Authentication (MFA) on the root user.
    * Do NOT create or use root user access keys.
    * Configure alternate contacts for the account.
* **IAM Identity Center (Successor to AWS SSO):** Prefer using IAM Identity Center for managing human user access, especially in multi-account environments. Federate with your existing Identity Provider (IdP) (e.g., Azure AD, Okta) if possible.
* **Principle of Least Privilege:** Grant only the minimum permissions necessary for users, groups, roles, and resources to perform their intended tasks.
    * Start with minimal permissions and grant more as needed, rather than starting broad and removing permissions.
    * Use AWS Managed Policies where appropriate, but create Customer Managed Policies for fine-grained control specific to your use cases.
    * Use IAM Access Analyzer to review permissions and identify overly permissive access. Generate policies based on access activity where appropriate.
* **IAM Roles:** Use IAM roles for granting permissions to AWS services (e.g., EC2 instances needing S3 access) and for cross-account access or federation, instead of embedding long-term credentials (access keys).
* **Temporary Credentials:** Use temporary credentials (via roles/STS) whenever possible, instead of long-term access keys.
* **Access Keys:** Avoid using long-term access keys for IAM users. If absolutely necessary (e.g., specific service accounts):
    * Rotate access keys regularly.
    * Do not embed access keys directly in code; use secure methods like environment variables, secrets management services, or IAM roles.
    * Monitor access key usage and remove unused keys.
* **IAM Groups:** Organize IAM users into groups based on job function or required permissions. Attach policies to groups rather than individual users to simplify management.
* **MFA:** Enforce MFA for all human users, especially those with privileged access.
* **Password Policy:** Configure a strong password policy for IAM users (complexity requirements, rotation).
* **Permissions Boundaries:** Use permissions boundaries on IAM entities (users/roles) to set the maximum permissions an entity can have, even if their identity-based policy allows more.
* **Regular Audits:** Regularly review IAM users, groups, roles, policies, and credentials. Remove unused entities and credentials promptly. Use credential reports and last accessed information.
* **Avoid Inline Policies:** Prefer attaching managed policies (AWS-managed or customer-managed) over using inline policies, as managed policies are easier to manage, reuse, and track changes.

## Security

* **Shared Responsibility Model:** Understand the AWS Shared Responsibility Model – AWS secures the cloud infrastructure, while you are responsible for security *in* the cloud (data, configurations, access management).
* **Data Encryption:**
    * Encrypt sensitive data at rest using services like AWS Key Management Service (KMS) with S3 server-side encryption, EBS volume encryption, RDS encryption, etc.
    * Encrypt data in transit using TLS/SSL (HTTPS). Enforce TLS 1.2+.
* **Network Security (VPC):**
    * Use Virtual Private Clouds (VPCs) to isolate resources logically.
    * Use Security Groups (stateful firewall at the instance level) to control inbound and outbound traffic. Apply least privilege principles.
    * Use Network Access Control Lists (NACLs) (stateless firewall at the subnet level) for an additional layer of defense, if needed.
    * Deploy resources across multiple Availability Zones (AZs) for high availability.
    * Minimize public exposure; use private subnets for resources that don't need direct internet access. Use NAT Gateways or VPC Endpoints for outbound/service access.
    * Use VPC Endpoints (Interface and Gateway) to access AWS services privately without traversing the public internet.
* **Logging and Monitoring:**
    * Enable AWS CloudTrail in all regions to log API activity across your account(s). Store logs securely (e.g., in a dedicated S3 bucket with encryption and access controls).
    * Enable VPC Flow Logs to capture IP traffic information for VPC network interfaces.
    * Configure Amazon CloudWatch for monitoring resource performance, logs, and setting alarms on key metrics or events (e.g., unauthorized API calls, high CPU usage).
    * Centralize logs from multiple accounts/sources if possible (e.g., using a central S3 bucket or CloudWatch Logs).
    * Use Amazon GuardDuty for intelligent threat detection.
    * Use AWS Security Hub for a centralized view of security alerts and compliance status.
    * Use AWS Config to track resource configuration changes and assess compliance against rules.
* **Patch Management:** Keep operating systems and application software patched and up-to-date on EC2 instances and other compute resources you manage. Use AWS Systems Manager Patch Manager for automation.
* **Secrets Management:** Use AWS Secrets Manager or Systems Manager Parameter Store (SecureString) to store and manage secrets like database passwords, API keys, etc., instead of hardcoding them.
* **API Security:** Secure APIs built on AWS (e.g., using API Gateway) with proper authentication (e.g., IAM, Cognito, Lambda authorizers) and authorization. Implement throttling and usage plans.
* **Regular Audits:** Conduct regular security audits and vulnerability assessments.

## Cost Optimization

* **Visibility:** Use AWS Cost Explorer, AWS Budgets, and Cost Allocation Tags to understand and analyze your spending patterns. Set up budget alerts.
* **Right-Sizing:** Continuously monitor resource utilization (EC2, RDS, EBS, etc.) and resize instances/volumes to match performance needs without overprovisioning. Use tools like AWS Compute Optimizer.
* **Pricing Models:**
    * Use Savings Plans (Compute or EC2 Instance) or Reserved Instances (RIs) for predictable, long-term workloads to achieve significant discounts compared to On-Demand pricing.
    * Use Spot Instances for fault-tolerant, stateless workloads that can handle interruptions for potentially massive savings (up to 90%).
* **Elasticity:** Automate shutting down or scaling down resources when not needed (e.g., dev/test environments outside business hours). Use Auto Scaling groups to match capacity with demand dynamically.
* **Storage Optimization:**
    * Use appropriate S3 storage classes (Standard, Intelligent-Tiering, Standard-IA, One Zone-IA, Glacier Instant Retrieval, Glacier Flexible Retrieval, Glacier Deep Archive) based on access frequency and retrieval time needs. Implement S3 Lifecycle policies to automate transitions.
    * Delete unattached EBS volumes and old EBS snapshots.
    * Choose the right EBS volume type (gp3, io2, etc.) based on performance requirements.
* **Data Transfer Costs:** Be mindful of data transfer costs, especially outbound traffic to the internet or between regions. Use services like CloudFront (CDN) or VPC Endpoints where applicable.
* **Managed Services:** Evaluate using AWS managed services (e.g., RDS, Lambda, Fargate) which can often reduce operational overhead and sometimes cost compared to self-managed solutions on EC2.
* **Optimize Over Time:** Regularly review costs and implement new cost-saving features or services released by AWS. Decommission unused resources promptly.

## Tagging Strategy

* **Standardization:** Define and enforce a standardized, case-sensitive tagging strategy across the organization. Use lowercase with hyphens (`-`) as separators (e.g., `cost-center`, `app-name`). Consider using a prefix (e.g., `org:environment`).
* **Consistency:** Apply tags consistently across all resource types that support tagging.
* **Mandatory Tags:** Define a set of mandatory tags for all resources (e.g., `environment`, `owner`, `project`, `cost-center`).
* **Tag Purposes:** Use tags for multiple purposes:
    * **Cost Allocation:** Track costs by project, department, application, etc. Activate user-defined cost allocation tags in Billing settings.
    * **Automation:** Trigger automated actions (e.g., start/stop schedules, backups, patching) based on tags.
    * **Access Control:** Use tags in IAM policies for Attribute-Based Access Control (ABAC).
    * **Organization/Filtering:** Easily identify and group resources in the console or via API/CLI.
    * **Security:** Identify resources handling sensitive data (`data-classification=confidential`) or subject to specific compliance (`compliance=pci`).
* **Governance:**
    * Use AWS Organizations Tag Policies to enforce tagging standards.
    * Use AWS Config rules to detect resources missing required tags.
    * Use automated tools (Tag Editor, Resource Groups Tagging API, IaC) to manage tags programmatically.
* **Avoid PII:** Do NOT put Personally Identifiable Information (PII) or other sensitive data in tag keys or values.
* **Documentation:** Document your tagging standards and make them easily accessible.
* **Review:** Regularly review and update your tagging strategy and clean up inconsistent or unused tags.

## Infrastructure as Code (IaC)

* **Use IaC:** Manage AWS infrastructure using IaC tools like AWS CloudFormation, AWS CDK, or Terraform. This promotes consistency, repeatability, and version control.
* **Version Control:** Store IaC templates/code in a version control system (e.g., Git).
* **Review Changes:** Implement code reviews for infrastructure changes.
* **Modular Design:** Structure IaC code modularly for reusability.
* **Secrets Management:** Do not hardcode secrets in IaC templates; use dynamic references to secrets stored in AWS Secrets Manager or Parameter Store.
* **Drift Detection:** Regularly check for configuration drift between your IaC definition and the actual deployed resources (e.g., using CloudFormation Drift Detection).

## Service-Specific Guidelines

### Compute

* **EC2:**
    * Use Amazon Machine Images (AMIs) for instance standardization.
    * Leverage spot instances for non-critical workloads to reduce costs.
    * Use EC2 Auto Scaling to dynamically adjust capacity based on demand.
    * Prefer the latest generation instance types for better performance/cost ratio.
    * Use Session Manager over SSH when possible for secure instance management.

* **Containers:**
    * Use ECR for container image storage with vulnerability scanning enabled.
    * Consider ECS Fargate for serverless container management when appropriate.
    * Use Amazon EKS for managed Kubernetes when advanced orchestration is needed.
    * Implement proper resource limits and requests for containers.

* **Lambda:**
    * Keep functions focused on a single responsibility.
    * Minimize cold start impact by optimizing dependencies and code size.
    * Set appropriate memory allocations and timeout values.
    * Use environment variables for configuration, but use Secrets Manager for sensitive values.
    * Implement proper error handling and dead-letter queues.

### Storage

* **S3:**
    * Implement appropriate bucket policies and access controls.
    * Use versioning for critical buckets to protect against accidental deletion or overwrites.
    * Consider enabling S3 Object Lock for compliance requirements.
    * Implement lifecycle policies to transition objects between storage classes.
    * Enable server-side encryption by default.

* **EBS:**
    * Use appropriate volume types based on workload requirements.
    * Enable EBS encryption by default for all volumes.
    * Implement regular snapshot schedules for backup purposes.
    * Clean up unused volumes and old snapshots regularly.

* **EFS/FSx:**
    * Choose appropriate performance modes and throughput options.
    * Enable encryption at rest and in transit.
    * Implement proper access controls using IAM and file system policies.

### Database

* **RDS:**
    * Enable automated backups with appropriate retention periods.
    * Use Multi-AZ deployments for production databases.
    * Enable encryption at rest for all database instances.
    * Implement parameter groups tuned for your workload.
    * Set up enhanced monitoring and Performance Insights.
    * Consider read replicas for read-heavy workloads.

* **DynamoDB:**
    * Design your schema carefully considering access patterns.
    * Choose appropriate provisioned capacity or on-demand pricing.
    * Use DynamoDB Accelerator (DAX) for read-heavy workloads requiring microsecond response times.
    * Implement TTL for data that should expire automatically.
    * Consider using DynamoDB Streams for event-driven architectures.

### Networking

* **VPC Design:**
    * Plan your CIDR blocks carefully to avoid addressing conflicts.
    * Use separate subnets for different tiers (web, application, database).
    * Implement transit gateways for complex multi-VPC or hybrid architectures.
    * Use different route tables for public and private subnets.

* **Load Balancing:**
    * Choose the appropriate load balancer type (ALB, NLB, CLB) based on your needs.
    * Enable access logs for troubleshooting and security analysis.
    * Implement health checks tuned to your application's behavior.
    * Use target groups effectively to separate different backend services.

### Resilience and Disaster Recovery

* **Backup Strategy:**
    * Define RPO (Recovery Point Objective) and RTO (Recovery Time Objective) for each workload.
    * Implement automated backups with appropriate retention policies.
    * Regularly test recovery procedures to ensure they work as expected.
    * Consider cross-region backups for critical workloads.

* **High Availability:**
    * Design multi-AZ architectures for all production workloads.
    * Consider multi-region architectures for mission-critical applications.
    * Implement automated failover mechanisms where possible.
    * Design for graceful degradation during partial outages.

* **Chaos Engineering:**
    * Consider implementing controlled chaos testing to validate resilience.
    * Use AWS Fault Injection Simulator for structured chaos experiments.

## DevOps and CI/CD

* **CI/CD Pipelines:**
    * Implement CI/CD pipelines using services like AWS CodePipeline, CodeBuild, and CodeDeploy.
    * Include security scanning and testing in your CI/CD pipelines.
    * Implement infrastructure validation as part of deployment pipelines.
    * Use feature branch strategies with automated testing before merging to main branches.

* **Monitoring and Observability:**
    * Implement comprehensive monitoring using CloudWatch, X-Ray, and third-party tools if needed.
    * Set up dashboards for key operational metrics.
    * Implement automated alerts for anomalies and service disruptions.
    * Use distributed tracing for microservice architectures.

## Governance and Compliance

* **Compliance:**
    * Understand the compliance requirements applicable to your workloads (e.g., HIPAA, PCI DSS, GDPR).
    * Use AWS Artifact for access to compliance documentation.
    * Implement AWS Config Rules to check for compliance with your organization's policies.
    * Consider using AWS Control Tower for multi-account governance.

* **Audit:**
    * Implement regular security assessments and penetration testing (with AWS permission).
    * Use AWS Trusted Advisor for ongoing best practice recommendations.
    * Regularly review AWS security bulletins and update your configurations accordingly.
    * Document all changes to production environments with justifications.
