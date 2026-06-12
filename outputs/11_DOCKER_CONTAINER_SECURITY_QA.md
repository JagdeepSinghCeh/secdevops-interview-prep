# Docker And Container Security Deep Q&A

This file prepares you for Docker and container security interview questions in DevSecOps, AppSec, and CloudSec roles. Containers are important because modern CI/CD pipelines usually build container images and deploy them to Kubernetes, ECS, AKS, GKE, Cloud Run, or other platforms.

## Container Fundamentals

### Q1. What is a container?

A container is a lightweight, isolated process environment that packages application code, runtime, dependencies, and configuration needed to run an application. Containers share the host operating system kernel but run with isolated filesystem, process, network, and resource views.

From a security point of view, containers are not the same as full virtual machines. They provide isolation, but the isolation depends on Linux namespaces, cgroups, capabilities, seccomp, AppArmor/SELinux, and runtime configuration. A misconfigured container can create host or cluster risk.

### Q2. What is Docker?

Docker is a platform and toolset used to build, package, distribute, and run containers. It includes concepts like Dockerfile, image, container, registry, volumes, networks, and Docker daemon.

In DevSecOps, Docker security focuses on secure Dockerfiles, trusted base images, vulnerability scanning, secrets handling, non-root execution, minimal images, image signing, and runtime restrictions.

### Q3. What is a Docker image?

A Docker image is a read-only package containing application code, dependencies, filesystem layers, and metadata needed to run a container. Images are built from Dockerfiles and stored in registries.

Security relevance: images may contain vulnerable packages, hardcoded secrets, malware, unnecessary tools, or risky configuration. Image scanning is a standard DevSecOps control.

### Q4. What is a container image layer?

A layer is a filesystem change created by an instruction in a Dockerfile. Images are built from stacked layers.

Security relevance: secrets copied into one layer can remain in image history even if deleted in a later layer. That is why secrets should not be added during image build unless using safe build secret mechanisms.

### Q5. What is a Dockerfile?

A Dockerfile is a text file containing instructions to build a container image. Instructions include `FROM`, `RUN`, `COPY`, `WORKDIR`, `USER`, `EXPOSE`, `CMD`, and `ENTRYPOINT`.

Security review of Dockerfiles checks base image, package installation, root user, secrets, file permissions, exposed ports, unnecessary tools, and update strategy.

### Q6. What is a container registry?

A container registry stores and distributes container images. Examples include Docker Hub, AWS ECR, Azure Container Registry, Google Artifact Registry, Harbor, and GitHub Container Registry.

Security checks include authentication, RBAC, private repositories, image scanning, tag immutability, signing, retention policies, and trusted image sources.

### Q7. What is the difference between image and container?

An image is a static package. A container is a running instance of an image.

Security scanning usually starts with images before deployment, while runtime security monitors running containers after deployment.

### Q8. What is container runtime?

A container runtime runs containers on a host. Examples include containerd, CRI-O, and Docker Engine. Kubernetes uses container runtimes through the Container Runtime Interface.

Security relevance: runtime configuration controls privileges, namespaces, capabilities, mounts, and isolation.

### Q9. Are containers secure by default?

Containers provide useful isolation, but they are not automatically secure. Risks include vulnerable images, root containers, privileged mode, host mounts, secrets in images, excessive capabilities, weak registry access, and insecure orchestration configuration.

Security requires secure build, secure registry, secure deployment, and runtime monitoring.

### Q10. What is the container attack surface?

Container attack surface includes image dependencies, application code, base image, exposed ports, container runtime, Docker daemon, mounted volumes, secrets, registry, orchestrator configuration, and host kernel.

Reducing attack surface means using minimal images, least privilege, restricted capabilities, network policies, and removing unnecessary tools.

## Dockerfile Security

### Q11. How do you secure a Dockerfile?

Use a trusted minimal base image, pin versions, avoid running as root, avoid storing secrets, use multi-stage builds, install only required packages, clean package caches, set correct permissions, avoid unnecessary exposed ports, and scan the final image.

Also use `.dockerignore` to avoid copying secrets, Git folders, local configs, or large unnecessary files into the image.

### Q12. Why should containers not run as root?

If a process runs as root inside the container and escapes or abuses mounted resources, impact can be higher. Running as non-root reduces privilege and limits damage.

In Dockerfile, create a dedicated user and use the `USER` instruction. In Kubernetes, enforce non-root with security context and admission policy.

### Q13. What is a minimal base image?

A minimal base image contains only the packages needed to run the application. Examples include Alpine, distroless images, slim images, or language-specific minimal images.

Security benefit: fewer packages mean fewer vulnerabilities and less attacker tooling inside the container.

### Q14. What are distroless images?

Distroless images contain application runtime dependencies but not a full Linux distribution shell or package manager. They reduce attack surface.

Tradeoff: debugging can be harder because tools like shell, curl, or package managers are absent. Use good observability and debug images for troubleshooting.

### Q15. Why should you pin base image versions?

Pinning avoids unexpected changes when upstream tags move. For example, using `latest` may pull different content over time, causing non-reproducible builds or unexpected vulnerabilities.

Best practice is to pin specific versions and update through a controlled process. For high assurance, pin image digests.

### Q16. Why is using `latest` tag risky?

`latest` does not guarantee the newest or safest image; it is just a tag. It can change without notice and break builds or introduce vulnerabilities.

Use explicit tags or digests and automate updates through Dependabot, Renovate, or image update workflows.

### Q17. What is multi-stage build?

A multi-stage build uses multiple `FROM` stages in a Dockerfile. Build tools and dependencies are used in an earlier stage, while only final artifacts are copied into the runtime stage.

Security benefit: the final image is smaller and does not include compilers, package managers, source code, or build secrets.

### Q18. What is `.dockerignore`?

`.dockerignore` excludes files from the Docker build context. It works like `.gitignore` for Docker builds.

Security use: exclude `.git`, `.env`, private keys, test data, local configs, node_modules if not needed, and other sensitive files from the image build context.

### Q19. Why should secrets not be passed with `ARG` or `ENV` in Dockerfile?

Build arguments and environment variables can appear in image history, metadata, build logs, or runtime inspection. If used for secrets, they may leak.

Use secret managers, runtime injection, CI secret stores, or Docker BuildKit secrets where appropriate.

### Q20. What is Docker BuildKit secret mount?

BuildKit secret mounts allow secrets to be used during image build without storing them in final image layers. It is safer than copying secrets or using build args.

Still, avoid build-time secrets unless necessary and ensure CI logs do not expose them.

### Q21. Why should package manager cache be cleaned?

Package caches increase image size and may include metadata that is not needed at runtime. Smaller images reduce attack surface and storage/transfer overhead.

However, cleaning cache is not a substitute for vulnerability patching.

### Q22. What is the risk of installing debugging tools in production images?

Tools like curl, wget, netcat, bash, package managers, and compilers can help attackers after compromise. They increase attack surface and make post-exploitation easier.

Keep production images minimal. Use separate debug workflows for troubleshooting.

### Q23. Why should file permissions be reviewed inside images?

Weak permissions may allow the application process to modify sensitive files, overwrite binaries, expose secrets, or create persistence.

Use least-privilege ownership and permissions. Avoid world-writable paths unless required.

### Q24. What is `COPY` vs `ADD` in Dockerfile?

`COPY` copies local files into the image. `ADD` can also extract archives and fetch remote URLs. Because `ADD` has extra behavior, `COPY` is usually preferred for clarity and security.

Use `ADD` only when you specifically need its features.

### Q25. Why should you avoid downloading scripts and piping to shell in Dockerfile?

Commands like downloading a script and executing it directly trust remote content at build time. If the source is compromised or changed, the image may include malicious code.

Use trusted package sources, verify checksums/signatures, pin versions, and prefer official installation methods.

## Image Scanning And Supply Chain

### Q26. What is container image scanning?

Container image scanning analyzes image contents for known vulnerabilities, secrets, malware indicators, license issues, and misconfigurations. It checks OS packages, language dependencies, and sometimes image metadata.

Tools include Trivy, Grype, Snyk Container, Docker Scout, Anchore, Prisma Cloud, Aqua, and AWS Inspector/ECR scanning.

### Q27. What does Trivy scan?

Trivy can scan container images, filesystems, Git repositories, IaC files, Kubernetes manifests, secrets, SBOMs, and sometimes live clusters depending on usage.

It is popular because it is open-source, easy to run in CI, and covers multiple DevSecOps categories.

### Q28. What is Grype?

Grype is an open-source vulnerability scanner for container images and filesystems. It is often used with Syft, which generates SBOMs.

Grype is useful for identifying OS and language package vulnerabilities.

### Q29. What is Docker Scout?

Docker Scout is Docker's tool for analyzing image vulnerabilities, package inventory, and recommendations. It integrates with Docker workflows.

Interview point: tool choice may vary, but the process is the same: scan, prioritize, fix, rescan, and enforce policy.

### Q30. What is an SBOM for containers?

An SBOM lists packages, libraries, and components inside a container image. It helps identify affected images when new CVEs are announced.

Common SBOM formats include CycloneDX and SPDX. Tools include Syft, Trivy, and Docker Scout.

### Q31. What is image signing?

Image signing uses cryptographic signatures to prove that an image came from a trusted build process and was not tampered with.

Tools include Cosign and Notary. In Kubernetes, admission policies can require signed images before deployment.

### Q32. What is Cosign?

Cosign is a Sigstore tool used to sign and verify container images and other artifacts. It supports key-based and keyless signing workflows.

DevSecOps use: sign images in CI after successful security checks, then verify signatures before deployment.

### Q33. What is image provenance?

Provenance records how an image was built, from which source code, by which pipeline, and with which dependencies. It helps prove supply-chain integrity.

Provenance is important for preventing tampered builds and supporting SLSA-style controls.

### Q34. What is tag immutability?

Tag immutability prevents an existing image tag from being overwritten. Without it, an attacker or mistake could replace `v1.0.0` with a different image.

Registries like ECR, ACR, Artifact Registry, Harbor, and others can support policies that reduce tag tampering risk.

### Q35. What is the risk of public container images?

Public images may be outdated, malicious, abandoned, or configured insecurely. Even official images need version review and scanning.

Use trusted sources, pin versions, scan images, and consider mirroring approved images into a private registry.

### Q36. What is dependency confusion in container builds?

Dependency confusion can happen when package managers inside builds pull malicious public packages instead of internal ones. Container builds often install dependencies automatically, so they can be affected.

Controls include private registries, scoped packages, lockfiles, dependency pinning, and build network controls.

### Q37. What is a malicious base image?

A malicious base image contains backdoors, crypto miners, credential stealers, or tampered binaries. If used, every derived image inherits the risk.

Use trusted registries, official images, digests, scanning, and image signing.

### Q38. How do you handle critical CVEs in images?

Validate affected package and image, check whether it is running in production, review exploitability and fix availability, update base image or package, rebuild, rescan, redeploy, and track affected tags.

If no patch exists, apply compensating controls and create a time-bound exception.

### Q39. Why can image scans show vulnerabilities in unused packages?

Scanners detect installed vulnerable packages, not always whether the vulnerable code path is used. A package may be installed but unused at runtime.

Still, unused vulnerable packages should often be removed because they increase attack surface.

### Q40. What is reachability in vulnerability triage?

Reachability means whether vulnerable code can actually be executed in the application or environment. It helps prioritize scanner findings.

For containers, consider whether the package is present, used, exposed, and reachable from attacker-controlled input.

## Runtime Container Security

### Q41. What is privileged container?

A privileged container gets elevated permissions and broad access to host devices and kernel capabilities. It weakens container isolation.

Avoid privileged containers unless absolutely required. If needed, document, restrict, monitor, and isolate them.

### Q42. What are Linux capabilities?

Linux capabilities split root privileges into smaller permissions such as `NET_ADMIN`, `SYS_ADMIN`, and `CHOWN`. Containers can drop unnecessary capabilities.

Security best practice: drop all capabilities and add only what is needed.

### Q43. What is `CAP_SYS_ADMIN` risk?

`CAP_SYS_ADMIN` is extremely broad and often considered close to root-level power. It can enable dangerous operations and escape paths depending on configuration.

Avoid granting it to containers unless there is a strong reason.

### Q44. What is seccomp?

Seccomp restricts which system calls a process can make. Docker and Kubernetes can apply seccomp profiles to reduce kernel attack surface.

Security benefit: if a container is compromised, seccomp can block dangerous system calls.

### Q45. What is AppArmor?

AppArmor is a Linux security module that restricts program behavior using profiles. It can limit file access, capabilities, and other actions.

Containers can use AppArmor profiles as an additional runtime restriction.

### Q46. What is SELinux?

SELinux is a Linux security module that enforces mandatory access control policies. It is commonly used in some Linux distributions and container platforms.

Security benefit: it limits what processes can access, even if they are compromised.

### Q47. What is read-only root filesystem?

A read-only root filesystem prevents the container from modifying its main filesystem at runtime. Temporary writes can be directed to specific writable volumes.

Security benefit: reduces persistence and tampering after compromise.

### Q48. What is hostPath mount risk?

Mounting host paths into containers can expose host files or sockets. If sensitive paths are mounted, a compromised container may modify host files or access credentials.

In Kubernetes, restrict hostPath usage through admission policies.

### Q49. Why is mounting Docker socket dangerous?

The Docker socket can control the Docker daemon. A container with access to it may start privileged containers, mount host files, or gain host-level control.

Avoid mounting `/var/run/docker.sock` into containers unless absolutely necessary and strongly controlled.

### Q50. What is container escape?

Container escape occurs when an attacker breaks out of container isolation to access the host or other containers. It can happen through kernel vulnerabilities, runtime bugs, privileged configuration, dangerous mounts, or excessive capabilities.

Prevention includes patching hosts, avoiding privileged containers, dropping capabilities, seccomp/AppArmor, and runtime monitoring.

### Q51. What is runtime security monitoring?

Runtime security monitoring observes running containers for suspicious behavior such as shell execution, crypto mining, unexpected network connections, file changes, privilege escalation, or sensitive file access.

Tools include Falco, Sysdig, Aqua, Prisma Cloud, and Datadog Cloud Security.

### Q52. What is Falco?

Falco is an open-source runtime security tool that detects suspicious behavior using rules based on system calls and events.

Examples: shell spawned in a container, write below `/etc`, connection to suspicious IP, or access to sensitive files.

### Q53. What is container drift?

Container drift occurs when a running container changes from its original image state, such as new binaries installed, files modified, or unexpected processes started.

Immutable infrastructure aims to reduce drift. Rebuild and redeploy instead of modifying running containers manually.

### Q54. What is egress control for containers?

Egress control restricts where containers can connect outbound. It helps prevent data exfiltration, command-and-control communication, and SSRF reaching internal services.

In Kubernetes, use NetworkPolicies or service mesh controls. In cloud, use firewalls, NAT controls, and proxy allowlists.

### Q55. What is container resource limit security?

Resource limits restrict CPU and memory usage. Without limits, a container can consume host or cluster resources and cause denial of service.

Set requests and limits in orchestrators like Kubernetes.

## Secrets And Configuration

### Q56. Where should container secrets be stored?

Secrets should be stored in secret managers or orchestrator secret systems, not in images. Examples include AWS Secrets Manager, Azure Key Vault, GCP Secret Manager, HashiCorp Vault, Kubernetes Secrets with encryption, or external secrets operators.

Avoid secrets in Dockerfiles, `.env` files committed to Git, image layers, and logs.

### Q57. Are environment variables safe for secrets?

Environment variables are common but not perfect. They can leak through process inspection, crash dumps, logs, debug endpoints, or misconfigured monitoring.

For sensitive secrets, prefer runtime secret injection from a secret manager and strict access controls.

### Q58. What is the risk of `.env` files?

`.env` files often contain API keys, passwords, database URLs, and tokens. If copied into images, committed to Git, or exposed through web roots, they can cause compromise.

Use `.gitignore`, `.dockerignore`, secret scanning, and secret managers.

### Q59. How do you rotate container secrets?

Update the secret in the secret manager, redeploy or reload workloads, verify the new secret works, revoke the old secret, and monitor for failures.

Design applications to support rotation without downtime where possible.

### Q60. What is configuration drift in containers?

Configuration drift occurs when runtime configuration differs from expected configuration. It may happen through manual changes, mutable containers, or environment-specific overrides.

Use IaC, GitOps, immutable images, and policy checks to reduce drift.

## CI/CD Container Security

### Q61. Where does container scanning fit in CI/CD?

Container scanning runs after the image is built and before it is pushed or deployed. It should check vulnerabilities, secrets, and misconfigurations.

For critical findings, the pipeline may fail based on policy. The image should be rebuilt after fixes and rescanned.

### Q62. What should happen before image build?

Before image build, run SAST, SCA, secret scanning, Dockerfile linting, and IaC/Kubernetes manifest scanning. These catch issues earlier than image scanning.

Then build the image in a controlled, isolated environment.

### Q63. What should happen after image build?

After image build, scan image, generate SBOM, sign image, push to trusted registry, and deploy only if policy passes.

In mature pipelines, admission control verifies image signature and policy at deployment time.

### Q64. How do you secure build runners for container builds?

Use isolated runners, avoid privileged builds unless required, restrict network access, protect registry credentials, clean workspaces, patch runner hosts, and avoid sharing runners between trusted and untrusted workloads.

Container builds can be dangerous because build steps execute code.

### Q65. What is Docker-in-Docker risk?

Docker-in-Docker or privileged Docker builds can require elevated access. If an attacker controls build steps, they may break out or access the host/runner.

Use rootless builders, BuildKit, Kaniko, Buildah, or isolated ephemeral runners where possible.

### Q66. What is Kaniko?

Kaniko builds container images without requiring a Docker daemon. It is often used in Kubernetes-based CI environments.

Security benefit: avoids mounting Docker socket, but still requires careful credential and runner isolation.

### Q67. What is Buildah?

Buildah is a tool for building OCI container images, often without requiring Docker daemon. It supports rootless builds.

It can reduce some risks associated with privileged Docker builds.

### Q68. What is hadolint?

Hadolint is a Dockerfile linter. It checks Dockerfile best practices and can catch issues like missing version pinning, invalid syntax, and risky patterns.

It complements security scanning but does not replace vulnerability scanning.

### Q69. How do you enforce only approved images?

Use private registries, admission policies, allowlisted registries, image signing verification, tag immutability, and CI controls.

In Kubernetes, OPA Gatekeeper, Kyverno, or built-in admission mechanisms can block unapproved images.

### Q70. What is an image promotion workflow?

Image promotion means the same tested image is promoted from dev to staging to production, instead of rebuilding separately for each environment.

Security benefit: ensures the artifact deployed to production is the same artifact that passed tests and scans.

## Cloud Container Services

### Q71. What is AWS ECR security?

ECR security includes IAM access control, private repositories, image scanning, encryption, lifecycle policies, tag immutability, and integration with CI/CD and EKS/ECS.

Avoid broad pull/push access and monitor image changes.

### Q72. What is Azure Container Registry security?

ACR security includes RBAC, private endpoints, admin user disabled where possible, image scanning through Defender for Cloud, managed identities, network restrictions, and retention.

Avoid shared admin credentials and broad push permissions.

### Q73. What is Google Artifact Registry security?

Artifact Registry security includes IAM, private repositories, vulnerability scanning integration, cleanup policies, Workload Identity access, and audit logs.

Avoid service account keys for registry access where possible.

### Q74. What is ECS task role risk?

If an ECS task role is over-privileged, compromise of the application container may allow access to AWS resources. The task role should be least privilege per service.

Do not rely only on network controls; IAM blast radius matters.

### Q75. What is Cloud Run container security?

Cloud Run runs containers as a managed service. Security includes secure image, least-privilege service account, ingress restrictions, authentication, secrets, environment variables, and logging.

Managed runtime reduces host management but not application or IAM risk.

## Container Scenario Questions

### Q76. A container image contains a hardcoded AWS key. What do you do?

Treat the key as compromised. Revoke or rotate it, check CloudTrail for use, remove it from source and image build process, rebuild image without the secret, remove affected image tags if possible, and add secret scanning.

Also check whether the secret exists in image history or registry caches.

### Q77. A container runs as root in production. Is that always critical?

Not always critical by itself, but it is risky and should be fixed where possible. Severity depends on privileges, mounts, capabilities, runtime, workload sensitivity, and escape paths.

Best practice is non-root execution with restricted capabilities and read-only filesystem where possible.

### Q78. Image scan shows 200 vulnerabilities. How do you approach it?

Group by base image, OS packages, app dependencies, severity, fix availability, and running workload exposure. Often updating the base image removes many findings at once.

Prioritize critical/high exploitable vulnerabilities in internet-facing or sensitive workloads.

### Q79. A developer says image CVE is not exploitable. What do you do?

Ask for evidence and validate reachability. Check whether the vulnerable package is present, used, exposed, and whether exploit conditions apply. If truly not exploitable, document risk decision. If uncertain, patch or mitigate.

Do not dismiss solely based on opinion.

### Q80. A production container starts a shell unexpectedly. What do you do?

Investigate as suspicious runtime activity. Check runtime alerts, process tree, image, deployment history, user activity, network connections, secrets access, and logs. Isolate if needed and preserve evidence.

Prevention includes runtime monitoring, minimal images, non-root, and restricted exec access.

### Q81. A registry image tag was overwritten. What is the risk?

Deployments using that tag may pull a different image than expected. This can introduce malicious code or untested changes.

Enable tag immutability, use digests, sign images, and verify provenance.

### Q82. A Dockerfile copies the whole repository. What is the issue?

It may copy secrets, `.git` history, tests, local configs, and unnecessary files into the image. This increases image size and risk.

Use `.dockerignore` and copy only required files.

### Q83. A container has no resource limits. Why is it risky?

It can consume excessive CPU or memory and impact other workloads, causing denial of service.

Set CPU/memory requests and limits in orchestration platforms.

### Q84. A container needs to write files. Can root filesystem still be read-only?

Yes. Make the root filesystem read-only and mount specific writable directories or temporary volumes for required writes.

This limits tampering while supporting application needs.

### Q85. How do you explain container security in one answer?

Container security covers the full lifecycle: secure Dockerfile, trusted base image, dependency and secret scanning, minimal non-root image, signed artifact, private registry, least-privilege runtime, restricted capabilities, secure secrets, network controls, and runtime monitoring.

It is not one tool; it is build-time and runtime defense in depth.

