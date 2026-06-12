# Kubernetes Security Deep Q&A

This file prepares you for Kubernetes security interview questions. Kubernetes security is a major DevSecOps topic because it connects containers, cloud IAM, networking, secrets, CI/CD, runtime controls, and policy enforcement.

## Kubernetes Fundamentals

### Q1. What is Kubernetes?

Kubernetes is an orchestration platform for deploying, scaling, networking, and managing containers. It provides objects like pods, deployments, services, ingress, configmaps, secrets, namespaces, and service accounts.

Security in Kubernetes includes cluster access, RBAC, workload isolation, network policies, pod security, image security, secrets, admission control, audit logs, and cloud IAM integration.

### Q2. What is a pod?

A pod is the smallest deployable unit in Kubernetes. It can contain one or more containers that share network namespace and storage volumes.

Security relevance: pod configuration defines container privileges, service account, volumes, secrets, resource limits, and runtime restrictions.

### Q3. What is a deployment?

A deployment manages replicas of pods and handles rolling updates and rollbacks. It ensures the desired number of pods are running.

Security relevance: deployment manifests should specify secure images, non-root users, resource limits, probes, and restricted security contexts.

### Q4. What is a service?

A service provides stable network access to pods. Types include ClusterIP, NodePort, LoadBalancer, and ExternalName.

Security relevance: service type affects exposure. LoadBalancer and NodePort can expose applications outside the cluster if not controlled.

### Q5. What is ingress?

Ingress manages external HTTP/S access to services, often through an ingress controller. It can handle routing, TLS, and host/path rules.

Security checks include TLS, WAF integration, authentication, allowed hosts, annotation risks, and exposure of internal services.

### Q6. What is a namespace?

A namespace is a logical partition inside a Kubernetes cluster. It helps organize resources and apply access controls, quotas, and policies.

Namespaces are not strong security boundaries by themselves. Use RBAC, network policies, and admission policies for isolation.

### Q7. What is a ConfigMap?

A ConfigMap stores non-sensitive configuration data. Applications can consume it as environment variables, files, or command-line arguments.

Security point: do not store secrets in ConfigMaps because they are not intended for sensitive data.

### Q8. What is a Kubernetes Secret?

A Kubernetes Secret stores sensitive values like passwords, tokens, and certificates. By default, values are base64-encoded, not securely encrypted unless encryption at rest is enabled.

Restrict RBAC access, enable encryption at rest, avoid exposing secrets in env dumps, and consider external secret managers.

### Q9. What is a service account?

A Kubernetes service account is an identity used by pods to communicate with the Kubernetes API. It can be bound to permissions through RBAC.

Security relevance: over-privileged service accounts can allow a compromised pod to control cluster resources.

### Q10. What is the Kubernetes API server?

The API server is the central control-plane component that handles Kubernetes API requests. All administrative actions go through it.

Security controls include authentication, authorization, admission control, audit logging, TLS, and network restrictions.

## Kubernetes RBAC

### Q11. What is Kubernetes RBAC?

RBAC means Role-Based Access Control. It controls which users, groups, or service accounts can perform actions on Kubernetes resources.

RBAC objects include Role, ClusterRole, RoleBinding, and ClusterRoleBinding.

### Q12. What is a Role?

A Role grants permissions within a namespace. Example: allowing read access to pods in the `dev` namespace.

Use Roles for namespace-scoped access whenever possible.

### Q13. What is a ClusterRole?

A ClusterRole grants permissions cluster-wide or defines permissions for cluster-scoped resources. It can also be bound within a namespace using RoleBinding.

ClusterRoles are powerful and should be reviewed carefully.

### Q14. What is RoleBinding?

A RoleBinding assigns a Role or ClusterRole to users, groups, or service accounts within a namespace.

It connects permissions to identities.

### Q15. What is ClusterRoleBinding?

A ClusterRoleBinding assigns a ClusterRole at cluster scope. It can give access across all namespaces.

Security risk: accidental cluster-admin bindings can expose the whole cluster.

### Q16. What is cluster-admin?

`cluster-admin` is a highly privileged ClusterRole with full control over the cluster.

Only a small number of trusted admins should have it, preferably through just-in-time access and audit logging.

### Q17. How do you review Kubernetes RBAC?

Check subjects, roles, verbs, resources, namespaces, wildcards, cluster-wide bindings, service accounts, and privilege escalation permissions.

Tools like kubectl, rbac-lookup, rbac-tool, kubescape, and commercial CNAPP tools can help.

### Q18. What are risky Kubernetes verbs?

Risky verbs include `create`, `update`, `patch`, `delete`, `deletecollection`, `impersonate`, `bind`, `escalate`, and broad wildcard `*`.

Permissions over secrets, pods/exec, roles, rolebindings, and clusterrolebindings are especially sensitive.

### Q19. Why is access to Kubernetes Secrets sensitive?

Anyone who can read secrets may access application credentials, tokens, certificates, or cloud credentials. In many clusters, secrets can unlock databases, APIs, and cloud services.

Limit secret access by namespace and workload need.

### Q20. Why is `pods/exec` permission sensitive?

`pods/exec` allows executing commands inside running containers. It can expose secrets, modify runtime state, or abuse application context.

Restrict exec access and audit its use.

### Q21. What is privilege escalation through RBAC?

If a user can create roles or bind powerful roles, they may grant themselves more permissions. Kubernetes has special checks for `escalate` and `bind`, but broad permissions can still be risky.

Review who can manage RBAC objects.

### Q22. How do you apply least privilege in Kubernetes?

Create dedicated service accounts per workload, grant only required verbs/resources in required namespaces, avoid cluster-wide permissions, avoid default service account use, and review permissions regularly.

Use policy tools to prevent over-privileged manifests.

## Pod And Workload Security

### Q23. What is securityContext?

`securityContext` defines security settings for pods or containers, such as user ID, privilege, capabilities, read-only filesystem, seccomp profile, and privilege escalation.

It is one of the most important workload hardening areas.

### Q24. What is privileged pod?

A privileged pod runs containers with elevated host-level privileges. It can access host devices and bypass many isolation controls.

Privileged pods should be blocked unless strictly required for infrastructure components.

### Q25. What is `allowPrivilegeEscalation`?

`allowPrivilegeEscalation` controls whether a process can gain more privileges than its parent. It should usually be set to false.

This reduces risk from setuid binaries and privilege escalation paths.

### Q26. What is `runAsNonRoot`?

`runAsNonRoot` ensures the container does not run as root. It should be used with images that support non-root execution.

This reduces impact if the container is compromised.

### Q27. What is `readOnlyRootFilesystem`?

It makes the container root filesystem read-only. Applications can use specific writable volumes for temporary files.

This reduces tampering and persistence.

### Q28. What are Linux capabilities in Kubernetes?

Capabilities are granular Linux privileges. Kubernetes lets you drop or add capabilities in container security context.

Best practice is to drop all and add only required capabilities.

### Q29. What is seccomp in Kubernetes?

Seccomp restricts system calls available to containers. Kubernetes can use `RuntimeDefault` or custom profiles.

Use seccomp to reduce kernel attack surface.

### Q30. What are Pod Security Standards?

Pod Security Standards define security levels for pods: privileged, baseline, and restricted. They replace older PodSecurityPolicy concepts.

Restricted is the strongest common baseline for general workloads.

### Q31. What happened to PodSecurityPolicy?

PodSecurityPolicy was deprecated and removed in Kubernetes. It was replaced by Pod Security Admission and external admission tools like OPA Gatekeeper and Kyverno.

Interview point: know that modern clusters use Pod Security Standards/Admission or policy engines.

### Q32. What is Pod Security Admission?

Pod Security Admission is a built-in admission controller that enforces Pod Security Standards at namespace level.

It can enforce, audit, or warn for privileged, baseline, or restricted profiles.

### Q33. What are resource requests and limits?

Requests reserve resources for scheduling. Limits cap resource usage. They apply to CPU and memory.

Security relevance: without limits, one workload can exhaust cluster resources and cause denial of service.

### Q34. What are liveness and readiness probes?

Liveness probes check whether a container should be restarted. Readiness probes check whether it should receive traffic.

Security/availability relevance: proper probes reduce downtime and prevent broken pods from receiving traffic.

### Q35. What is hostNetwork risk?

`hostNetwork` makes a pod use the host's network namespace. It can expose host network interfaces and bypass some network isolation.

Avoid unless required for infrastructure components.

### Q36. What is hostPID risk?

`hostPID` allows a pod to see host processes. This can expose sensitive process information and support attacks.

Avoid in application workloads.

### Q37. What is hostPath risk?

`hostPath` mounts host filesystem paths into pods. It can expose or modify host files and lead to node compromise.

Restrict with admission policies and avoid for normal apps.

### Q38. What is default service account risk?

Pods use the default service account if no service account is specified. If it has permissions or token auto-mounting, workloads may get unnecessary API access.

Create dedicated service accounts and disable token automount where not needed.

### Q39. What is automountServiceAccountToken?

This setting controls whether Kubernetes automatically mounts a service account token into a pod.

Set it to false for workloads that do not need Kubernetes API access.

### Q40. What is imagePullPolicy?

`imagePullPolicy` controls when Kubernetes pulls images. Values include Always, IfNotPresent, and Never.

Security relevance: use immutable tags or digests. Do not rely on mutable tags alone.

## Kubernetes Network Security

### Q41. What is Kubernetes networking model?

Kubernetes gives each pod an IP and allows pod-to-pod communication inside the cluster. By default, many clusters allow broad communication unless network policies are enforced.

Security requires segmentation through NetworkPolicies or service mesh policies.

### Q42. What is NetworkPolicy?

NetworkPolicy controls ingress and egress traffic for pods based on labels, namespaces, IP blocks, and ports.

It helps limit lateral movement. It requires a compatible CNI plugin to enforce policies.

### Q43. Is NetworkPolicy enforced by default?

No. NetworkPolicy only works if the cluster CNI supports it, and policies must be defined. Also, if no policy selects a pod, traffic may remain allowed by default.

Implement default-deny policies and allow only required traffic.

### Q44. What is default-deny NetworkPolicy?

A default-deny policy blocks all traffic for selected pods unless explicitly allowed by other policies.

It is a strong starting point for namespace segmentation.

### Q45. What is ingress traffic in Kubernetes?

Ingress traffic is traffic entering pods or services. It can come from other pods, namespaces, cluster components, or external users through ingress/load balancer.

Control it with NetworkPolicies, ingress controllers, service configuration, and cloud firewalls.

### Q46. What is egress traffic in Kubernetes?

Egress traffic leaves pods toward other services, databases, internet, or metadata endpoints.

Egress control helps reduce data exfiltration and SSRF impact.

### Q47. What is service mesh security?

A service mesh like Istio, Linkerd, or Consul can provide mTLS, traffic policy, observability, and service-to-service authorization.

It is useful but adds complexity and does not replace application authorization.

### Q48. What is mTLS?

mTLS means mutual TLS. Both client and server authenticate each other using certificates.

In Kubernetes, mTLS can secure service-to-service communication inside the cluster.

### Q49. What is an ingress controller risk?

Ingress controllers can be exposed to the internet and may support powerful annotations or custom configuration. Misconfiguration can expose internal services or weaken TLS.

Keep controllers patched, restrict annotations, and review ingress resources.

### Q50. What is Kubernetes metadata service risk in cloud?

Pods running on cloud nodes may access cloud metadata endpoints if network controls allow it. This can expose node role credentials or metadata.

Use workload identity, metadata protections, network policies, and least-privilege node roles.

## Secrets And Config

### Q51. Are Kubernetes Secrets encrypted?

By default, Kubernetes Secrets are base64-encoded. Encryption at rest must be configured for stronger protection in etcd.

Also restrict RBAC access because anyone who can read secrets can decode them.

### Q52. What is etcd?

etcd is the key-value store used by Kubernetes to store cluster state, including secrets and configuration.

Protect etcd with encryption, TLS, backups security, access control, and restricted network access.

### Q53. What is external secrets operator?

External secrets operators sync secrets from external secret managers like AWS Secrets Manager, Azure Key Vault, GCP Secret Manager, or HashiCorp Vault into Kubernetes.

They help centralize secret lifecycle while supporting Kubernetes workloads.

### Q54. What is Sealed Secrets?

Sealed Secrets encrypt Kubernetes secrets so they can be safely stored in Git and decrypted only by the controller in the cluster.

It is useful for GitOps but requires protecting the sealing keys.

### Q55. What is HashiCorp Vault in Kubernetes?

Vault can inject secrets into pods, provide dynamic credentials, and centralize secret management. It can integrate through agents, sidecars, CSI drivers, or operators.

Security benefit: reduces static secrets and supports rotation.

### Q56. Why should secrets not be stored in ConfigMaps?

ConfigMaps are not designed for sensitive data and are often more broadly accessible. Secrets should use Kubernetes Secrets with encryption or external secret managers.

Even Kubernetes Secrets need RBAC restrictions.

### Q57. What is secret rotation in Kubernetes?

Secret rotation updates credentials and ensures pods use new values. Some applications need restart to pick up updated environment variable secrets, while mounted secrets can update depending on configuration.

Design apps and deployment processes to support rotation.

### Q58. What is the risk of environment variable secrets in pods?

Environment variables may appear in process dumps, debug outputs, crash logs, or accidental logging. They can also require pod restart for updates.

Mounted secrets or external secret injection may be safer depending on context.

## Admission Control And Policy

### Q59. What is admission control?

Admission control intercepts Kubernetes API requests before objects are persisted. It can validate or mutate resources.

Security use cases include blocking privileged pods, enforcing labels, requiring resource limits, restricting registries, and requiring signed images.

### Q60. What is OPA Gatekeeper?

OPA Gatekeeper is a Kubernetes admission controller using Open Policy Agent. It enforces policies written as constraints and templates.

Example policies: deny privileged containers, require labels, restrict hostPath, require non-root.

### Q61. What is Kyverno?

Kyverno is a Kubernetes policy engine designed for Kubernetes-native policy definitions in YAML. It can validate, mutate, generate, and verify images.

It is often easier for Kubernetes teams because policies look like Kubernetes resources.

### Q62. OPA Gatekeeper vs Kyverno?

OPA Gatekeeper uses Rego and is powerful for general policy logic. Kyverno uses Kubernetes-style YAML and is often simpler for Kubernetes-specific policies.

Both can enforce admission policies; choice depends on team skill and policy needs.

### Q63. What policies should be enforced in Kubernetes?

Enforce non-root containers, no privileged mode, no hostPath unless approved, dropped capabilities, resource limits, approved registries, image signatures, required labels, no default namespace for apps, and restricted service account token automount.

Policies should start in audit mode before enforcement if impact is unknown.

### Q64. What is image signature verification in Kubernetes?

Admission control can verify that container images are signed by trusted keys or identities before allowing deployment.

Tools include Kyverno, Connaisseur, Ratify, Sigstore Policy Controller, and cloud-specific options.

### Q65. What is Binary Authorization?

Binary Authorization is a GCP service that enforces deploy-time policies requiring trusted container images for GKE and related environments.

It helps prevent untrusted images from running.

## Kubernetes Logging And Detection

### Q66. What are Kubernetes audit logs?

Kubernetes audit logs record API server requests, including who made the request, what action was attempted, and what resource was affected.

They are essential for investigating RBAC abuse, secret access, pod exec, and suspicious deployments.

### Q67. What Kubernetes events should be monitored?

Monitor creation of privileged pods, exec into pods, secret reads, RBAC changes, service account token usage, suspicious image deployments, admission denials, failed auth, and changes to network policies.

These events may indicate compromise or risky changes.

### Q68. What is Falco in Kubernetes?

Falco detects suspicious runtime behavior in containers and Kubernetes environments. It can alert on shell execution, sensitive file reads, unexpected network connections, or privilege escalation.

It complements admission control by detecting runtime behavior.

### Q69. What is kube-bench?

kube-bench checks Kubernetes clusters against CIS Kubernetes Benchmark controls. It helps identify insecure cluster configuration.

It is useful for compliance and baseline hardening.

### Q70. What is kube-hunter?

kube-hunter looks for security weaknesses in Kubernetes clusters from an attacker perspective. It should only be used with authorization.

It can help identify exposed services and misconfigurations.

### Q71. What is Kubescape?

Kubescape is a Kubernetes security posture tool that scans clusters, manifests, and Helm charts for misconfigurations and compliance gaps.

It can help with DevSecOps and cluster posture visibility.

### Q72. What is Polaris?

Polaris checks Kubernetes workload best practices such as resource limits, probes, security context, and image tags.

It helps improve workload hygiene.

### Q73. What is Trivy for Kubernetes?

Trivy can scan Kubernetes manifests and sometimes clusters for vulnerabilities, secrets, and misconfigurations.

It is useful because the same tool can scan images, IaC, and Kubernetes configs.

## Managed Kubernetes Cloud Security

### Q74. What is EKS security focus?

EKS security includes AWS IAM integration, IRSA or Pod Identity, Kubernetes RBAC, audit logs, private endpoints, security groups, node roles, image scanning in ECR, and network policies.

EKS security requires both AWS and Kubernetes knowledge.

### Q75. What is AKS security focus?

AKS security includes Entra ID integration, Kubernetes RBAC, Azure RBAC where used, managed identities, private clusters, network policies, Defender for Cloud, Key Vault integration, and ACR security.

Identity and network controls are major AKS interview topics.

### Q76. What is GKE security focus?

GKE security includes Workload Identity, Kubernetes RBAC, private clusters, network policies, Binary Authorization, shielded nodes, Cloud Logging, SCC findings, and Artifact Registry security.

GKE often emphasizes workload identity and organization policy.

### Q77. What is private Kubernetes cluster?

A private cluster restricts public access to nodes and sometimes control plane endpoints depending on provider configuration.

Security benefit: reduces internet exposure of cluster components.

### Q78. What is node security?

Node security includes patched OS, minimal access, restricted SSH, container runtime hardening, kubelet protection, IAM/node role least privilege, and monitoring.

Compromised nodes can affect many pods.

### Q79. What is kubelet security?

Kubelet runs on each node and manages pods. If exposed or misconfigured, attackers may execute commands, view logs, or access pod data.

Restrict kubelet access, use authentication/authorization, and avoid exposing kubelet ports publicly.

### Q80. What is cluster upgrade security?

Kubernetes and managed cluster versions receive security patches. Running unsupported versions increases risk.

Maintain upgrade plans and test upgrades in non-production first.

## Kubernetes CI/CD And GitOps

### Q81. What is GitOps?

GitOps manages infrastructure and deployments through Git as the source of truth. Tools like Argo CD and Flux sync desired state to clusters.

Security focus: repo protection, signed commits, secret management, RBAC, drift detection, and admission policy.

### Q82. What is Argo CD security?

Argo CD security includes SSO/RBAC, project restrictions, repository credentials, cluster credentials, application permissions, sync policies, and avoiding overly broad admin access.

Compromise of Argo CD can become cluster compromise.

### Q83. What is Helm security?

Helm packages Kubernetes manifests as charts. Security risks include untrusted charts, insecure default values, templated secrets, privileged workloads, and outdated dependencies.

Scan Helm charts with tools like Checkov, Trivy, Kubescape, or kube-score.

### Q84. What should a Kubernetes deployment pipeline check?

Check image vulnerabilities, image signature, Kubernetes manifest misconfigurations, secrets, RBAC, network policies, resource limits, security context, admission policies, and environment approvals.

Deploy only trusted images from approved registries.

### Q85. What is manifest scanning?

Manifest scanning checks Kubernetes YAML for insecure settings before deployment. It detects privileged pods, missing resource limits, hostPath mounts, public services, and weak security context.

Tools include Checkov, Trivy, Kubescape, Polaris, kube-score, and Datree.

## Kubernetes Scenario Questions

### Q86. A pod has `privileged: true`. What do you do?

Validate why it is needed. If not required, remove it and apply restricted security context. If required for infrastructure, isolate it, limit permissions, document exception, and monitor closely.

Add admission policy to prevent future privileged pods.

### Q87. A service account has cluster-admin. What do you do?

Identify workload using it, review required permissions, create least-privilege Role/ClusterRole, update binding, and monitor for breakage. Check audit logs for past usage.

Service accounts should rarely have cluster-admin.

### Q88. A Kubernetes Secret is committed to Git. What do you do?

Treat it as compromised. Rotate the secret, remove it from Git/history if required, update workloads, and use Sealed Secrets or external secrets for future GitOps workflows.

Also add secret scanning.

### Q89. A NodePort service exposes internal app. What do you do?

Check whether exposure is intended. If not, change service type to ClusterIP, restrict network access, review cloud firewall/load balancer rules, and check access logs if available.

Prevent with policy and review.

### Q90. A pod can talk to every namespace. Is that normal?

Many clusters allow this by default, but it is not ideal for sensitive environments. Implement default-deny NetworkPolicies and allow only required traffic.

Segmentation reduces lateral movement.

### Q91. An attacker gets pod shell. What can they do?

Impact depends on pod permissions. They may read environment secrets, mounted secrets, service account token, app files, network internal services, cloud metadata, or Kubernetes API if token has permissions.

Controls include non-root, minimal image, network policy, token automount disablement, least-privilege service account, and runtime detection.

### Q92. Kubernetes audit logs show many secret reads. What do you do?

Identify principal, namespace, source, and timing. Determine whether reads are expected. If suspicious, revoke permissions, rotate affected secrets, and investigate related activity.

Secret read access is sensitive.

### Q93. A pod uses image `latest`. Why is it risky?

`latest` is mutable and can change unexpectedly. This reduces reproducibility and can deploy untested or malicious images.

Use immutable tags or digests, tag immutability, and image signing.

### Q94. A deployment has no resource limits. Why does security care?

Without limits, a workload can consume node resources and affect other services, causing denial of service.

Resource limits are availability and multi-tenant safety controls.

### Q95. How do you summarize Kubernetes security?

Kubernetes security means securing cluster access, RBAC, service accounts, pod security context, secrets, network policies, image trust, admission control, audit logs, node security, and cloud IAM integration.

The strongest answer connects build-time checks, deploy-time policy, and runtime monitoring.

