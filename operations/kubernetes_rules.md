# Kubernetes (K8s) Rules

## General Principles & Cluster Organization

* **Namespaces:** Use namespaces to logically partition cluster resources (e.g., by environment: `dev`, `staging`, `prod`; by team; by application). Avoid deploying application workloads directly into the `default` namespace. Apply RBAC, Network Policies, Resource Quotas, and Limit Ranges at the namespace level.
* **Labels:** Apply meaningful labels to all resources (Pods, Deployments, Services, etc.) for organization, selection, and operational purposes (e.g., `app: myapp`, `environment: production`, `tier: frontend`, `owner: team-a`). Use consistent label keys and values.
* **Annotations:** Use annotations for non-identifying metadata, such as descriptions, links to monitoring dashboards, or tool-specific information.
* **Declarative Configuration:** Define all Kubernetes objects using declarative YAML manifests stored in version control (Git). Avoid imperative commands (`kubectl run`, `kubectl create`) for managing persistent resources in favor of `kubectl apply -f <file/directory>`.
* **Latest Stable Version:** Keep your Kubernetes cluster (control plane and nodes) updated to the latest stable version supported by your provider or community to benefit from new features and security patches. Plan upgrades carefully.
* **Infrastructure as Code (IaC):** Manage the cluster infrastructure itself using IaC tools (Terraform, Pulumi, Crossplane, etc.).

## Manifests & Resources

* **Deployments:**
    * Use Deployments for stateless applications. Avoid creating "naked" Pods directly.
    * Define a clear `strategy` (e.g., `RollingUpdate`) with appropriate `maxSurge` and `maxUnavailable` settings to ensure availability during updates.
    * Use descriptive names and apply consistent labels for Deployments and their associated Pods. Use the `spec.selector.matchLabels` field correctly to link Deployments to their Pods.
    * Define Pod templates (`spec.template`) within the Deployment manifest.
* **Services:**
    * Use Services (`ClusterIP`, `NodePort`, `LoadBalancer`, `ExternalName`) to provide stable network endpoints for Pods.
    * Use `ClusterIP` for internal communication within the cluster.
    * Use `LoadBalancer` type cautiously, as it usually provisions a cloud load balancer which incurs costs. Prefer using Ingress for external access.
    * Use `NodePort` mainly for development/debugging or specific use cases; avoid it for production external access.
    * Ensure the Service `selector` correctly matches the labels of the Pods it should target.
    * Create Services *before* the Deployments/Pods that need to consume them to ensure DNS and environment variables are available upon Pod startup.
* **Ingress:**
    * Use Ingress resources managed by an Ingress Controller (e.g., Nginx Ingress, Traefik) to expose HTTP/S services outside the cluster.
    * Define rules for routing traffic based on hostnames and paths.
    * Secure Ingress resources with TLS certificates, preferably managed automatically using tools like `cert-manager`.
* **StatefulSets:** Use StatefulSets for stateful applications that require stable network identifiers, persistent storage per Pod, and ordered, graceful deployment/scaling.
* **DaemonSets:** Use DaemonSets to run a copy of a Pod on all (or specific) nodes in the cluster (e.g., for logging agents, monitoring agents).
* **Jobs & CronJobs:** Use Jobs for run-to-completion tasks and CronJobs for scheduled tasks. Define appropriate restart policies and completion/failure handling.
* **Pod Disruption Budgets (PDBs):** Define PDBs for critical applications to limit the number of Pods that can be voluntarily disrupted simultaneously during maintenance or node drains, ensuring minimum availability.

## Configuration Management

* **ConfigMaps:** Use ConfigMaps to store non-sensitive configuration data (e.g., environment variables, configuration files) as key-value pairs.
    * Mount ConfigMaps as volumes to inject configuration files into Pods.
    * Inject ConfigMap data as environment variables. Be mindful that updates to ConfigMaps are not automatically reflected in environment variables of running Pods (requires Pod restart). Mounting as a volume often allows for dynamic updates within the container if the application supports reloading configuration.
    * Keep ConfigMaps focused; avoid creating massive ConfigMaps with unrelated configurations.
* **Secrets:** Use Secrets to store sensitive data (API keys, passwords, TLS certificates).
    * **Do NOT store Secrets directly in version control, even base64 encoded.** Base64 is encoding, not encryption.
    * Use secure methods for managing Secrets:
        * Leverage external secrets management tools (e.g., HashiCorp Vault, cloud provider secrets managers) integrated via operators like External Secrets Operator or CSI drivers (Secrets Store CSI Driver). This is the recommended approach for production.
        * Use tools like Sealed Secrets or SOPS (Secrets OPerationS) to encrypt secrets before committing them to Git (GitOps workflow).
        * Enable Encryption at Rest for Secrets in etcd within the cluster configuration.
    * Mount Secrets as volumes (preferred for files like TLS certs or config files containing secrets) or inject them as environment variables (use with caution, as env vars can be logged or inspected more easily).
    * Apply RBAC rules to restrict `get`, `list`, `watch` access to Secrets.

## Security

* **RBAC (Role-Based Access Control):**
    * Enable RBAC on your cluster.
    * Follow the principle of least privilege: Create specific `Roles` (namespace-scoped) or `ClusterRoles` (cluster-scoped) with minimum necessary permissions (`verbs` on `resources`).
    * Assign roles to users, groups, or service accounts using `RoleBindings` or `ClusterRoleBindings`. Prefer binding roles to groups or service accounts over individual users.
    * Regularly audit RBAC roles and bindings.
* **Service Accounts:**
    * Create dedicated Service Accounts for applications/pods instead of using the `default` service account.
    * Grant minimal necessary RBAC permissions to these Service Accounts.
    * Do not automatically mount the service account token in Pods if they don't need to interact with the Kubernetes API (`automountServiceAccountToken: false`).
* **Security Contexts (Pod & Container):** Define `securityContext` at the Pod and/or Container level to control security settings:
    * `runAsUser` / `runAsGroup`: Run containers as a specific non-root user/group ID.
    * `runAsNonRoot: true`: Enforce that containers must run as a non-root user.
    * `readOnlyRootFilesystem: true`: Make the container's root filesystem read-only. Mount volumes for necessary write paths.
    * `allowPrivilegeEscalation: false`: Prevent processes from gaining more privileges than their parent process.
    * `capabilities`: Drop unnecessary Linux capabilities (`drop: ["ALL"]`) and add only specific required capabilities (`add: ["NET_BIND_SERVICE"]`).
    * `seccompProfile`: Apply seccomp profiles (e.g., `RuntimeDefault` or custom profiles) to restrict allowed syscalls.
    * `fsGroup`: Set a supplemental group ID for accessing shared volumes.
* **Pod Security Standards (PSS) / Pod Security Admission (PSA):** Use PSA (built-in admission controller) configured with PSS profiles (`privileged`, `baseline`, `restricted`) at the namespace level to enforce security context standards. Aim for `baseline` or `restricted` profiles where possible. (Note: PodSecurityPolicy (PSP) is deprecated).
* **Network Policies:** Use Network Policies to control traffic flow between Pods at the IP/port level (L3/L4). Implement default deny policies and explicitly allow required traffic between applications/namespaces. Requires a network plugin that supports NetworkPolicy (e.g., Calico, Cilium).
* **Secrets Security:** Implement strong practices for Secrets management (see Configuration Management section). Enable encryption at rest for Secrets in etcd.
* **Image Security:** Scan container images for vulnerabilities before deployment and regularly during runtime. Use trusted, minimal base images.
* **API Server Access:** Secure access to the Kubernetes API server. Use strong authentication, RBAC, and restrict network access where possible. Disable anonymous authentication.
* **etcd Security:** Secure the etcd datastore (restrict network access, use TLS encryption for communication, encrypt data at rest).

## Resource Management

* **Requests and Limits:** Define CPU and Memory `requests` (guaranteed allocation) and `limits` (maximum allowed usage) for all containers in production workloads.
    * **Requests:** Set based on typical application usage. Ensures the Pod gets scheduled on a node with sufficient resources. Affects QoS Class.
    * **Limits:** Set based on maximum tolerated usage. Prevents resource hogging. CPU limits lead to throttling; memory limits lead to OOMKilled (container termination).
    * Set requests and limits equal (`requests.cpu == limits.cpu`, `requests.memory == limits.memory`) for `Guaranteed` QoS class for predictable performance, especially for critical workloads.
    * Set requests lower than limits for `Burstable` QoS class, allowing containers to use more resources if available but with less performance guarantee.
    * Avoid setting CPU limits unless necessary for specific reasons (like cost allocation in multi-tenant clusters), as it can lead to unnecessary throttling even with available CPU on the node. Focus on setting appropriate CPU requests. Set memory limits to prevent OOM kills from impacting the node.
* **LimitRange:** Define `LimitRange` objects per namespace to set default requests/limits for containers and enforce min/max resource constraints.
* **ResourceQuota:** Define `ResourceQuota` objects per namespace to limit the total amount of resources (CPU, memory, storage, object counts) that can be consumed within that namespace. Essential for multi-tenant environments.
* **Monitoring:** Monitor resource usage (CPU, memory, disk, network) using tools like `metrics-server`, Prometheus/Grafana stack, or cloud provider monitoring services to inform rightsizing decisions.

## Helm Chart Best Practices

* **Structure:** Follow the standard Helm chart structure (`Chart.yaml`, `values.yaml`, `templates/`, `charts/`, `crds/`).
* **`Chart.yaml`:**
    * Keep `apiVersion`, `name`, and `version` updated. Use Semantic Versioning (SemVer 2.0.0) for `version`.
    * Keep `appVersion` aligned with the application version being deployed.
    * Provide a clear `description`.
    * List `maintainers`.
* **`values.yaml`:**
    * Define clear, well-documented default values for all configurable parameters.
    * Organize values logically (e.g., group related parameters under keys).
    * Avoid hardcoding values in templates; parameterize everything configurable.
    * Use comments (`#`) to explain each parameter.
* **Templates (`templates/`):**
    * Use named templates (`_helpers.tpl`) for reusable snippets of YAML (e.g., labels, resource names, container definitions). Use functions like `include`.
    * Generate resource names dynamically and consistently (e.g., using `{{ include "mychart.fullname" . }}`).
    * Ensure all generated resources include standard labels (e.g., from `_helpers.tpl`) for selection and organization.
    * Use template functions (`if`, `range`, `default`, `required`, etc.) effectively, but keep templates readable.
    * Use `NOTES.txt` to provide useful information to the user after installation.
* **Dependencies (`charts/`, `Chart.yaml:dependencies`):** Manage dependencies explicitly in `Chart.yaml`. Use version constraints. Consider pulling dependencies into the `charts/` directory (`helm dependency update`).
* **Validation:** Use Helm Schema (`values.schema.json`) to validate user-provided values against expected types, formats, and constraints.
* **Testing:** Include chart tests (`templates/tests/`) using Helm's test framework (typically Pods/Jobs that check application health or connectivity) runnable via `helm test`.
* **Linting:** Lint charts using `helm lint` to catch common errors and style issues.
* **Documentation:** Provide a comprehensive `README.md` explaining the chart's purpose, prerequisites, configuration parameters, and usage examples. Consider using tools like `helm-docs` to auto-generate parts of the README from `values.yaml`.
* **Security:** Avoid exposing sensitive information directly in `values.yaml`. Provide mechanisms to use existing Kubernetes Secrets or external secret management solutions.

## GitOps Practices

* **Source of Truth:** Maintain a Git repository as the single source of truth for Kubernetes manifests and configuration.
* **Pull-Based Deployments:** Use GitOps controllers (Flux, ArgoCD) to automatically sync cluster state with the Git repository.
* **Environment Separation:** Structure your GitOps repositories to clearly separate environments (dev, staging, production).
* **Manifest Deployment Order:** Ensure dependencies are applied in the correct order (e.g., CRDs before custom resources, namespaces before namespaced resources).
* **Automated Reconciliation:** Configure controllers to automatically reconcile cluster state with Git repository state at regular intervals.
* **Progressive Delivery:** Implement canary deployments or blue/green deployments for critical services using GitOps-compatible tools.
* **Drift Detection:** Configure controllers to detect and alert on cluster drift from the desired state in Git.

## Monitoring and Observability

* **Metrics Collection:** Deploy a metrics collection solution (Prometheus) with appropriate exporters (node-exporter, kube-state-metrics).
* **Logging Strategy:** Implement a centralized logging solution (ELK stack, Loki) with appropriate log collection agents (Fluentd, Filebeat).
* **Tracing:** Consider distributed tracing (Jaeger, Zipkin) for complex microservices architectures.
* **Dashboards:** Create and maintain standardized dashboards (Grafana) for key metrics and service health.
* **Alerting:** Configure appropriate alerting rules and notification channels for critical conditions.
* **Health Probes:** Define proper liveness, readiness, and startup probes for all containerized applications.
* **Service Mesh:** Consider using a service mesh (Istio, Linkerd) for advanced traffic management, security, and observability.

## Disaster Recovery and Backup

* **Application Data Backup:** Implement backup solutions for stateful applications, preferably using Kubernetes-native tools (Velero).
* **Etcd Backup:** Regularly backup the etcd datastore with appropriate retention periods.
* **Disaster Recovery Plan:** Document and test procedures for recovering from cluster failures or data corruption.
* **Multi-Cluster Strategy:** Consider multi-cluster deployments for high availability across regions or data centers.
* **PersistentVolume Backup:** Ensure all critical PersistentVolumes have appropriate backup strategies.
* **Configuration Backup:** Maintain backups of all cluster configuration separate from application manifests.
