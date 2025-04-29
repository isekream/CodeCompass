# Security Rules

## General Security Principles

- Follow OWASP Top 10 security best practices and secure coding guidelines.
- Apply the principle of least privilege in all contexts.
- Implement defense in depth - multiple layers of security controls.
- Sanitize all input from external sources before use.
- Validate all data against predefined schemas or constraints.
- Never trust user input or data from external sources.

## Authentication & Authorization

- Implement strong authentication mechanisms appropriate for the threat model:
  - OAuth 2.0 + OpenID Connect for services requiring social login or third-party authentication
  - JWT with appropriate security measures for stateless authentication
  - Session-based authentication with secure session management for traditional web applications
- Store passwords using strong adaptive hashing algorithms (Argon2, bcrypt, PBKDF2) with appropriate work factors.
- Never store passwords in plain text or with reversible encryption.
- Implement proper authorization checks at every entry point.
- Use Role-Based Access Control (RBAC) or Attribute-Based Access Control (ABAC) for complex permission models.
- Implement proper account lockout policies for failed login attempts.
- Ensure that authentication credentials are transmitted over secure channels (HTTPS).

## Data Protection

- Encrypt sensitive data at rest and in transit.
- Use TLS 1.2+ for all communications.
- Implement proper key management for cryptographic operations.
- Apply the principle of data minimization - only collect and retain necessary data.
- Anonymize or pseudonymize personal data where possible.
- Implement proper data access controls and audit logging.

## Common Vulnerability Prevention

- XSS (Cross-Site Scripting):
  - Use context-appropriate output encoding.
  - Implement Content Security Policy (CSP).
  - Validate and sanitize user input.
  - Use safe, templating engines with automatic escaping.

- SQL Injection:
  - Use parameterized queries or prepared statements.
  - Use ORMs with proper parameter binding.
  - Validate and sanitize input before using in database queries.
  - Apply the principle of least privilege to database accounts.

- CSRF (Cross-Site Request Forgery):
  - Implement anti-CSRF tokens.
  - Validate the origin header.
  - Use SameSite cookie attributes.
  - Implement proper CORS policies.

- Injection Attacks (Command, LDAP, XML, etc.):
  - Avoid OS commands when possible.
  - Validate and sanitize input before use.
  - Use parameterized APIs and prepared statements.
  - Implement proper input validation and whitelisting.

## Security in DevOps

- Include security scanning in the CI/CD pipeline.
- Perform dependency vulnerability scanning regularly.
- Conduct code reviews with a security focus.
- Implement secure configuration management.
- Use secrets management solutions instead of hardcoding secrets.
- Keep all systems, libraries, and dependencies updated.
- Regularly perform security testing (SAST, DAST, penetration testing).

## Logging and Monitoring

- Implement proper security logging and monitoring.
- Log security-relevant events (authentication, authorization, etc.).
- Do not log sensitive information (passwords, tokens, PII).
- Protect log files from unauthorized access.
- Implement detection mechanisms for unusual or suspicious activities.
- Set up alerts for security-relevant events.

## API Security

- Implement proper API authentication.
- Apply rate limiting and throttling to prevent abuse.
- Validate all input data.
- Return appropriate error messages without exposing sensitive information.
- Implement proper CORS configuration.
- Use security headers to enhance protection.
