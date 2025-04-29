# CI/CD Rules

## General Principles

* **Automate Everything:** The core goal of CI/CD is automation. Automate build, testing, security scanning, artifact publishing, and deployment processes as much as possible.
* **Single Source of Truth:** Use version control (e.g., Git) as the single source of truth for application code, infrastructure code, and pipeline definitions.
* **Fast Feedback:** Pipelines should provide feedback to developers quickly. Optimize build and test stages for speed. Fail fast if issues are detected early.
* **Consistency:** Ensure pipelines produce consistent results regardless of where or when they are run. Use containerized build environments or consistent tooling.
* **Idempotency:** Deployment steps should be idempotent, meaning running them multiple times produces the same end state.

## Pipeline Design & Structure

* **Triggering:** Configure pipelines to trigger automatically on code commits/merges to relevant branches (e.g., main, develop, feature branches).
* **Stages:** Structure pipelines into logical stages (e.g., Build, Test, Security Scan, Package, Deploy Staging, Test Staging, Deploy Prod). Ensure stages run sequentially and gate progression based on success.
* **Parallelization:** Run independent tasks within a stage in parallel to speed up execution time (e.g., running unit tests and linting simultaneously).
* **Pipeline as Code:** Define pipeline configurations as code (e.g., `Jenkinsfile`, `gitlab-ci.yml`, `azure-pipelines.yml`, GitHub Actions workflows) and store them in version control alongside the application code.
* **Modularity & Reusability:** Use templates, shared libraries, or reusable actions/jobs to avoid duplication in pipeline definitions across different projects or microservices.

## Continuous Integration (CI)

* **Build Automation:** Automate the compilation/building process for the application.
* **Testing:**
    * Automate unit tests and integration tests within the CI pipeline. Tests must pass for the pipeline to proceed.
    * Measure and track test coverage. Aim for high coverage of critical code paths.
    * Consider faster, lower-level tests earlier in the pipeline and slower, integration/E2E tests later.
* **Static Analysis & Linting:** Integrate static code analysis (SAST) tools and linters to check for code quality, potential bugs, and style guide adherence. Fail the build if critical issues are found.
* **Formatting:** Enforce code formatting standards automatically.
* **Build Artifacts:** Produce build artifacts (e.g., container images, compiled binaries, packages) consistently at the end of the CI stage if all previous steps pass.

## Security Integration (DevSecOps)

* **Secrets Management:**
    * Never hardcode secrets (API keys, passwords, tokens) in pipeline definitions or source code.
    * Use the CI/CD tool's built-in secrets management features or integrate with external secrets managers (e.g., HashiCorp Vault, AWS Secrets Manager, Azure Key Vault, GCP Secret Manager).
    * Limit access to secrets based on the pipeline stage or environment.
* **Vulnerability Scanning:**
    * Scan application dependencies for known vulnerabilities (Software Composition Analysis - SCA). Fail the build if high-severity vulnerabilities are found.
    * Scan container images for OS and library vulnerabilities before pushing them to a registry.
    * Integrate Static Application Security Testing (SAST) tools to analyze source code for security flaws.
* **Dynamic Analysis (DAST):** Consider running DAST scans against deployed applications in staging environments.
* **Infrastructure as Code (IaC) Scanning:** Scan IaC code (Terraform, CloudFormation, ARM/Bicep) for security misconfigurations using tools like `tfsec`, `checkov`.
* **Least Privilege:** Configure CI/CD runners and deployment agents with the minimum necessary permissions to perform their tasks. Avoid overly broad permissions.

## Artifact Management

* **Versioning:** Version all build artifacts (e.g., using SemVer for application releases, Git commit hash for development builds).
* **Artifact Repository:** Store build artifacts in a dedicated artifact repository (e.g., Nexus, Artifactory, Docker Hub, ECR, ACR, GCR). Do not store large binaries directly in Git.
* **Immutability:** Treat build artifacts as immutable. Once built and versioned, an artifact should not be changed. If changes are needed, build a new version.
* **Promotion:** Promote artifacts through environments (dev -> staging -> prod) rather than rebuilding them for each environment. Ensure the same artifact tested in staging is deployed to production.
* **Cleanup:** Implement policies to clean up old or unused artifacts in the repository to manage storage costs.

## Continuous Deployment/Delivery (CD)

* **Environments:** Maintain distinct deployment environments (e.g., development, testing/QA, staging, production). Staging should mirror production as closely as possible.
* **Automated Deployments:** Automate the deployment process to ensure consistency and reduce manual errors.
* **Deployment Strategies:** Choose appropriate deployment strategies based on application needs and risk tolerance:
    * **Rolling Update:** Gradually replace old instances with new ones. Default in Kubernetes.
    * **Blue/Green Deployment:** Deploy the new version alongside the old one, then switch traffic. Allows easy rollback.
    * **Canary Release:** Gradually roll out the new version to a small subset of users/servers before a full release. Allows testing in production with limited impact.
    * **A/B Testing:** Deploy different versions to different user segments for comparison (often more feature-related than pure deployment).
* **Approval Gates:** Include manual approval steps in the pipeline before deploying to critical environments like production.
* **Infrastructure as Code (IaC):** Manage target infrastructure (servers, clusters, databases) using IaC integrated into the deployment pipeline.
* **Configuration Management:** Manage environment-specific configurations separately from application code (e.g., using ConfigMaps/Secrets in K8s, external configuration services, environment variables injected by the pipeline).
* **Rollback Strategy:** Have a documented and tested rollback plan. Automate rollback procedures within the pipeline where possible.

## Monitoring & Feedback

* **Pipeline Monitoring:** Monitor the health, duration, and success/failure rates of CI/CD pipelines. Identify bottlenecks or frequently failing stages.
* **Deployment Monitoring:** Monitor deployments closely for errors or performance degradation immediately after release. Integrate application performance monitoring (APM) and logging tools.
* **Alerting:** Set up alerts for pipeline failures and critical deployment issues.
* **Feedback Loop:** Ensure deployment status, test results, and monitoring alerts are clearly communicated back to the development team.

## CI/CD Tool-Specific Guidelines

### GitHub Actions

* **Workflow Files:** Place workflow files in the `.github/workflows` directory. Use descriptive names for workflow files (e.g., `ci.yml`, `deploy-prod.yml`).
* **Reusable Workflows:** Create reusable workflows for common CI/CD patterns shared across repositories.
* **Secrets:** Use GitHub Secrets for sensitive data. Consider organization-level secrets for shared credentials.
* **Environments:** Define environments in repository settings with protection rules and secrets for different deployment targets.
* **Caching:** Use GitHub's built-in caching to speed up builds by caching dependencies.
* **Self-hosted Runners:** Consider self-hosted runners for specific needs (private network access, specialized hardware).
* **Matrix Builds:** Use matrix strategy for testing across multiple configurations (OS, language versions).

### Jenkins

* **Jenkinsfile:** Define pipelines in a `Jenkinsfile` using declarative pipeline syntax wherever possible.
* **Shared Libraries:** Create shared libraries for reusable pipeline components.
* **Credentials:** Use Jenkins Credentials Provider for secret management. Never expose credentials in pipeline code.
* **Agents:** Use consistent agent definitions (Docker where possible) to ensure reproducible builds.
* **Pipeline Steps:** Prefer predefined pipeline steps over shell scripts for better portability.
* **Notifications:** Configure post-build actions to notify teams of build results via appropriate channels.
* **Archiving:** Archive important artifacts and test results for audit and debugging purposes.

### GitLab CI/CD

* **Pipeline Configuration:** Define pipelines in `.gitlab-ci.yml` at the root of the repository.
* **Templates:** Use includes and extends for template reuse and inheritance.
* **Variables:** Use GitLab CI/CD variables for configuration. Protect sensitive variables in project settings.
* **Stages:** Clearly define pipeline stages that reflect your workflow.
* **Caching & Artifacts:** Use caching for dependencies and artifacts to pass data between jobs.
* **Runners:** Tag jobs to use appropriate runners (shared, specific, or Docker executors).
* **Auto DevOps:** Consider GitLab Auto DevOps for standard applications with common patterns.

## Branching Strategies & Workflows

* **Trunk-Based Development:** Prefer trunk-based development (main branch as primary) with short-lived feature branches for faster integration.
* **Branch Protection:** Enforce branch protection rules on main branches, requiring code reviews and passing CI checks before merging.
* **Feature Branches:** Use short-lived feature branches for development. Name them descriptively (e.g., `feature/user-authentication`).
* **Environment Branches:** Consider specific branches for environments (`main` -> production, `develop` -> staging) or use tags/releases.
* **Pull/Merge Requests:** Require pull/merge requests for all changes to protected branches. Include description templates prompting for change summary, testing details, etc.
* **Commit Standards:** Encourage clear, descriptive commit messages. Consider conventional commits format (`type(scope): message`).

## Documentation & Communication

* **Pipeline Documentation:** Document the CI/CD pipeline architecture, including stages, gates, and security controls.
* **Badges:** Include build/deployment status badges in README files.
* **Change Log:** Maintain an automated or semi-automated change log from commit messages or PR/MR descriptions.
* **Notifications:** Configure appropriate notifications for pipeline events (failures, approvals needed).
* **Runbooks:** Create runbooks for handling common pipeline failures or emergency procedures.
