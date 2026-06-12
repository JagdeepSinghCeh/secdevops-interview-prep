# Terraform And IaC Security Deep Q&A

This file prepares you for Terraform, Infrastructure as Code, and policy-as-code interview questions. IaC security is central to DevSecOps because it prevents insecure cloud resources before they are deployed.

## IaC Fundamentals

### Q1. What is Infrastructure as Code?

Infrastructure as Code means defining and managing infrastructure through code files instead of manual console changes. Examples include Terraform, CloudFormation, ARM/Bicep, Pulumi, Kubernetes YAML, and Helm.

Security benefit: infrastructure can be version-controlled, reviewed, scanned, approved, and reproduced. Risk: insecure code can deploy insecure infrastructure at scale.

### Q2. What is Terraform?

Terraform is an open-source Infrastructure as Code tool by HashiCorp. It uses configuration files to define resources across providers like AWS, Azure, GCP, Kubernetes, and many SaaS platforms.

Terraform security focuses on state protection, secrets handling, provider trust, module security, least-privilege deployment roles, code review, and IaC scanning.

### Q3. What is Terraform provider?

A provider is a plugin that lets Terraform interact with an API, such as AWS, Azure, GCP, Kubernetes, GitHub, or Cloudflare.

Security relevance: providers need credentials and permissions. Pin provider versions and use trusted sources.

### Q4. What is Terraform resource?

A resource represents an infrastructure object managed by Terraform, such as an AWS S3 bucket, Azure Storage Account, GCP firewall rule, Kubernetes deployment, or IAM role.

Security review checks whether resource configuration follows secure defaults.

### Q5. What is Terraform state?

Terraform state maps configuration to real infrastructure. It tracks resource IDs, attributes, dependencies, and sometimes sensitive values.

State is highly sensitive and must be protected with encryption, access control, locking, and secure remote backends.

### Q6. Why is Terraform state sensitive?

State can contain secrets, database passwords, access keys, IPs, resource names, and internal architecture details. Anyone who can read state may learn sensitive information or plan attacks.

Never store state in public buckets or unsecured local files for team environments.

### Q7. What is remote backend?

A remote backend stores Terraform state outside the local machine, such as S3 with DynamoDB locking, Azure Storage, GCS, or Terraform Cloud.

Security benefits include centralized access control, encryption, locking, and collaboration.

### Q8. What is state locking?

State locking prevents multiple Terraform runs from modifying state at the same time. Without locking, concurrent changes can corrupt state or cause unsafe infrastructure updates.

Use DynamoDB locking for S3 backend, Azure backend locking, GCS generation checks, or Terraform Cloud state management.

### Q9. What is Terraform plan?

`terraform plan` shows what changes Terraform will make before applying them. It compares current state, configuration, and real resources.

Security review should inspect plan output for risky changes like public access, IAM broadening, resource deletion, or logging disabled.

### Q10. What is Terraform apply?

`terraform apply` executes the planned changes and modifies infrastructure.

Production applies should use least-privilege credentials, approvals, protected branches, and pipeline controls.

## Terraform Security Risks

### Q11. What are common Terraform security issues?

Common issues include public storage buckets, open security groups, unencrypted databases, weak IAM policies, secrets in variables/state, disabled logging, public Kubernetes endpoints, unrestricted egress, and use of untrusted modules.

IaC scanning helps catch these before deployment.

### Q12. Why should secrets not be stored in Terraform files?

Terraform files are stored in Git, reviewed by many users, and may be copied into CI logs. Secrets in code are likely to leak.

Use secret managers, CI secret stores, or data sources carefully. Remember that secrets may still enter state.

### Q13. Can Terraform sensitive variables fully protect secrets?

No. Marking variables as `sensitive` hides them from some CLI output, but values may still be stored in state. Sensitive does not mean encrypted or inaccessible.

Protect state and avoid managing raw secrets through Terraform where possible.

### Q14. What is the risk of broad IAM in Terraform?

Terraform can create IAM roles and policies. If a policy uses wildcards like `Action: "*"` and `Resource: "*"`, it may grant excessive permissions.

This increases blast radius and can enable privilege escalation.

### Q15. What is the risk of `0.0.0.0/0` in Terraform?

`0.0.0.0/0` means all IPv4 addresses. If used with admin ports like SSH, RDP, databases, or internal services, it exposes resources to the internet.

Use restricted CIDRs, VPN, bastion, private endpoints, or managed access services.

### Q16. What is the risk of untrusted Terraform modules?

Modules can create resources, IAM roles, policies, and outputs. A malicious or poorly written module can deploy insecure infrastructure or expose data.

Use trusted modules, pin versions, review source code, and scan generated plans.

### Q17. Why pin Terraform module versions?

Pinning versions prevents unexpected changes when a module updates. Unpinned modules can introduce breaking changes or insecure resources.

Use semantic version constraints and review updates.

### Q18. Why pin provider versions?

Provider updates can change behavior, resource defaults, or introduce bugs. Pinning provider versions improves reproducibility.

Update providers through controlled review and testing.

### Q19. What is drift?

Drift occurs when deployed infrastructure differs from Terraform configuration, often due to manual console changes.

Drift is risky because reviewed code no longer represents reality. Detect drift with Terraform plan, cloud config tools, or CSPM.

### Q20. What is manual console change risk?

Manual changes bypass code review, security scanning, approvals, and version history. They can introduce misconfigurations and drift.

Restrict manual changes and use break-glass processes for emergencies.

## IaC Scanning Tools

### Q21. What is IaC scanning?

IaC scanning checks infrastructure code for misconfigurations, policy violations, and compliance gaps before deployment.

It is a shift-left cloud security control.

### Q22. What is Checkov?

Checkov scans Terraform, CloudFormation, Kubernetes, Helm, Dockerfile, and other IaC formats for misconfigurations and compliance issues.

It can detect public storage, open security groups, missing encryption, weak IAM, and Kubernetes workload risks.

### Q23. What is tfsec?

tfsec is a Terraform-focused security scanner. It checks Terraform code for cloud misconfigurations.

It is useful when the environment is heavily Terraform-based.

### Q24. What is Terrascan?

Terrascan scans IaC for security and compliance violations across Terraform, Kubernetes, Helm, Kustomize, Dockerfiles, and cloud templates.

It supports policy-based scanning in CI/CD.

### Q25. What is KICS?

KICS, Keeping Infrastructure as Code Secure, scans IaC for vulnerabilities, compliance issues, and misconfigurations across multiple formats.

It is another option for broad IaC scanning.

### Q26. What is Snyk IaC?

Snyk IaC scans Terraform, Kubernetes, CloudFormation, and other IaC for misconfigurations and provides remediation guidance.

It integrates well with developer workflows and Snyk's broader platform.

### Q27. What is cfn-nag?

cfn-nag scans AWS CloudFormation templates for insecure patterns like open security groups, permissive IAM, and missing encryption.

It is useful for AWS-native CloudFormation environments.

### Q28. What is CloudFormation Guard?

CloudFormation Guard is a policy-as-code tool for validating CloudFormation templates against custom rules.

Use it for organization-specific AWS guardrails.

### Q29. What is ARM/Bicep scanning?

ARM templates and Bicep define Azure infrastructure. They can be scanned by tools like Checkov, Terrascan, KICS, and Microsoft security tooling.

Common findings include public storage, weak NSGs, missing diagnostics, and insecure Key Vault settings.

### Q30. What is Kubernetes manifest scanning?

Kubernetes manifest scanning checks YAML files for insecure workload settings, such as privileged containers, missing resource limits, hostPath mounts, broad RBAC, or unsafe services.

Tools include Checkov, Trivy, Kubescape, Polaris, kube-score, and Datree.

## Policy As Code

### Q31. What is policy as code?

Policy as code means defining security, compliance, or operational rules in machine-readable code and enforcing them automatically.

Examples: deny public buckets, require encryption, block privileged pods, require tags, and restrict allowed regions.

### Q32. What is OPA?

OPA, Open Policy Agent, is a general-purpose policy engine. Policies are written in Rego and can be used with Kubernetes, CI/CD, APIs, and IaC.

OPA helps enforce consistent rules across environments.

### Q33. What is Rego?

Rego is the policy language used by OPA. It allows writing logical rules to evaluate JSON-like input.

For interviews, you do not need deep Rego syntax unless the role asks for policy engineering, but know what it is used for.

### Q34. What is Conftest?

Conftest uses OPA policies to test configuration files like Terraform plans, Kubernetes YAML, Dockerfiles, and CI configs.

It is useful in CI/CD for custom policy checks.

### Q35. What is Sentinel?

Sentinel is HashiCorp's policy-as-code framework used with Terraform Enterprise/Cloud and other HashiCorp products.

It can enforce policies before Terraform applies changes.

### Q36. What is Kyverno policy as code?

Kyverno defines Kubernetes policies as YAML resources. It can validate, mutate, generate, and verify resources.

It is Kubernetes-native and often easier for platform teams than Rego.

### Q37. What is preventive vs detective policy?

Preventive policy blocks risky changes before they happen, such as denying public buckets. Detective policy alerts after something exists, such as CSPM finding a public bucket.

Strong programs use both.

### Q38. What is policy exception?

A policy exception allows a temporary deviation from a security rule with justification, owner, expiry, and compensating controls.

Exceptions should not become permanent silent bypasses.

### Q39. How do you roll out policy as code safely?

Start in audit mode, measure impact, fix common violations, communicate with teams, then enforce high-risk policies gradually.

This prevents breaking deployments unexpectedly.

### Q40. What policies should every cloud IaC pipeline have?

Block public storage, open admin ports, disabled encryption for sensitive services, broad IAM admin policies, missing logging on critical resources, public databases, and privileged Kubernetes workloads.

Add organization-specific rules for tags, regions, and compliance.

## CI/CD For IaC

### Q41. Where does IaC scanning fit in CI/CD?

IaC scanning should run on pull requests before infrastructure is applied. It should also run on main branch and scheduled jobs.

Preventing insecure infrastructure before deployment is cheaper than fixing cloud drift later.

### Q42. What is secure Terraform pipeline flow?

Flow: format/validate, lint, IaC scan, plan, manual review/approval, apply with least-privilege credentials, store state securely, and monitor drift after deployment.

Production applies should require approval and protected branches.

### Q43. Who should approve Terraform plan?

Approvers should include infrastructure owners and sometimes security reviewers for high-risk changes. Sensitive changes include IAM, networking, public exposure, databases, logging, and encryption.

Use CODEOWNERS or branch protection to enforce reviews.

### Q44. What Terraform changes are security-sensitive?

IAM policies/roles, security groups/firewall rules, public IPs, object storage policies, KMS/key policies, database exposure, logging changes, Kubernetes RBAC, and CI/CD role permissions.

These should receive extra review.

### Q45. How do you handle high findings in IaC scan?

Validate finding, check whether resource is truly exposed or sensitive, fix code if valid, or document exception with owner and expiry. Rerun scan before merge.

Critical issues like public sensitive storage or open admin ports should block deployment.

### Q46. How do you avoid static cloud credentials for Terraform?

Use OIDC federation, managed identities, workload identity, or short-lived assumed roles. Avoid long-lived cloud keys in CI variables.

For AWS use role assumption/OIDC, Azure use managed identity or federated credentials, GCP use Workload Identity Federation.

### Q47. Why should Terraform apply not run from developer laptops for production?

Laptop applies may bypass approvals, use personal credentials, create inconsistent state, and lack auditability. Production applies should run through controlled pipelines.

Break-glass exceptions should be documented and logged.

### Q48. What is GitOps for infrastructure?

GitOps uses Git as the source of truth for infrastructure and deployments. Changes are made through pull requests and applied automatically or semi-automatically.

Security controls include branch protection, scanning, approvals, signed commits, and drift detection.

### Q49. What is plan artifact risk?

Terraform plan files can contain sensitive values and exact infrastructure changes. If stored as CI artifacts, access should be restricted.

Do not publish sensitive plan outputs broadly.

### Q50. What is Terraform destroy risk?

`terraform destroy` deletes managed infrastructure. In production, accidental destroy can cause major outage or data loss.

Protect with approvals, restricted permissions, resource deletion protection, backups, and state access control.

## Cloud-Specific IaC Security

### Q51. What AWS Terraform misconfigs are common?

Public S3 buckets, open security groups, public RDS, disabled EBS/RDS encryption, broad IAM policies, no CloudTrail, weak KMS policies, public EKS endpoints, and unrestricted Lambda permissions.

Use Checkov/tfsec and AWS Config/Security Hub for prevention and detection.

### Q52. What Azure Terraform misconfigs are common?

Public Storage Accounts, open NSGs, public SQL, missing diagnostics, weak Key Vault access, no purge protection, broad RBAC, public AKS API, and no private endpoints for sensitive services.

Use IaC scanning and Azure Policy.

### Q53. What GCP Terraform misconfigs are common?

Public Cloud Storage buckets, broad Owner/Editor roles, service account keys, open firewall rules, public GKE control plane, disabled audit logs, no public access prevention, and weak KMS IAM.

Use IaC scanning and Organization Policies.

### Q54. What Kubernetes IaC misconfigs are common?

Privileged pods, root containers, hostPath mounts, missing resource limits, default service accounts, broad RBAC, NodePort exposure, missing NetworkPolicy, and mutable image tags.

Scan manifests before deployment and enforce admission policies.

### Q55. What is public database misconfiguration?

A database is publicly reachable from the internet due to network rules, public IPs, or firewall settings. This is high risk because databases often contain sensitive data.

Restrict to private networks, app subnets, private endpoints, or approved admin paths.

### Q56. What is missing encryption misconfiguration?

Missing encryption means data at rest is not protected by service-managed or customer-managed keys where required.

Fix by enabling encryption and controlling key access. For some resources, encryption must be enabled at creation time.

### Q57. What is missing logging misconfiguration?

Missing logging means important activity cannot be audited. Examples include disabled CloudTrail, missing Azure diagnostic settings, or missing GCP Data Access logs for sensitive resources.

Logging is essential for incident response and compliance.

### Q58. What is overly permissive bucket policy?

A bucket policy is overly permissive if it grants public or broad cross-account access beyond need. Examples include AWS S3 public principal, Azure anonymous access, or GCP `allUsers`.

Fix with least privilege and public access prevention controls.

### Q59. What is region restriction policy?

Region restriction limits where resources can be created. It supports data residency, compliance, and cost/security governance.

Enforce with SCPs, Azure Policy, or GCP Organization Policy.

### Q60. What is mandatory tagging policy?

Mandatory tagging requires resources to include tags/labels like owner, environment, data classification, and cost center.

Security benefit: improves ownership, incident response, vulnerability routing, and compliance reporting.

## Scenario Questions

### Q61. Terraform plan opens port 22 to `0.0.0.0/0`. What do you do?

Block or reject the change unless there is an approved exception. Ask for business need, restrict source IPs, use VPN/bastion/SSM/IAP/Bastion, and add IaC policy to prevent recurrence.

Open admin ports to internet are high-risk.

### Q62. Terraform state is stored in a public S3 bucket. What do you do?

Immediately restrict bucket access, rotate any secrets found in state, review access logs if available, move state to secure backend with encryption/versioning/locking, and assess exposure.

State exposure can leak infrastructure details and secrets.

### Q63. A module creates IAM admin role. What do you do?

Review why admin is needed. Replace with least-privilege policy, restrict trust policy, add permission boundaries if appropriate, and scan/review module source.

Do not blindly trust modules.

### Q64. IaC scanner flags public S3 bucket for static website. Is it valid?

It may be intentionally public. Validate whether the bucket contains only public static content and no sensitive data. If public by design, document exception and use safe policy. If accidental, fix immediately.

Context matters in triage.

### Q65. A developer disables encryption to fix deployment issue. What do you do?

Reject the change or require approved exception. Investigate deployment issue and fix it without weakening encryption. If temporary exception is unavoidable, document owner, expiry, and compensating controls.

Security controls should not be silently removed for convenience.

### Q66. A Terraform plan deletes production database. What do you do?

Stop the apply. Investigate why Terraform wants deletion, check state, lifecycle settings, resource naming, and drift. Ensure backups and deletion protection. Require explicit approval for destructive changes.

Production destructive changes need strong guardrails.

### Q67. Cloud console shows resource changed outside Terraform. What do you do?

Identify who changed it, why, and whether it creates risk. Decide whether to import/update Terraform code or revert the manual change. Add controls to reduce unauthorized manual changes.

This is drift management.

### Q68. CI Terraform role has full admin. What do you do?

Reduce to required permissions, split roles by environment, use OIDC/short-lived credentials, add approval gates, and monitor usage.

CI compromise with admin cloud permissions is severe.

### Q69. A secret appears in Terraform variable file. What do you do?

Treat as exposed. Rotate secret, remove from Git/history if needed, move to secret manager, protect state, and add secret scanning.

Also check CI logs and plan artifacts.

### Q70. IaC scan has too many false positives. What do you do?

Tune rules, add context-aware exceptions with expiry, focus blocking on high-confidence critical risks, educate developers, and improve secure modules.

Do not disable scanning entirely because of noise.

## Rapid Fire

### Q71. Terraform or CloudFormation?

Terraform is multi-cloud and provider-based. CloudFormation is AWS-native. Both are IaC and need scanning, review, and state/change protection.

### Q72. Checkov or tfsec?

Checkov supports many IaC formats and broad policies. tfsec is Terraform-focused. Either can work; tuning and CI integration matter more than tool name.

### Q73. IaC scanning or CSPM?

IaC scanning prevents bad changes before deployment. CSPM detects deployed misconfigurations and drift. Use both.

### Q74. Terraform plan or scan first?

Run format/validate and static scan early. Also scan plan when possible because plan includes resolved values and module output.

### Q75. Local state or remote state?

Use remote state for team and production environments. Local state is risky for collaboration and sensitive data protection.

### Q76. Manual cloud console or IaC?

Prefer IaC for repeatability, review, audit, and security scanning. Manual changes should be limited and documented.

### Q77. Policy audit or enforce?

Start with audit to understand impact, then enforce critical policies once teams are ready.

### Q78. Secrets in Terraform or secret manager?

Use secret manager. Be careful because Terraform may still store secret values in state if it manages them directly.

### Q79. Module reuse or custom code?

Use trusted secure modules where possible. Review and pin module versions. Custom code may be needed for specific controls.

### Q80. IaC security mindset?

Prevent insecure cloud before it exists, protect state, use least-privilege deployment credentials, scan every change, enforce policy, monitor drift, and make secure modules easy for developers.

