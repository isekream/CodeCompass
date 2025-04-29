# Docker Rules

## Dockerfile Best Practices

* **Minimal Base Image:** Start with a minimal, trusted base image (e.g., `alpine`, `debian-slim`, official language-specific slim variants, distroless images). Avoid full OS images unless strictly necessary.
* **Use Specific Tags:** Use specific version tags for base images (e.g., `python:3.11-slim`) instead of `latest` to ensure reproducible builds and avoid unexpected breaking changes.
* **Multi-Stage Builds:** Utilize multi-stage builds to separate build-time dependencies from the runtime environment. Copy only necessary artifacts (binaries, assets) to the final, smaller runtime stage. This significantly reduces image size and attack surface. Name your stages (`AS builder`) for clarity.
* **Optimize Layer Caching:** Order Dockerfile instructions from least frequently changing to most frequently changing to leverage the build cache effectively. Place commands like installing dependencies (`RUN apt-get update && apt-get install...`) before `COPY` commands that bring in frequently changing application code.
* **Combine RUN Commands:** Chain related `RUN` commands using `&&` and line continuation (`\`) to reduce the number of image layers. For example, update package lists, install packages, and clean up caches in a single `RUN` command.
    * Example (`apt`): `RUN apt-get update && apt-get install -y --no-install-recommends package1 package2 && rm -rf /var/lib/apt/lists/*`
* **Use COPY over ADD:** Prefer `COPY` over `ADD` unless you specifically need `ADD`'s features like auto-extraction or remote URL fetching. `COPY` is more explicit and predictable.
* **Use `.dockerignore`:** Create a `.dockerignore` file to exclude unnecessary files and directories (e.g., `.git`, `node_modules`, local build artifacts, temporary files, secrets) from the build context. This speeds up builds and prevents sensitive information from being included in the image.
* **Non-Root User:** Run container processes as a non-root user for enhanced security. Create a dedicated user and group, set appropriate file permissions (`chown`/`chmod`), and switch to that user using the `USER` instruction before the final `CMD` or `ENTRYPOINT`. Ensure application directories are writable by this user if needed.
* **Metadata Labels:** Use the `LABEL` instruction to add metadata to your images (e.g., maintainer, version, description, project link).
* **Executable Permissions:** Ensure application executables are owned by root and not world-writable, even if run by a non-root user.
* **HEALTHCHECK:** Include a `HEALTHCHECK` instruction to allow Docker (and orchestrators like Kubernetes) to monitor the container's health status.
* **ENTRYPOINT vs CMD:** Understand and use `ENTRYPOINT` (the main executable) and `CMD` (default arguments, easily overridden) appropriately. Often used together in `exec` form: `ENTRYPOINT ["python"]`, `CMD ["app.py"]`.
* **Lint Dockerfiles:** Use linters like Hadolint to check Dockerfiles for common mistakes and adherence to best practices.

## Image Optimization & Security

* **Minimize Packages:** Install only strictly necessary packages in the final image to reduce size and attack surface. Remove package manager caches after installation (e.g., `rm -rf /var/lib/apt/lists/*`).
* **Regular Rebuilds:** Regularly rebuild images from the Dockerfile to incorporate updated base images and security patches for OS packages and dependencies.
* **Vulnerability Scanning:** Integrate image vulnerability scanning tools (e.g., `docker scout`, Trivy, Grype, Snyk) into your CI/CD pipeline to detect known vulnerabilities in OS packages and application dependencies before deployment.
* **Trusted Base Images:** Use base images only from trusted sources (e.g., Docker Official Images, Verified Publishers).
* **Do Not Hardcode Secrets:** Never hardcode secrets (passwords, API keys, tokens) directly in the Dockerfile (via `ENV`, `ARG`, or commands). Use secure methods like build-time secrets (BuildKit), runtime environment variables, or dedicated secrets management tools (Docker Secrets, HashiCorp Vault, cloud provider secrets managers).
* **Sign Images:** Use Docker Content Trust (or alternatives like Sigstore/cosign) to sign images, allowing consumers to verify image authenticity and integrity.

## Container Runtime Security & Best Practices

* **Run as Non-Root:** Ensure containers run processes as a non-privileged user (see Dockerfile section).
* **Read-Only Filesystem:** Run containers with a read-only root filesystem (`--read-only`) whenever possible. Mount temporary filesystems (`--tmpfs`) for paths that require writes.
* **Drop Capabilities:** Drop all Linux capabilities (`--cap-drop=ALL`) and explicitly add back only those strictly required by the container process (`--cap-add=NET_BIND_SERVICE`, etc.).
* **Prevent Privilege Escalation:** Prevent containers from gaining additional privileges (`--security-opt=no-new-privileges`).
* **Avoid Privileged Mode:** Do NOT run containers in privileged mode (`--privileged`) unless absolutely essential and fully understood, as it bypasses nearly all security mechanisms.
* **Limit Resources:** Set resource limits (CPU, memory) for containers (`--memory`, `--cpus`) to prevent resource exhaustion on the host.
* **Docker Daemon Security:**
    * Do NOT expose the Docker daemon socket (`/var/run/docker.sock`) inside containers unless strictly necessary and the implications are understood. Access to the socket effectively grants root access to the host.
    * Do NOT expose the Docker daemon over an unauthenticated TCP socket. If remote access is needed, secure it using TLS.
    * Consider running Docker in rootless mode for enhanced security.
* **Networking:**
    * Do not expose unnecessary ports. Only expose ports required for the application using the `EXPOSE` instruction (documentation) and the `-p`/`-P` flags (runtime).
    * Use custom Docker networks to isolate containers and control inter-container communication. Avoid the default bridge network for production applications. Disable inter-container communication (`--icc=false`) on the default bridge if not needed.
* **Secrets Management:** Use Docker Secrets (Swarm), Kubernetes Secrets, or external secrets management tools to inject sensitive data into containers at runtime, rather than using environment variables for secrets.
* **Logging:** Configure container logging appropriately (e.g., use logging drivers to send logs to a centralized logging system) instead of relying solely on log files within the container.
* **Health Checks:** Implement application-level health checks accessible via the `HEALTHCHECK` instruction or orchestration system probes.

## General

* **Keep Docker Updated:** Keep the Docker Engine and client up-to-date on host machines to benefit from the latest features and security patches.
* **Host Security:** Harden the host operating system where Docker runs. Apply OS patches, use host-level security modules (SELinux, AppArmor).
* **Cleanup:** Regularly prune unused containers, images, volumes, and networks (`docker system prune`) to reclaim disk space.

## Docker Compose Best Practices

* **Version Control:** Store docker-compose.yml files in version control alongside application code.
* **Environment Variables:** Use `.env` files for non-sensitive environment variables and integrate with a secrets management solution for sensitive data.
* **Service Segregation:** Organize services by their function, with clear dependency chains.
* **Network Segmentation:** Create separate networks for frontend/backend services.
* **Volume Management:** Use named volumes instead of bind mounts for persistent data, and document their purpose.
* **Configuration Reuse:** Utilize YAML anchors and extensions to avoid repetition in complex configurations.
* **Health Checks:** Define health checks for services to ensure dependencies start in the correct order.
* **Resource Constraints:** Set memory and CPU limits for services to prevent resource contention.
* **Security Context:** Specify user IDs, group IDs, and read-only flags where appropriate.

## Docker in CI/CD Pipelines

* **Caching Strategy:** Configure your CI system to cache Docker layers between builds to speed up the process.
* **Parallel Builds:** Use parallelism for building multiple images or testing stages.
* **Matrix Testing:** Test containers with different configurations and base images where appropriate.
* **Vulnerability Scanning:** Automatically scan images for vulnerabilities before pushing to registries.
* **Image Promotion:** Implement a promotion process for images moving through dev, staging, and production.
* **Tagging Strategy:** Use meaningful tags beyond 'latest': commit hashes, semantic versions, branch names, or build numbers.
* **Registry Authentication:** Use short-lived credentials or OIDC authentication for registry access.
* **Pipeline Documentation:** Document how Docker is used in your CI/CD process for new team members.

## Container Orchestration Readiness

* **Configuration Compatibility:** Design Dockerfiles and configurations to work seamlessly with orchestration platforms (Kubernetes, Swarm, ECS).
* **Signal Handling:** Ensure applications properly handle SIGTERM signals for graceful shutdowns.
* **Statelessness:** Design containers to be stateless where possible, storing persistent data in external systems.
* **Logging to STDOUT/STDERR:** Direct logs to standard output/error streams instead of files to enable log collection by orchestration platforms.
* **Disposability:** Design containers to start quickly and shut down gracefully.
* **Port Configuration:** Document required ports and ensure they're properly exposed in your Dockerfile.
* **Health and Readiness:** Distinguish between liveness (is the container running) and readiness (is it ready to accept traffic) checks.
* **Init Process:** Use an init process in containers with multiple processes to properly handle signals and zombie processes.
