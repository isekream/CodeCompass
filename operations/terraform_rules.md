# Terraform Rules

## General Style & Structure

* **File Structure:** Organize Terraform configurations logically.
    * Keep root module files (`main.tf`, `variables.tf`, `outputs.tf`, `providers.tf`, `terraform.tf`, `backend.tf`) in the root directory of a configuration set (e.g., per environment or application layer).
    * Group related resources within `main.tf` or split into logical files (e.g., `network.tf`, `compute.tf`) if the configuration grows large. Avoid putting every resource in its own file.
    * Place reusable modules in a dedicated `modules/` directory within the repository or use external module sources.
* **Formatting:** Use `terraform fmt` to automatically format all `.tf` files consistently (2-space indent, alignment). Enforce this in CI.
* **Naming Conventions:**
    * Use `_` (underscore) as a separator, not `-` (hyphen), for resource names, variable names, outputs, etc.
    * Use lowercase letters and numbers.
    * Resource names should be descriptive nouns, avoiding repetition of the resource type (e.g., use `resource "aws_instance" "web_app"` not `resource "aws_instance" "web_app_aws_instance"`). Use singular nouns.
    * Variable names should be descriptive. Use singular nouns for single values and plural nouns for lists/maps.
    * Output names should describe the property being returned (e.g., `public_ip`, `instance_ids`). Consider the `{name}_{type}_{attribute}` pattern for module outputs (e.g., `web_app_instance_id`).
* **Comments:** Use `#` for single-line and multi-line comments. Explain the *why* behind non-obvious configurations or complex logic.
* **Complexity:** Limit the complexity of individual expressions (e.g., avoid multiple nested ternary operations). Use local values to build up complex logic step-by-step.
* **Root Modules:** Keep root modules focused. Avoid managing hundreds of resources in a single state file, as this slows down `plan`/`apply` operations. Split large infrastructures into smaller, independently manageable configurations (e.g., by environment, region, application layer).

## State Management

* **Remote State:** Always use remote state backends (e.g., AWS S3, Azure Blob Storage, GCS Bucket, Terraform Cloud/Enterprise) for collaboration and reliability. Do not store state files locally for shared projects.
* **State Locking:** Use a remote backend that supports state locking (e.g., S3 with DynamoDB, Azure Blob with Lease, GCS with Locking, Terraform Cloud) to prevent concurrent runs from corrupting the state.
* **State Encryption:** Ensure the remote state backend encrypts the state file at rest. Use backend configurations that enforce this (e.g., `encrypt = true` for S3).
* **Versioning:** Enable versioning on the remote state backend (e.g., S3 bucket versioning) to allow recovery from accidental state modifications or corruption.
* **Isolation:** Use separate state files for different environments (dev, staging, prod), regions, or major application components. Achieve this via:
    * Separate directories, each with its own backend configuration (using different keys/paths).
    * Terraform Workspaces (use with caution, mainly for temporary feature branches or minor variations, not typically recommended for strong environment separation due to shared backend).
* **Backups:** Regularly back up your remote state file, even with versioning enabled.
* **Sensitive Data:** Avoid storing secrets directly in the state file whenever possible. Some resources might inevitably write sensitive data; ensure state backend access is tightly controlled and state is encrypted. Mark sensitive outputs as `sensitive = true`.
* **Manual Changes:** Avoid manually editing the state file (`terraform.tfstate`). Use Terraform commands (`terraform import`, `terraform state mv`, `terraform state rm`) for state manipulation when necessary.

## Modules

* **Use Modules:** Encapsulate reusable sets of resources into modules to promote DRY (Don't Repeat Yourself) principles and consistency.
* **Module Structure:** Follow the standard Terraform module structure (`main.tf`, `variables.tf`, `outputs.tf`, `README.md`).
* **Source:** Use reliable module sources (Terraform Registry, private registry, version-controlled Git repository). Pin module versions using the `version` attribute in the `module` block or `ref`/`tag` for Git sources.
* **Scope:** Design modules with a clear and focused purpose. Avoid creating overly complex "god" modules. Group resources that are deployed and managed together.
* **Interface (Variables & Outputs):**
    * Expose configurable parameters as input variables (`variables.tf`). Provide descriptions and types for all variables. Use variable validation where appropriate.
    * Expose useful information from the module's resources as outputs (`outputs.tf`). Provide descriptions for all outputs.
    * Minimize the number of exposed variables in the initial version (MVP); add more as needed. Avoid parameterizing everything unnecessarily.
* **Versioning:** Version modules using Git tags following SemVer. Reference specific versions in root configurations.
* **Documentation:** Provide a `README.md` for each module explaining its purpose, inputs, outputs, requirements, and usage examples.

## Variables & Outputs

* **`variables.tf`:** Declare all input variables in `variables.tf`.
    * Provide a `description` and `type` for every variable.
    * Use specific types (`string`, `number`, `bool`, `list(...)`, `map(...)`, `object(...)`, `set(...)`) where possible instead of `any`.
    * Provide sensible `default` values for optional variables. Do not provide defaults for required, environment-specific variables (e.g., `project_id`).
    * Use `validation` blocks to enforce constraints on variable values.
    * Mark sensitive input variables as `sensitive = true`.
* **`outputs.tf`:** Declare all outputs in `outputs.tf`.
    * Provide a `description` for every output.
    * Mark sensitive outputs as `sensitive = true`. This prevents them from being displayed in CLI output, but the value is still present in the state file.
    * Expose only outputs that are genuinely useful to the parent module or for informational purposes.
* **Variable Definitions Files (`.tfvars`):** Use `.tfvars` files (e.g., `terraform.tfvars`, `dev.tfvars`) to provide values for variables, especially for environment-specific configurations. Do NOT commit `.tfvars` files containing secrets to version control. Use environment variables or secure methods for secrets.
* **Local Values (`locals.tf`):** Use `locals` to define intermediate values or simplify complex expressions. Keep local value definitions in a separate `locals.tf` file for clarity.

## Security

* **Least Privilege:** Ensure Terraform runs with the minimum necessary permissions (via IAM roles, service principals, etc.) to manage the intended resources.
* **Secrets Management:**
    * Do NOT hardcode secrets (API keys, passwords, certificates) in `.tf` or `.tfvars` files.
    * Use secure methods like environment variables (`TF_VAR_...`), dedicated secrets management tools (HashiCorp Vault, AWS Secrets Manager, Azure Key Vault, GCP Secret Manager) accessed via provider data sources, or Terraform Cloud/Enterprise sensitive variables.
    * Mark variables and outputs containing sensitive data as `sensitive = true`.
    * Be aware that sensitive data might still end up in plan files or state files; protect access accordingly.
* **State Security:** Secure access to the remote state backend (restrict permissions, enable encryption at rest and in transit).
* **Static Analysis:** Use static analysis tools (e.g., `tfsec`, `checkov`, `terrascan`) in CI/CD pipelines to scan Terraform code for security misconfigurations before applying changes.
* **Policy as Code:** Use policy-as-code tools (e.g., Sentinel with Terraform Cloud/Enterprise, Open Policy Agent) to enforce security, compliance, and cost policies before infrastructure changes are applied.
* **Provider Versions:** Lock provider versions in the `terraform { required_providers { ... } }` block to prevent unexpected changes or vulnerabilities introduced by newer provider versions. Regularly update and test provider versions.
* **Code Review:** Implement mandatory code reviews for all Terraform changes via pull requests.

## CI/CD & Workflow

* **Version Control:** Store all Terraform code (`.tf`, `.tfvars` - excluding secrets) in a version control system (e.g., Git).
* **Automation:** Use a CI/CD pipeline (e.g., GitHub Actions, GitLab CI, Jenkins, Terraform Cloud) to automate Terraform workflows (`fmt`, `validate`, `plan`, `apply`).
* **`validate` & `fmt`:** Always run `terraform fmt -check` and `terraform validate` in the pipeline.
* **`plan`:** Always generate a plan (`terraform plan -out=tfplan`) and review it carefully before applying. Store plan artifacts securely if needed for apply stage.
* **`apply`:** Apply changes using the saved plan file (`terraform apply tfplan`) for consistency.
* **Branching Strategy:** Use a standard branching strategy (e.g., Gitflow, GitHub Flow). Apply changes from the main branch or dedicated release branches, not feature branches.
* **Testing:** Implement automated testing for Terraform code, especially modules:
    * Unit tests (e.g., using `terraform test` for module checks).
    * Integration tests (e.g., using Terratest, Kitchen-Terraform) to deploy real infrastructure in a test environment and verify its state.

## Resource Management

* **Resource Dependencies:** Use explicit dependencies (`depends_on`) only when Terraform cannot automatically detect the dependency relationship. Typically, Terraform infers dependencies based on reference relationships between resources.
* **Resource Naming:** Use a consistent resource naming convention to make resources easily identifiable in the management console. Consider a standard pattern like `{project}-{environment}-{resource-type}-{purpose}`.
* **Count & For Each:** Use `count` for simple, integer-based resource repetition or conditional creation. Use `for_each` for creating multiple resources based on a map or set, which provides more explicit identification and better handling of resource changes and removals.
* **Data Sources:** Use data sources to reference existing resources or to fetch information from external sources, rather than hardcoding values that might change.
* **Lifecycle Settings:** Carefully apply lifecycle settings where needed:
    * `create_before_destroy`: Set to `true` when new resources must be created before old ones are destroyed (e.g., for zero-downtime replacements).
    * `prevent_destroy`: Set to `true` for critical resources that should never be accidentally destroyed (e.g., production databases).
    * `ignore_changes`: Use to exclude specific attributes from ongoing management by Terraform when they're modified externally or frequently change (e.g., tags modified by automated systems).
* **Import:** Import existing resources into Terraform state instead of recreating them to bring manual or legacy infrastructure under Terraform management.

## Provider Configuration

* **Provider Versions:** Pin provider versions in the `terraform { required_providers { ... } }` block to prevent unexpected behavior from provider updates.
* **Multiple Providers:** When using multiple providers or multiple configurations of the same provider (e.g., different regions), use provider aliases and explicitly specify which provider configuration to use for each resource.
* **Provider Organization:** For multi-region or multi-account deployments, use a provider configuration structure that clearly separates different contexts.
* **Authentication:** Use secure authentication methods for providers. Avoid hardcoding credentials in provider blocks; use environment variables, assumed roles, or other secure credential sources.
* **Required Terraform Version:** Specify the `required_version` attribute in the `terraform` block to ensure consistent Terraform core behavior across different environments and users.

## Testing and Documentation

* **README:** Provide a comprehensive README file for each Terraform project or module, including:
    * Purpose and overview
    * Prerequisites and requirements
    * Usage examples
    * Input variables and outputs
    * Deployment instructions
* **Documentation Comments:** Add meaningful comments in code to explain complex logic, rationale for decisions, and references to external documentation or requirements.
* **Testing:** Implement a testing strategy for Terraform code:
    * Basic validation (`terraform validate`, `terraform plan`)
    * Static analysis for security and best practices (tfsec, checkov, etc.)
    * Unit testing for modules
    * Integration testing with actual infrastructure deployment (dev/test environment)
* **Examples:** Provide working examples of how to use modules or configurations in different scenarios.

## Cost Optimization

* **Resource Sizing:** Right-size resources to match actual needs rather than over-provisioning.
* **Cost Estimation:** Use `terraform plan -out=tfplan` followed by `terraform show -json tfplan | jq` with cost estimation tools or cloud provider pricing calculators to estimate costs before applying changes.
* **Tagging Strategy:** Implement a consistent tagging strategy for all resources to enable cost tracking and allocation.
* **Lifecycle Management:** Use `auto_scaling` features and time-based resource creation/destruction for non-production environments to reduce costs during off-hours.
* **Use Spot/Preemptible Instances:** For non-critical, fault-tolerant workloads, consider using spot/preemptible instances to reduce compute costs.
