# GCP Security Deep Q&A

This file focuses on Google Cloud Platform security interview preparation for DevSecOps, CloudSec, and AppSec roles. GCP interviewers often test IAM, service accounts, Cloud Storage, VPC firewall rules, audit logs, Security Command Center, GKE, and organization policies.

## GCP Fundamentals

### Q1. What is GCP from a security point of view?

GCP is Google's cloud platform for compute, storage, networking, data, Kubernetes, serverless, security, and DevOps. From a security perspective, GCP heavily uses resource hierarchy and IAM bindings across organization, folders, projects, and resources.

GCP security focuses on IAM, service accounts, audit logs, VPC firewall rules, Cloud Storage access, Cloud KMS, Secret Manager, Security Command Center, GKE security, organization policies, and workload identity.

### Q2. What is GCP shared responsibility?

GCP shared responsibility means Google secures the cloud infrastructure, while customers secure their data, identities, access policies, applications, configurations, and workloads. Google also describes a shared fate model where provider and customer work together to improve outcomes.

Responsibility depends on service type. Compute Engine requires more customer OS hardening than managed services like Cloud Run or BigQuery, but customers always manage IAM and data access.

### Q3. What is GCP resource hierarchy?

GCP resource hierarchy includes Organization, Folders, Projects, and Resources. IAM policies can be inherited down the hierarchy.

Security relevance: broad permissions at organization or folder level can affect many projects. Use least privilege and review inheritance carefully.

### Q4. What is a GCP project?

A project is a core GCP boundary for resources, IAM, billing, APIs, and quotas. Workloads are commonly separated into different projects by environment or application.

Security benefit: project separation reduces blast radius and simplifies ownership.

### Q5. What is an organization policy?

Organization Policy Service lets administrators enforce constraints across GCP resources. Examples include restricting public IPs, disabling service account key creation, restricting domains, or enforcing uniform bucket-level access.

It is a strong governance control for preventing misconfiguration.

### Q6. What is folder in GCP?

Folders group projects under an organization and can represent departments, environments, or business units. IAM and policies can be applied at folder level.

Security relevance: folder-level permissions and policies affect all child projects.

### Q7. What are labels in GCP?

Labels are key-value metadata applied to resources. They help with ownership, cost allocation, environment identification, and security prioritization.

Security teams use labels to identify critical assets and assign remediation owners.

### Q8. What is GCP region and zone?

A region is a geographic area, and a zone is an isolated location within a region. Deploying across zones improves availability.

Security relevance includes availability, data residency, compliance, and disaster recovery.

### Q9. What is VPC Service Controls?

VPC Service Controls create security perimeters around supported Google Cloud services to reduce data exfiltration risk. They are commonly used for sensitive data services.

Interview point: VPC Service Controls are powerful but require careful design and testing because they can affect workflows.

### Q10. What is Assured Workloads?

Assured Workloads helps configure Google Cloud environments for specific compliance and data residency requirements.

Mention it when discussing regulated workloads and governance.

## GCP IAM And Service Accounts

### Q11. What is GCP IAM?

GCP IAM controls who can do what on which resources. It uses principals, roles, policies, and resource hierarchy.

Principals include Google accounts, groups, service accounts, domains, and workload identities. Roles can be basic, predefined, or custom.

### Q12. What are GCP basic roles?

Basic roles are Owner, Editor, and Viewer. They are broad and apply many permissions.

Security best practice is to avoid Owner/Editor where possible and use predefined or custom least-privilege roles.

### Q13. What are predefined roles?

Predefined roles are Google-managed roles for specific services or job functions, such as Storage Object Viewer or Compute Admin.

They are usually safer than basic roles but may still be broader than needed.

### Q14. What are custom roles?

Custom roles contain a specific set of permissions created by the organization. They help implement least privilege when predefined roles are too broad.

They require maintenance as services evolve.

### Q15. What is a service account in GCP?

A service account is a non-human identity used by applications, VMs, Cloud Run, GKE, CI/CD, or automation to access GCP resources.

Service accounts are central to GCP security. Over-privileged service accounts are a major cloud risk.

### Q16. What are service account keys?

Service account keys are long-lived credentials that can be downloaded and used outside GCP. They are risky because if leaked, attackers can authenticate as the service account.

Best practice is to avoid user-managed keys where possible and use Workload Identity Federation or attached service accounts.

### Q17. What is Workload Identity Federation?

Workload Identity Federation lets external workloads access GCP resources using short-lived credentials from trusted identity providers instead of service account keys.

It is useful for CI/CD platforms and multicloud workloads because it reduces static key exposure.

### Q18. What is GKE Workload Identity?

GKE Workload Identity allows Kubernetes service accounts to act as GCP service accounts without storing keys in pods.

Security benefit: scoped workload identity and reduced secret exposure.

### Q19. What is IAM Recommender?

IAM Recommender suggests permission reductions based on observed usage. It helps identify over-privileged accounts and improve least privilege.

Use recommendations carefully and validate business needs before removing permissions.

### Q20. What is Policy Troubleshooter?

Policy Troubleshooter helps understand why a principal has or does not have access to a resource.

It is useful during IAM debugging and access review.

### Q21. What are common GCP IAM misconfigurations?

Common issues include broad Owner/Editor roles, service account key usage, over-privileged service accounts, public access to resources, domain-wide delegation risks, broad folder/org-level permissions, and lack of audit logging.

Prevent with organization policies, least privilege, IAM Recommender, and access reviews.

### Q22. What is domain-wide delegation risk?

Domain-wide delegation allows a service account to impersonate users in a Google Workspace domain if configured. It is powerful and risky if misused.

Restrict it tightly and monitor usage.

### Q23. What is service account impersonation?

Service account impersonation allows a principal to act as a service account and obtain short-lived credentials. It is safer than downloading keys if controlled properly.

Monitor who has `iam.serviceAccountTokenCreator` or similar permissions.

### Q24. How do you secure GCP CI/CD access?

Use Workload Identity Federation instead of service account keys. Grant least-privilege roles to deployment service accounts, restrict repository/branch claims where possible, protect secrets, and monitor audit logs.

Avoid storing JSON keys in GitHub Actions, GitLab, or Jenkins.

### Q25. What is the risk of default service accounts?

Default service accounts may have broad permissions depending on legacy settings and project configuration. If workloads use default service accounts, compromise can lead to broad cloud access.

Use custom least-privilege service accounts and disable automatic broad grants where possible.

## GCP Logging And Detection

### Q26. What are Cloud Audit Logs?

Cloud Audit Logs record administrative activity, data access, system events, and policy denied events for GCP resources.

They are essential for answering who did what, when, and against which resource.

### Q27. What are Admin Activity audit logs?

Admin Activity logs record administrative operations that modify resource configuration or metadata. They are enabled by default for many services.

Security use case: detecting IAM changes, resource creation, firewall changes, and service account key creation.

### Q28. What are Data Access audit logs?

Data Access logs record reads and writes of user data and metadata for supported services. They can be high volume and may need explicit enablement.

Enable them for sensitive resources where investigation requires object or data access visibility.

### Q29. What are Policy Denied logs?

Policy Denied logs record when access is denied due to security policies such as organization policy or IAM denial.

They help detect blocked attempts and policy impact.

### Q30. What is Cloud Logging?

Cloud Logging stores, searches, analyzes, and exports logs from GCP resources and applications.

Security best practices include centralized logging, retention, protected log sinks, and alerting.

### Q31. What is Cloud Monitoring?

Cloud Monitoring collects metrics, events, and uptime information. It supports alerting and dashboards.

Security teams may use it for unusual activity, availability issues, and workload health.

### Q32. What is Security Command Center?

Security Command Center is Google Cloud's centralized security and risk management platform. It helps identify misconfigurations, vulnerabilities, threats, exposed resources, and compliance risks depending on tier and configuration.

It is the primary GCP-native security posture and finding management service.

### Q33. What is Event Threat Detection?

Event Threat Detection analyzes logs to identify suspicious activity such as malware, IAM abuse, crypto mining, data exfiltration patterns, or brute-force behavior depending on detections available.

It feeds findings into Security Command Center.

### Q34. What is Security Health Analytics?

Security Health Analytics checks GCP resources for misconfigurations and compliance issues. Findings appear in Security Command Center.

Examples include public buckets, weak IAM, open firewall rules, and logging gaps.

### Q35. What is Cloud Asset Inventory?

Cloud Asset Inventory provides inventory and history of GCP assets and IAM policies. It helps with visibility, compliance, and incident response.

Security teams use it to find exposed resources, track changes, and support investigations.

### Q36. What is a log sink?

A log sink exports logs from Cloud Logging to destinations such as BigQuery, Cloud Storage, or Pub/Sub.

Security use case: centralize logs in a protected project and route them to SIEM.

### Q37. What GCP events should be monitored?

Monitor service account key creation, IAM policy changes, firewall rule changes, public bucket access, organization policy changes, audit log configuration changes, project creation/deletion, and suspicious service account usage.

These events can indicate compromise or misconfiguration.

### Q38. What do you do if audit logs are missing?

State investigation limitations, enable necessary logs, review available logs from applications, network, CI/CD, and billing, and add logging requirements as corrective action.

Missing logs should be treated as a control gap.

## GCP Network Security

### Q39. What is GCP VPC?

GCP VPC is a global virtual network containing regional subnets. Unlike some clouds, a GCP VPC can span regions.

Security design includes firewall rules, routes, private access, segmentation, Shared VPC, and VPC Service Controls.

### Q40. What are GCP firewall rules?

GCP firewall rules control ingress and egress traffic. They can target instances by network tags or service accounts.

Common risk: allowing `0.0.0.0/0` to SSH, RDP, databases, or admin services.

### Q41. What is implied firewall behavior in GCP?

GCP has implied rules that allow egress and deny ingress unless overridden. Custom rules define allowed or denied traffic.

Security teams should explicitly control ingress and review broad egress where needed.

### Q42. What is Shared VPC?

Shared VPC allows multiple service projects to use a centrally managed VPC in a host project.

Security benefit: centralized network control while allowing teams to deploy workloads in separate projects.

### Q43. What is Private Google Access?

Private Google Access lets VM instances without external IPs access Google APIs and services using internal IP paths.

Security benefit: workloads can reach Google services without public internet exposure.

### Q44. What is Cloud NAT?

Cloud NAT lets resources without external IPs initiate outbound internet connections. It does not allow inbound internet connections.

Security point: NAT reduces inbound exposure but outbound access still needs monitoring and controls.

### Q45. What is Cloud Armor?

Cloud Armor provides WAF and DDoS protection for applications behind supported Google Cloud load balancers.

It can block common web attacks, malicious IPs, and rate-based abuse, but does not replace secure application code.

### Q46. What is Identity-Aware Proxy?

Identity-Aware Proxy, or IAP, controls access to applications and VMs based on user identity and context. It can provide secure access without exposing services broadly.

Use it to protect internal apps and admin access.

### Q47. How do you secure SSH in GCP?

Avoid public SSH where possible. Use IAP TCP forwarding, OS Login, restricted firewall rules, no external IPs, strong IAM, and audit logs.

Public SSH should be tightly restricted and monitored.

### Q48. What is OS Login?

OS Login manages Linux SSH access using IAM identities instead of metadata SSH keys. It improves centralized access control and auditability.

Use OS Login with IAM and MFA policies where possible.

### Q49. What is Cloud IDS?

Cloud IDS is an intrusion detection service that inspects network traffic for threats.

It is a detective network control and can complement firewall rules and logging.

### Q50. How do you reduce public exposure in GCP?

Use private subnets/no external IPs, restricted firewall rules, IAP, Private Google Access, Cloud NAT, Cloud Armor, VPC Service Controls, and organization policies.

Also continuously monitor with Security Command Center and asset inventory.

## GCP Storage And Secrets

### Q51. What is Cloud Storage?

Cloud Storage is GCP object storage. It stores objects in buckets and is used for files, logs, backups, data lakes, and application assets.

Security focus: public access, IAM, uniform bucket-level access, public access prevention, encryption, retention, and audit logs.

### Q52. How do you secure Cloud Storage buckets?

Enable public access prevention, use uniform bucket-level access, avoid `allUsers` and `allAuthenticatedUsers`, apply least-privilege IAM, enable audit logs for sensitive buckets, use encryption, and configure retention/versioning where needed.

Do not mix public and sensitive data in the same bucket.

### Q53. What is uniform bucket-level access?

Uniform bucket-level access disables object ACLs and uses IAM policies for access control. It simplifies and centralizes bucket permissions.

It is recommended for consistent access management.

### Q54. What is public access prevention?

Public access prevention blocks public access to buckets and objects through IAM or ACLs.

It helps prevent accidental public exposure.

### Q55. What are signed URLs in GCP?

Signed URLs provide temporary access to Cloud Storage objects without making the bucket public.

Security best practices include short expiry, least privilege, HTTPS, and authorization checks before generating URLs.

### Q56. What is Cloud KMS?

Cloud KMS manages cryptographic keys used for encryption and signing. It supports key rings, keys, versions, IAM, rotation, and audit logs.

Security review focuses on who can use and administer keys.

### Q57. What is Secret Manager?

Secret Manager stores secrets such as API keys, passwords, certificates, and tokens. It supports versioning, IAM access, replication policies, and audit logs.

Use it instead of storing secrets in code, container images, CI variables, or VM metadata.

### Q58. What is CMEK?

CMEK means Customer-Managed Encryption Key. It lets customers control the keys used to encrypt supported GCP resources through Cloud KMS.

Security benefit: stronger control and separation of duties, but key availability and permissions must be managed carefully.

### Q59. What is retention policy in Cloud Storage?

A retention policy prevents objects from being deleted or modified for a defined period. It supports compliance and ransomware resilience.

Use carefully because locked retention policies can be difficult to change.

### Q60. What is Bucket Lock?

Bucket Lock permanently locks a retention policy on a bucket. It helps meet compliance requirements for immutable storage.

It should be tested carefully before use because it is irreversible for the retention period.

## GCP Compute, Containers, And DevOps

### Q61. What is Compute Engine security baseline?

Baseline includes no unnecessary external IPs, restricted firewall rules, OS Login, patched images, shielded VM where appropriate, least-privilege service accounts, logging/monitoring, disk encryption, and vulnerability management.

Avoid using default service accounts with broad permissions.

### Q62. What is Shielded VM?

Shielded VM provides protections like secure boot, virtual trusted platform module, and integrity monitoring.

It helps protect against boot-level and rootkit-style attacks.

### Q63. What is Cloud Run security?

Cloud Run security includes least-privilege service account, ingress restrictions, authentication, secret manager integration, container image scanning, environment variable hygiene, and logging.

Cloud Run abstracts infrastructure but still needs secure application and IAM configuration.

### Q64. What is Cloud Functions security?

Cloud Functions security includes secure triggers, least-privilege service accounts, input validation, dependency scanning, secrets management, timeout/resource limits, and audit logging.

Serverless functions can be abused if event sources or IAM are misconfigured.

### Q65. What is GKE security?

GKE security includes Kubernetes RBAC, Workload Identity, network policies, private clusters, shielded nodes, image scanning, Binary Authorization, secrets management, audit logs, and regular upgrades.

GKE combines Kubernetes and GCP IAM/network security.

### Q66. What is Binary Authorization?

Binary Authorization enforces deploy-time policies for container images in GKE and related environments. It can require images to be signed or attested before deployment.

It helps protect software supply chain integrity.

### Q67. What is Artifact Registry?

Artifact Registry stores container images and language packages. It replaces or complements older Container Registry patterns.

Security checks include IAM, vulnerability scanning, private repositories, cleanup policies, and provenance/signing.

### Q68. What is Cloud Build?

Cloud Build is GCP CI/CD build service. It can build, test, scan, and deploy artifacts.

Security focus includes service account permissions, build triggers, secret access, private pools, artifact provenance, and avoiding broad deployment roles.

### Q69. What is Source Repositories?

Cloud Source Repositories was GCP's managed Git repository service, but many teams use GitHub, GitLab, or other systems. For interviews, focus on source control security principles rather than this service specifically.

Key controls are branch protection, reviews, secret scanning, and CI/CD permissions.

### Q70. How do you secure GCP deployment from GitHub Actions?

Use Workload Identity Federation to avoid JSON service account keys. Restrict trust to specific repository, branch, and workflow. Grant least-privilege roles to deployment service account and monitor audit logs.

Also protect GitHub branches and environments.

## GCP Incident Scenarios

### Q71. A service account key is leaked. What do you do?

Disable or delete the key, rotate credentials, review audit logs for service account usage, check IAM permissions, identify accessed resources, and look for persistence.

Then migrate to Workload Identity Federation or attached service accounts and block key creation where possible.

### Q72. A Cloud Storage bucket is public. What do you do?

Remove public access, enable public access prevention, use uniform bucket-level access, identify exposed data, review audit logs, notify owners, and add organization policy or SCC monitoring.

Assess compliance impact if sensitive data was exposed.

### Q73. A firewall rule allows SSH from `0.0.0.0/0`. What do you do?

Restrict or remove the rule, use IAP/OS Login, check logs for suspicious SSH access, identify who created the rule, and add policy or monitoring to prevent recurrence.

Public SSH is a common cloud exposure.

### Q74. SCC reports over-privileged service account. What do you do?

Review permissions and actual usage, reduce roles using IAM Recommender where appropriate, replace broad roles with least-privilege roles, and monitor service account activity.

Do not remove permissions blindly without understanding workload impact.

### Q75. Audit logs show service account key creation. What do you do?

Check whether key creation was approved. If not, disable key, investigate creator and usage, and enforce organization policy to disable key creation where possible.

Service account keys are high-risk long-lived credentials.

### Q76. GKE workload accesses resources it should not. What do you do?

Review Kubernetes service account, GCP service account binding, Workload Identity mapping, IAM roles, pod configuration, and audit logs. Reduce permissions and rotate exposed secrets if needed.

Workload-level least privilege is critical.

### Q77. Cloud Run service is publicly invokable. Is that bad?

It depends. Public websites and APIs may need unauthenticated access. Internal services should require authentication and restricted ingress.

Always evaluate intended exposure, data sensitivity, and authorization inside the app.

### Q78. A project has many users with Editor role. What do you do?

Review who needs access, replace Editor with predefined or custom least-privilege roles, use groups, implement access reviews, and monitor privileged actions.

Basic roles are usually too broad for production.

### Q79. Secret Manager secret accessed unexpectedly. What do you do?

Review audit logs, identify principal and source, check IAM bindings, rotate the secret if suspicious, and investigate downstream systems that used the secret.

Secret access should be rare and explainable.

### Q80. Cloud Build service account has project Owner. What do you do?

Reduce permissions to required deployment roles, use separate service accounts per pipeline/environment, restrict triggers, and monitor builds.

CI/CD service accounts are high-value targets.

## GCP Rapid Fire

### Q81. Project or folder?

A project is a resource and billing boundary. A folder groups projects and can inherit IAM and policies.

### Q82. Basic role or predefined role?

Prefer predefined or custom roles. Basic roles like Owner and Editor are broad and risky.

### Q83. Service account key or Workload Identity Federation?

Prefer Workload Identity Federation or attached service accounts. Service account keys are long-lived and risky.

### Q84. Cloud Audit Logs or Security Command Center?

Cloud Audit Logs provide raw activity records. Security Command Center centralizes findings, misconfigurations, vulnerabilities, and threats.

### Q85. Cloud Armor or firewall rules?

Cloud Armor protects HTTP/S applications at the edge. Firewall rules control network traffic to resources. Use both for different layers.

### Q86. Secret Manager or Cloud KMS?

Secret Manager stores secrets. Cloud KMS manages encryption keys. They are related but solve different problems.

### Q87. Public access prevention or IAM review?

Use both. Public access prevention blocks public exposure; IAM review ensures least privilege for non-public access.

### Q88. GKE RBAC or GCP IAM?

Both matter. GKE RBAC controls Kubernetes API permissions; GCP IAM controls Google Cloud resource access. Workload Identity connects the two.

### Q89. Private Google Access or Cloud NAT?

Private Google Access lets private VMs access Google APIs. Cloud NAT lets private VMs access the internet outbound. They solve different connectivity needs.

### Q90. GCP security mindset?

Use resource hierarchy carefully, enforce least privilege, avoid service account keys, restrict public exposure, centralize logs, protect storage, use organization policies, monitor with SCC, and secure GKE/workloads.

