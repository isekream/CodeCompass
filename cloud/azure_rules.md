# Azure Rules

## General Principles & Organization

* **Management Groups, Subscriptions, Resource Groups:** Utilize the Azure hierarchy effectively.
    * Use Management Groups to apply governance controls (Policy, RBAC) across multiple subscriptions. Align top-level groups with segmentation strategy (e.g., environments, business units). Keep hierarchy depth minimal (e.g., <= 3 levels).
    * Use Subscriptions as a unit of management, billing, and scale. Consider separate subscriptions for different environments (dev, staging, prod), major applications, or business units.
    * Use Resource Groups to group related resources for an application or deployment lifecycle. Resources can only exist in one resource group.
* **Azure Policy:** Use Azure Policy extensively to enforce organizational standards, compliance requirements, security configurations, and tagging rules across subscriptions and resource groups. Use built-in policies and create custom policies as needed. Prioritize `audit` effects before using `deny` or `deployIfNotExists`.
* **Infrastructure as Code (IaC):** Manage Azure resources using IaC tools like Azure Resource Manager (ARM) templates, Bicep, or Terraform for consistency, repeatability, and version control.
* **Naming Conventions:** Establish and enforce clear and consistent naming conventions for all Azure resources. Include resource type, environment, application name, region, etc., where appropriate.
* **Tagging:** Implement a consistent and comprehensive tagging strategy (see Tagging section below).
* **Documentation:** Document your Azure architecture, network designs, IAM configurations, policies, and operational procedures.

## Identity and Access Management (Microsoft Entra ID / Azure AD)

* **Centralize Identity Management:** Use Microsoft Entra ID as the central identity provider. Synchronize on-premises Active Directory using Microsoft Entra Connect if in a hybrid environment, but prefer cloud-native accounts for privileged roles.
* **Principle of Least Privilege:** Assign roles using Azure Role-Based Access Control (RBAC) based on the minimum permissions needed.
    * Avoid assigning broad built-in roles like Owner or Contributor at high scopes (Subscription, Management Group) unless absolutely necessary.
    * Use more granular built-in roles or create custom roles specific to job functions. Assign roles at the narrowest scope possible (Resource Group or individual resource).
* **Privileged Identity Management (PIM):** Use PIM for managing, controlling, and monitoring access to important resources.
    * Require Just-In-Time (JIT) access for privileged roles instead of permanent assignments.
    * Configure approval workflows for activating privileged roles.
    * Enable MFA for role activation.
    * Conduct regular access reviews for privileged roles.
* **Emergency Access Accounts:** Maintain 2-5 highly secured "break-glass" accounts with the Global Administrator role, used only for emergencies. These should be cloud-only accounts with strong MFA and complex passwords, stored securely offline.
* **Multi-Factor Authentication (MFA):** Enforce MFA for ALL users, especially administrators and anyone accessing sensitive resources. Use Conditional Access policies to control MFA requirements based on user, location, device, application, and risk.
* **Conditional Access Policies:** Use Conditional Access policies to enforce security controls at sign-in based on various conditions (e.g., require MFA, block legacy authentication, require compliant devices, restrict locations).
* **Identity Protection:** Utilize Microsoft Entra ID Protection features (requires P1/P2 license) to detect and respond to identity-based risks (e.g., risky sign-ins, users at risk). Configure risk-based Conditional Access policies.
* **Groups:** Assign access to Azure resources and applications via Entra ID groups rather than individual users. Use dynamic groups where appropriate. Avoid nesting groups excessively, especially for role assignments via PIM.
* **Service Principals & Managed Identities:**
    * Use Managed Identities (System-assigned or User-assigned) for Azure resources needing to authenticate to other Azure services (e.g., VM accessing Key Vault). Avoid storing credentials in code or configuration.
    * If Service Principals with secrets or certificates are necessary, implement secure credential management and regular rotation using tools like Azure Key Vault. Limit their permissions using least privilege.
* **Regular Audits & Reviews:** Regularly review Entra ID sign-in logs, audit logs, and user access assignments (using Access Reviews). Remove unused accounts and unnecessary permissions promptly. Monitor for suspicious activity.

## Security

* **Microsoft Defender for Cloud:** Enable and utilize Microsoft Defender for Cloud across subscriptions.
    * Review the Secure Score and implement recommendations to improve security posture.
    * Enable enhanced security features (Defender plans) for relevant resource types (Servers, Storage, SQL, Containers, Key Vault, etc.) for advanced threat detection and protection.
    * Monitor security alerts and incidents, and establish remediation processes.
* **Network Security (VNet):**
    * Segment networks using Virtual Networks (VNets) and subnets.
    * Use Network Security Groups (NSGs) to filter traffic between Azure resources within a VNet and with external networks. Apply least privilege rules (default deny). Use Application Security Groups (ASGs) to simplify NSG rule management for groups of VMs.
    * Use Azure Firewall (managed, stateful firewall-as-a-service) for centralized network traffic control and threat protection across VNets, especially for hub-spoke architectures or filtering outbound traffic.
    * Use Azure Private Link / Private Endpoints to access Azure PaaS services (Storage, SQL DB, Key Vault, etc.) securely over the private VNet without exposing them to the public internet.
    * Protect against DDoS attacks using Azure DDoS Protection (Standard tier recommended for critical applications).
    * Minimize public IP address exposure. Use NAT Gateway for controlled outbound internet access from private subnets.
* **Data Encryption:**
    * Data is encrypted at rest by default using platform-managed keys. Use Customer-Managed Keys (CMK) via Azure Key Vault for greater control over encryption keys for services like Azure Storage, Azure Disk Encryption, Azure SQL DB, etc.
    * Enforce encryption in transit using TLS 1.2+ for all communications. Configure services (e.g., App Service, API Management) to require HTTPS.
* **Azure Key Vault:**
    * Store secrets (API keys, passwords, connection strings), encryption keys (CMK), and certificates securely in Azure Key Vault.
    * Control access to Key Vault secrets/keys/certificates using RBAC (recommended) or Access Policies, applying least privilege.
    * Enable logging and monitoring for Key Vault access. Rotate secrets and keys regularly.
* **Logging and Monitoring:**
    * Collect Azure Activity Logs (control plane actions) and Diagnostic Logs (data plane/resource logs) for all relevant services. Send logs to Azure Monitor Logs (Log Analytics workspace) for analysis and alerting, or to Azure Storage for long-term archival, or Azure Event Hubs for streaming.
    * Use Azure Monitor to create alerts based on logs and metrics for security events, performance issues, or availability problems.
    * Use Microsoft Sentinel (cloud-native SIEM/SOAR) for advanced threat detection, investigation, and response by correlating logs and alerts from various sources (Azure, M365, on-premises).
* **Patch Management:** Keep Azure VMs and application dependencies patched using Azure Update Management or other solutions.
* **Web Application Security:** Protect web applications using Azure Application Gateway with Web Application Firewall (WAF) enabled to protect against common web vulnerabilities (OWASP Top 10).

## Cost Optimization

* **Visibility & Governance:** Use Azure Cost Management + Billing for visibility into spending patterns. Analyze costs by subscription, resource group, tag, resource type, etc. Use cost analysis, set Budgets with alert thresholds, and review Azure Advisor cost recommendations.
* **Right-Sizing:** Continuously monitor resource utilization (VM CPU/Memory, disk IOPS/throughput, App Service plans) and resize resources to match actual needs. Use Azure Advisor recommendations.
* **Pricing Models:**
    * Use Azure Reservations (1 or 3-year commitment) for predictable workloads (VMs, SQL DB, App Service, etc.) to get significant discounts compared to Pay-As-You-Go.
    * Use Azure Savings Plans for compute (1 or 3-year commitment based on hourly spend) for more flexibility across compute services and regions.
    * Use Spot Virtual Machines for interruptible, fault-tolerant workloads for deep discounts.
    * Leverage Azure Hybrid Benefit if you have existing on-premises Windows Server or SQL Server licenses with Software Assurance.
* **Shut Down Unused Resources:** Identify and delete or deallocate unused resources (VMs stopped vs. deallocated, unattached disks, old snapshots, idle App Service plans, empty resource groups). Automate shutdown/startup of non-production resources outside business hours.
* **Autoscaling:** Configure autoscaling for services like Virtual Machine Scale Sets (VMSS), App Service Plans, AKS clusters to dynamically adjust capacity based on load, paying only for what's needed.
* **Storage Optimization:**
    * Use appropriate Azure Blob Storage access tiers (Hot, Cool, Cold, Archive) based on access frequency. Use Lifecycle Management policies to automate tiering or deletion.
    * Choose the right managed disk type and size (Standard HDD, Standard SSD, Premium SSD, Ultra Disk) based on performance needs. Delete unattached managed disks.
* **Networking Costs:** Be mindful of data transfer costs, especially egress traffic out of Azure regions or across regions. Use Azure CDN, optimize traffic routing, and use services like Private Link where appropriate.
* **Serverless & PaaS:** Evaluate Azure Functions, Logic Apps, Container Apps, and other PaaS services which can be cost-effective by abstracting infrastructure management and often having consumption-based pricing.
* **Regular Review:** Continuously review costs using Azure Cost Management and Advisor. Adopt new cost-saving features and optimize architectures over time.

## Tagging Strategy

* **Standardization:** Define and enforce a standardized tagging strategy. Tag names are case-insensitive, but values are case-sensitive. Consider a consistent casing convention (e.g., `camelCase` or `PascalCase` for names, specific values).
* **Consistency:** Apply tags consistently across resources, resource groups, and subscriptions using Azure Policy (`append` or `modify` effects) or IaC.
* **Mandatory Tags:** Identify mandatory tags required for all or specific types of resources (e.g., `environment`, `owner` / `team`, `applicationName`, `costCenter`, `dataClassification`). Use Azure Policy (`deny` effect) to enforce mandatory tags if needed.
* **Tag Purposes:** Utilize tags for:
    * **Cost Management:** Grouping and filtering costs in Azure Cost Management.
    * **Resource Organization:** Filtering and managing resources in the Azure portal, CLI, PowerShell, or scripts.
    * **Automation:** Targeting resources for automated operations (e.g., start/stop VMs, patching, backups).
    * **Security/Policy:** Applying Azure Policies based on tags (e.g., restricting certain configurations for resources tagged `environment=prod`).
    * **Operations:** Identifying workload, service level, etc.
* **Avoid PII:** Do NOT put Personally Identifiable Information (PII) or other sensitive data in tag names or values. Tags are stored as plain text and can be exposed in various ways (billing reports, logs, etc.).
* **Limits**: A resource or resource group can have a maximum of 50 tags. Tag names are limited to 512 characters, and tag values to 256 characters. (Storage accounts have stricter limits).
* **Inheritance:** Resources do NOT automatically inherit tags from their resource group or subscription. Use Azure Policy or automation to apply tags consistently down the hierarchy if needed.
* **Documentation & Governance:** Document the tagging strategy and enforce it using Azure Policy. Regularly audit tag usage for compliance and consistency.

## Infrastructure as Code (IaC)

* **Use IaC:** Manage Azure infrastructure using ARM templates, Bicep (recommended), or Terraform.
* **Version Control:** Store IaC code in version control (e.g., Git).
* **Review Changes:** Implement code review processes for infrastructure changes.
* **Modularity:** Use modules (Bicep/Terraform) or linked/nested templates (ARM) for reusability.
* **Secrets Management:** Do not hardcode secrets. Use references to secrets stored in Azure Key Vault.
* **State Management (Terraform):** Use Azure Blob Storage for remote Terraform state files with state locking.
* **Testing & Validation:** Use pre-deployment validation tools (e.g., `az deployment validate`, `terraform validate`, ARM template test toolkit) and linting tools.

## Service-Specific Guidelines

### Compute

* **Virtual Machines:**
  * Use VM Scale Sets for horizontal scaling rather than individual VMs.
  * Choose appropriate VM series and sizes based on workload requirements.
  * Implement Availability Sets or Zones for high availability.
  * Use managed disks with appropriate redundancy (LRS, ZRS).
  * Implement backup and disaster recovery strategies.
  * Enable diagnostic settings for monitoring and troubleshooting.

* **App Service:**
  * Choose appropriate App Service plans and tiers for your workloads.
  * Enable deployment slots for zero-downtime deployments.
  * Configure auto-scaling rules based on metrics.
  * Enable logging and diagnostics.
  * Implement health checks and configure appropriate timeouts.
  * Use Application Insights for monitoring and troubleshooting.

* **Azure Kubernetes Service (AKS):**
  * Use managed identity integration for cluster authentication.
  * Enable cluster autoscaling and node pool configuration.
  * Implement proper network policies and pod security policies.
  * Use Azure CNI for advanced networking features.
  * Enable Azure Monitor for containers for monitoring and logging.
  * Consider using Virtual Nodes for burst scaling.

* **Functions & Logic Apps:**
  * Design for idempotency and statelessness.
  * Choose appropriate hosting plans (Consumption vs. Premium).
  * Implement proper error handling and retry policies.
  * Use Application Insights for monitoring and logging.
  * Set appropriate timeouts and concurrency limits.
  * Follow security best practices for HTTP triggers.

### Storage

* **Blob Storage:**
  * Choose appropriate access tiers based on access patterns.
  * Implement lifecycle management policies.
  * Use appropriate redundancy options (LRS, ZRS, GRS, RA-GRS).
  * Implement secure access using SAS tokens or Managed Identities.
  * Enable soft delete and versioning for data protection.
  * Configure firewall and network rules to restrict access.

* **Managed Disks:**
  * Choose appropriate disk types based on performance needs.
  * Implement appropriate backup strategies.
  * Use encryption at rest with platform or customer-managed keys.
  * Size disks appropriately for performance and cost.
  * Delete unattached disks to avoid unnecessary costs.

* **Files & NetApp Files:**
  * Choose the appropriate tier based on performance requirements.
  * Implement proper access controls using AD integration where needed.
  * Configure appropriate monitoring and alerts.
  * Implement backup strategies appropriate for file data.

### Databases

* **Azure SQL Database:**
  * Choose appropriate service tiers (Basic, Standard, Premium, Hyperscale).
  * Implement geo-replication for disaster recovery.
  * Configure automatic tuning and query performance insights.
  * Enable Advanced Threat Protection for security.
  * Use Dynamic Data Masking and Always Encrypted for sensitive data.
  * Implement proper backup and point-in-time restore strategies.

* **Cosmos DB:**
  * Choose appropriate consistency levels based on application needs.
  * Design effective partition keys for scalability.
  * Implement proper indexing policies for query performance.
  * Monitor request units (RU) consumption and optimize queries.
  * Configure multi-region writes where appropriate.
  * Use TTL for automatic document expiration.

* **MySQL/PostgreSQL/MariaDB:**
  * Configure high availability and read replicas appropriately.
  * Implement proper firewall rules and VNet integration.
  * Enable performance recommendations and query insights.
  * Configure appropriate backup retention and geo-redundancy.
  * Implement proper monitoring and alerting.

### Networking

* **Virtual Network Design:**
  * Design address spaces carefully to avoid overlaps with on-premises networks.
  * Use subnet segmentation based on workload types and security requirements.
  * Implement hub-spoke architectures for centralized connectivity.
  * Use NSGs at both subnet and NIC levels with proper security rules.
  * Implement service endpoints and private endpoints for PaaS services.

* **Load Balancing:**
  * Choose appropriate load balancer types (Azure Load Balancer, App Gateway).
  * Implement proper health probes for backend services.
  * Configure WAF policies for web applications.
  * Implement TLS termination and certificate management.
  * Configure appropriate logging and diagnostics.

* **VPN and ExpressRoute:**
  * Implement redundant connections for high availability.
  * Configure appropriate bandwidth and routing policies.
  * Monitor connection health and throughput.
  * Implement proper security for hybrid connectivity.
  * Document all connection details and fail-over procedures.

### Security

* **Azure Firewall:**
  * Implement rule collections based on network, application, and FQDN filtering.
  * Configure appropriate logging and diagnostics.
  * Consider implementing Azure Firewall Manager for centralized policy.
  * Use threat intelligence-based filtering where appropriate.
  * Document firewall rules and their business justification.

* **Microsoft Defender for Cloud:**
  * Enable appropriate plans for different resource types.
  * Implement recommended security configurations.
  * Configure email notifications for critical alerts.
  * Use workflow automation for remediating common issues.
  * Regularly review security posture and improvements.

* **Key Vault & Certificates:**
  * Use separate key vaults for different environments or sensitivity levels.
  * Configure proper access policies or RBAC permissions.
  * Enable purge protection and soft delete.
  * Monitor and alert on unexpected access patterns.
  * Implement proper key and secret rotation policies.

### DevOps & Deployment

* **Azure DevOps/GitHub Actions:**
  * Use service connections with managed identities where possible.
  * Implement proper branch policies and pull request reviews.
  * Use deployment environments with approvals for production.
  * Implement IaC validation as part of CI/CD pipelines.
  * Store secrets in Azure Key Vault, not in pipeline variables.

* **Monitoring & Observability:**
  * Use Application Insights for application monitoring.
  * Configure Log Analytics workspaces for centralized logging.
  * Implement custom dashboards for key metrics.
  * Set up appropriate alerts for service health and performance.
  * Configure action groups for alert notifications.
  * Implement distributed tracing for microservices architectures.
