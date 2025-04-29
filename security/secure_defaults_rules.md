# Secure Defaults Configuration Guidelines

## Core Principles

* **Secure by Default:** Systems, applications, libraries, and components MUST be configured securely out-of-the-box. The default state should require minimal security configuration changes for a production deployment. Avoid "insecure by default" configurations that require users to explicitly enable security features.
* **Least Privilege:** Default configurations should grant only the minimum necessary permissions and access rights required for the component's essential function. Avoid default administrative or overly broad permissions.
* **Least Functionality:** Default configurations should enable only essential features, ports, protocols, and services required for the component's primary purpose. Disable or do not install unnecessary features or services by default.
* **Fail Securely:** In case of errors or failures, components should default to a secure state (e.g., deny access, halt processing) rather than failing open or exposing sensitive information.
* **Simplicity:** Secure configurations should be simple to understand and manage. Avoid complex default settings that are prone to misconfiguration.

## Configuration Practices

* **No Default Credentials:** Never ship products or deploy systems with default, hardcoded, or easily guessable credentials (usernames, passwords, API keys, certificates). Require users to set strong, unique credentials upon initial setup or first use.
* **Strong Defaults:** Configure default settings to align with current security best practices:
    * **Authentication:** Require strong authentication mechanisms by default. If passwords are used, enforce complexity, history, and lockout policies by default. Enable MFA options where applicable, even if not forced on by default.
    * **Authorization:** Implement restrictive default access controls. Deny access by default and require explicit grants.
    * **Cryptography:** Use strong, current, and industry-accepted cryptographic algorithms and protocols by default (e.g., TLS 1.2+, strong cipher suites, secure password hashing like Argon2id or bcrypt). Disable weak or deprecated algorithms (e.g., SSLv3, TLS 1.0/1.1, DES, MD5).
    * **Session Management:** Configure secure session defaults (e.g., short timeouts, `HttpOnly`, `Secure`, `SameSite=Lax` or `Strict` flags for cookies).
    * **Logging & Auditing:** Enable sufficient logging and auditing by default to track security-relevant events (logins, failures, admin actions). Ensure logs do not contain sensitive data unless necessary and protected.
    * **Error Handling:** Configure error handling to avoid revealing sensitive internal details or stack traces to end-users in production defaults.
* **Minimize Attack Surface:**
    * Disable unused network ports and services.
    * Disable unnecessary features or modules.
    * Remove default/sample accounts, applications, or content before deployment.
* **Configuration Files:**
    * Provide clear documentation for all configuration settings, especially security-relevant ones.
    * Separate configuration from code.
    * Protect configuration files containing sensitive data with strict file permissions and encryption where appropriate.
* **Infrastructure as Code (IaC):** Define infrastructure configurations using IaC tools (Terraform, CloudFormation, etc.). Use secure default values within templates and modules. Utilize linters and static analysis tools (tfsec, checkov) to check IaC for insecure configurations.

## Frameworks & Libraries

* **Choose Secure Options:** Prefer frameworks and libraries that prioritize security and have secure defaults built-in.
* **Verify Defaults:** Do not blindly trust the defaults of third-party frameworks or libraries. Review their documentation and configuration settings to ensure they align with your security requirements. Disable or override insecure defaults if necessary.
* **Update Regularly:** Keep frameworks and libraries updated to incorporate security patches, which often include fixes for insecure default configurations or behaviors.

## Infrastructure

* **Hardened Images:** Use hardened operating system images (e.g., CIS Benchmarked images) as base configurations.
* **Network Security:** Implement default deny firewall rules. Only allow necessary ports and protocols between network segments and from external sources.
* **Cloud Provider Defaults:** Review and adjust default settings provided by cloud service providers (e.g., public access settings for storage buckets, default security group rules). Apply Secure Configuration Baselines (e.g., CIS Benchmarks, MCSB).

## Maintenance & Review

* **Regular Audits:** Periodically audit system configurations against defined secure baselines and best practices. Use automated configuration scanning tools where possible.
* **Documentation:** Document the chosen secure default settings and the rationale behind them. Document any necessary deviations from the defaults and the associated risk acceptance.
* **Training:** Ensure developers and operations personnel are aware of secure configuration principles and project-specific defaults.
