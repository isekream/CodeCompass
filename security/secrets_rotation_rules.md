# Secrets Rotation Rules

## Core Principles

* **Purpose:** Regularly changing secrets (passwords, API keys, certificates, tokens, database credentials) reduces the risk associated with compromised credentials. If a secret is leaked or stolen, rotation limits the time window an attacker can use it.
* **Automation:** Secrets rotation MUST be automated whenever possible. Manual rotation is error-prone, difficult to enforce consistently, and does not scale.
* **Scope:** This policy applies to all types of secrets used within applications, infrastructure, CI/CD pipelines, and by human operators accessing privileged systems.
* **Integration with Secrets Management:** Rotation policies should be implemented in conjunction with a centralized Secrets Management solution (e.g., HashiCorp Vault, AWS Secrets Manager, Azure Key Vault, GCP Secret Manager).

## Rotation Frequency

* **Risk-Based Approach:** Determine rotation frequency based on the sensitivity of the resource protected by the secret, the potential impact of compromise, compliance requirements, and the likelihood of exposure.
* **General Guidelines:**
    * **Highly Sensitive Credentials:** (e.g., database admin passwords, root API keys, signing keys) - Rotate frequently (e.g., every 30-90 days).
    * **Service-to-Service Credentials:** (e.g., API keys, service account keys) - Rotate regularly (e.g., every 60-180 days, or more frequently if feasible).
    * **User Passwords:** Follow current NIST guidelines (SP 800-63B) - Do *not* enforce periodic password expiration *unless* there is evidence of compromise. Focus on strong passwords, MFA, and breach detection instead.
    * **TLS Certificates:** Rotate well before expiration (e.g., at 80% of validity period or at least 30 days before expiry for longer-lived certs). Automate renewal using ACME protocol (Let's Encrypt) or internal PKI tools.
* **Compliance:** Adhere to specific rotation frequencies mandated by relevant compliance frameworks (e.g., PCI DSS, HIPAA) if applicable.
* **Event-Driven Rotation:** Immediately rotate any secret suspected of compromise or exposure (e.g., found in logs, code commits, public repositories). Also, rotate secrets upon personnel changes (e.g., when an employee with access leaves).

## Automation & Tooling

* **Secrets Management Tools:** Use dedicated secrets management tools that support automated rotation features. Configure these tools to handle the rotation lifecycle (generation, deployment, revocation).
* **Integration:** Integrate secret rotation tooling with target systems (databases, cloud providers, CAs) to automatically update credentials. Leverage built-in rotation capabilities (e.g., AWS Secrets Manager rotation Lambdas, Azure Key Vault rotation policies).
* **Application Integration:** Applications MUST fetch secrets dynamically from the secrets management system at startup or runtime. Avoid embedding secrets directly in application configuration files or environment variables that are not securely managed.
* **Zero Downtime Rotation:** Implement rotation mechanisms that support zero downtime:
    * **Dual Secrets:** Maintain two active versions of a secret during rotation. The application tries the newest version first; if it fails, it tries the previous version. The oldest version is revoked only after a safe transition period or confirmation that the new secret is in use by all consumers.
    * **Secrets Management Features:** Leverage features in tools like HashiCorp Vault (dynamic secrets) or AWS Secrets Manager that manage the creation and distribution of new credentials before revoking old ones.

## Specific Secret Types

* **Database Credentials:** Use secrets management tools to automatically rotate database user passwords. Grant database permissions based on least privilege. Consider using temporary, dynamically generated database credentials (e.g., via Vault Database Secrets Engine) where possible.
* **API Keys/Tokens:** Rotate API keys according to provider recommendations or internal policy (e.g., every 90 days). Use keys with the minimum necessary scope/permissions. Monitor usage for anomalies. Revoke keys immediately if compromised or no longer needed.
* **Certificates (TLS/SSL):** Automate certificate issuance and renewal using ACME clients (like Certbot for Let's Encrypt) or internal PKI automation. Monitor certificate expiration dates proactively.
* **Service Account Keys (Cloud):** Prefer using short-lived credentials obtained via instance metadata services or workload identity federation instead of long-lived static keys. If static keys are unavoidable, rotate them regularly (e.g., every 90 days) using automated tooling and enforce least privilege IAM roles.

## Process & Handling

* **Generation:** Generate new secrets using cryptographically secure random generators. Ensure sufficient complexity and length according to policy.
* **Distribution:** Securely distribute the new secret to the application or service *before* revoking the old one (unless using a mechanism that handles this automatically).
* **Validation:** After rotation, verify that applications and services are successfully using the new secret.
* **Revocation:** Securely and reliably revoke the old secret after confirming the new secret is active and the transition period (if any) has passed.
* **Failure Handling:** Implement robust error handling and alerting for the rotation process itself. Have a documented plan for manual intervention if automated rotation fails.

## Auditing & Compliance

* **Audit Trails:** Ensure the secrets management system and rotation process generate detailed audit logs for all rotation events (successes and failures), including who or what initiated the rotation and when.
* **Monitoring & Alerting:** Monitor the status of secret rotation jobs. Alert on rotation failures or secrets approaching their rotation deadline without successful rotation. Monitor secret access logs for anomalous behavior.
* **Policy Enforcement:** Use policy-as-code or configuration checks to verify that rotation policies are configured correctly for critical secrets.
* **Regular Review:** Periodically review rotation policies, frequencies, and procedures to ensure they remain effective and aligned with current risks and best practices.
