# Cloud, Tools, And Chained DevSecOps Q&A

This file focuses on questions where interviewers combine multiple skills: AppSec plus cloud, CI/CD plus secrets, Kubernetes plus IAM, scanner findings plus risk decisions, and AWS/Azure/GCP comparisons.

## Cloud Comparison Questions

### Q1. What are the AWS, Azure, and GCP equivalents for IAM?

AWS uses IAM users, groups, roles, and policies. Azure uses Microsoft Entra ID for identities and Azure RBAC for resource permissions. GCP uses Cloud IAM with principals, roles, and policies assigned at organization, folder, project, or resource level.

The common principle across all three is least privilege. Avoid permanent admin access, review permissions regularly, use roles for workloads, and monitor privileged activity.

### Q2. What are the AWS, Azure, and GCP equivalents for object storage?

AWS object storage is S3. Azure object storage is Blob Storage inside Storage Accounts. GCP object storage is Cloud Storage.

Security checks are similar: public access, IAM, encryption, logging, versioning or retention, sensitive data, and lifecycle management. The names differ, but the risk pattern is the same.

### Q3. What are the AWS, Azure, and GCP equivalents for key management?

AWS uses KMS. Azure uses Key Vault for keys, secrets, and certificates. GCP uses Cloud KMS for keys and Secret Manager for secrets.

In interviews, emphasize that encryption is not only "enabled or disabled." Key access, rotation, audit logs, and separation of duties matter.

### Q4. What are the AWS, Azure, and GCP equivalents for cloud logging?

AWS uses CloudTrail for API activity, CloudWatch for logs/metrics, and VPC Flow Logs for network metadata. Azure uses Activity Logs, Entra ID logs, Azure Monitor, and Log Analytics. GCP uses Cloud Audit Logs, Cloud Logging, and VPC Flow Logs.

Logs are critical for incident response because they answer who did what, when, from where, and against which resource.

### Q5. What are the AWS, Azure, and GCP equivalents for threat detection/posture?

AWS has GuardDuty, Security Hub, Inspector, Macie, Config, and IAM Access Analyzer. Azure has Defender for Cloud, Microsoft Sentinel, Entra ID Protection, and Azure Policy. GCP has Security Command Center, Event Threat Detection, Security Health Analytics, IAM Recommender, and Cloud Armor for edge protection.

The interview answer should connect tools to use cases: posture, threat detection, vulnerability scanning, sensitive data discovery, identity risk, and compliance.

### Q6. What are the AWS, Azure, and GCP equivalents for Kubernetes?

AWS managed Kubernetes is EKS. Azure managed Kubernetes is AKS. GCP managed Kubernetes is GKE.

Security concepts are shared: RBAC, network policies, pod security, image scanning, secrets, workload identity, cluster upgrades, audit logs, and admission control. Provider-specific differences appear in IAM integration and default security features.

### Q7. What are the AWS, Azure, and GCP equivalents for serverless functions?

AWS uses Lambda, Azure uses Azure Functions, and GCP uses Cloud Functions or Cloud Run functions depending on architecture.

Security checks include IAM permissions, event source validation, secrets, dependency scanning, logging, environment variables, network access, and timeout/resource limits.

### Q8. What are the AWS, Azure, and GCP equivalents for WAF?

AWS has AWS WAF. Azure has Azure Web Application Firewall, commonly with Application Gateway or Front Door. GCP has Cloud Armor.

WAF can reduce attack traffic and provide virtual patching, but it does not replace secure code or proper authorization.

### Q9. What are AWS Security Groups, Azure NSGs, and GCP firewall rules?

They are cloud network controls that filter traffic based on rules. AWS Security Groups are stateful and attached to resources. Azure NSGs apply to subnets or network interfaces. GCP firewall rules apply at the VPC level and target resources by tags or service accounts.

Security review checks include public admin ports, broad source ranges, unnecessary outbound access, and environment segmentation.

### Q10. How is shared responsibility different for IaaS, PaaS, and SaaS?

In IaaS, the customer manages more: OS patching, runtime, application, data, IAM, and network configuration. In PaaS, the provider manages more platform layers, but the customer still secures application, data, identity, and configuration. In SaaS, the provider manages the application, while the customer manages identities, access, configuration, data usage, and integrations.

The more managed the service, the more responsibility shifts from infrastructure hardening to configuration, identity, and data governance.

## AppSec To Cloud Chains

### Q11. How can SSRF become a cloud security issue?

SSRF can make a cloud-hosted application request internal metadata endpoints or internal services. If metadata credentials are exposed and the workload role is over-privileged, an attacker can use those credentials to call cloud APIs.

Prevention requires app-level SSRF controls, metadata protection, egress filtering, and least-privilege cloud IAM.

### Q12. How can IDOR become a cloud data exposure issue?

If an application stores user files in S3, Azure Blob, or GCS and uses predictable object references without authorization checks, users may access other users' cloud-stored objects.

The fix is not only private buckets. The application must enforce object-level authorization and generate scoped, short-lived access URLs when needed.

### Q13. How can XSS lead to cloud or DevOps risk?

XSS in an internal admin portal can steal tokens, perform privileged actions, or attack cloud management dashboards if tokens or sensitive data are exposed in the browser. XSS in CI/CD dashboards or internal tools can be especially dangerous.

Controls include output encoding, CSP, HttpOnly cookies, strong session handling, least privilege, and admin action re-authentication.

### Q14. How can SQL injection become a cloud incident?

SQL injection can expose or modify data in a cloud database like RDS, Azure SQL, or Cloud SQL. If database credentials are over-privileged, impact increases. SQLi can also expose secrets stored in tables.

Controls include parameterized queries, least-privilege database users, encryption, logging, WAF as temporary mitigation, and database activity monitoring.

### Q15. How can file upload lead to cloud compromise?

If uploaded files are stored in public buckets, sensitive files may leak. If files are processed by backend services, malicious files may exploit parsers. If uploads allow executable content in a web-accessible path, attackers may gain code execution.

Cloud controls include private storage, scoped pre-signed URLs, malware scanning, metadata stripping, encryption, logging, and strict access control.

### Q16. How can weak authentication impact cloud systems?

Weak authentication can allow attackers into internal portals, cloud consoles, CI/CD systems, or admin apps. If those systems have cloud deployment rights, an account compromise can become infrastructure compromise.

Controls include MFA, SSO, conditional access, least privilege, session security, strong recovery flows, and monitoring.

### Q17. How can insecure CORS expose cloud-hosted APIs?

If a cloud-hosted API reflects arbitrary origins and allows credentials, a malicious website may read sensitive API responses from a victim's browser. This is especially dangerous for authenticated internal APIs.

Fix with strict origin allowlists, avoiding credentialed cross-origin access unless required, and server-side authorization.

### Q18. How can missing rate limiting become a cloud cost issue?

Attackers can abuse expensive endpoints, serverless functions, image processing, search, or OTP APIs to increase cloud costs or cause service degradation.

Controls include rate limiting, quotas, request size limits, timeouts, caching, bot detection, and budget alerts.

### Q19. How can leaked logs become a security incident?

Logs may contain tokens, passwords, personal data, internal URLs, stack traces, or cloud resource identifiers. If log storage is public or broadly accessible, sensitive information can leak.

Controls include log redaction, access control, encryption, retention policies, and avoiding secrets in logs.

### Q20. How can a vulnerable dependency affect cloud workloads?

A vulnerable dependency in a cloud-hosted service can lead to remote code execution, data exposure, SSRF, or denial of service. If the workload has cloud IAM permissions, compromise may extend to cloud resources.

Controls include SCA, container scanning, least-privilege workload roles, runtime detection, and patch management.

## CI/CD Chain Questions

### Q21. How would you design a DevSecOps pipeline for a web application?

Start with branch protection and pull request review. Add secret scanning, SAST, SCA, and IaC scanning in PR. During build, generate SBOM, build container image, scan image, and sign artifacts. Deploy to staging using least-privilege credentials, run DAST/API tests, then require approval for production. After deployment, monitor logs, WAF, cloud findings, and runtime behavior.

This design combines shift-left prevention and shift-right detection.

### Q22. What security checks should run on every pull request?

Run fast checks: secret scanning, SAST, dependency scan, IaC scan, workflow/pipeline linting, unit tests, and code owner review for sensitive files. Container scanning may run after image build if the PR builds images.

Keep PR checks fast enough that developers do not bypass them. Slower scans can run nightly or in staging.

### Q23. What security checks should run nightly?

Nightly jobs can run deeper SAST, full DAST, authenticated scans, container registry scans, dependency rechecks, license checks, external attack surface scans, and cloud posture reports.

Nightly scanning helps catch new CVEs and issues that do not need to block every commit.

### Q24. What should block a production deployment?

Production should usually be blocked by leaked secrets, confirmed critical vulnerabilities, exploitable high-risk findings, public sensitive storage, unsafe infrastructure changes, unsigned artifacts if signing is required, failed approvals, and policy violations for regulated systems.

Blocking policy should be documented and risk-based.

### Q25. What should not automatically block every deployment?

Low-risk informational findings, unvalidated scanner noise, missing headers on non-sensitive internal pages, or vulnerabilities with no reachable code path may not need automatic blocking.

However, they should still be tracked and remediated according to policy.

### Q26. How do you handle secrets in GitHub Actions?

Store secrets in GitHub secrets or external secret managers, restrict environments, limit `GITHUB_TOKEN` permissions, avoid printing secrets, protect branches, prevent untrusted fork workflows from accessing secrets, and prefer OIDC federation for cloud credentials.

Rotate secrets if logs or workflow changes expose them.

### Q27. How do you handle secrets in Jenkins?

Use Jenkins Credentials plugin, credential binding, RBAC, folder-level access, masked logs, restricted script approvals, patched plugins, and isolated agents. Avoid hardcoding secrets in Jenkinsfiles or environment output.

Jenkins itself must be hardened because it often has deployment power.

### Q28. How do you handle secrets in Azure DevOps?

Use secret variables, variable groups with restricted access, Azure Key Vault integration, service connections with least privilege, environment approvals, and pipeline permissions.

Managed identities and federated credentials can reduce long-lived secrets.

### Q29. What is OIDC federation in CI/CD?

OIDC federation allows CI/CD platforms to request short-lived cloud credentials based on trusted identity claims instead of storing long-lived cloud keys. For example, GitHub Actions can assume an AWS role using OIDC.

This reduces risk from leaked static cloud keys.

### Q30. Why pin third-party actions or pipeline dependencies?

Unpinned actions or dependencies can change unexpectedly or be compromised. Pinning to a commit SHA or trusted version improves supply-chain integrity.

Combine pinning with review, least privilege, and dependency update processes.

### Q31. What is artifact provenance?

Provenance records where an artifact came from, how it was built, which source commit produced it, and which build system was used. It helps verify supply-chain integrity.

Tools and frameworks include SLSA, in-toto, Sigstore, and Cosign.

### Q32. What is SLSA?

SLSA means Supply-chain Levels for Software Artifacts. It is a framework for improving software supply-chain security through build integrity, provenance, and tamper resistance.

Interview point: SLSA helps prevent unauthorized modification of code, dependencies, and build artifacts.

### Q33. What is Cosign?

Cosign is a tool from the Sigstore project used to sign and verify container images and other artifacts. It helps ensure artifacts are authentic and have not been tampered with.

In Kubernetes, admission policies can require signed images before deployment.

### Q34. What is dependency pinning?

Dependency pinning means locking dependencies to specific versions or hashes. It prevents unexpected updates from introducing vulnerabilities or malicious code.

Use lockfiles and trusted registries, but still update regularly through controlled processes.

### Q35. How do you secure CI runners?

Use isolated runners, avoid running untrusted code with privileged access, patch runner hosts, restrict network access, clean workspaces, avoid shared secrets, limit cloud credentials, and monitor runner activity.

Self-hosted runners need special care because they may have access to internal networks.

## Tool Decision Questions

### Q36. Which SAST tool should I choose?

Choice depends on language, budget, integration, rule quality, false-positive rate, and developer workflow. SonarQube is common for code quality plus security, Semgrep is strong for custom rules, CodeQL is strong for semantic analysis, and enterprise tools like Checkmarx, Fortify, and Veracode provide broader governance features.

The best tool is one developers will actually use and security can tune.

### Q37. Which DAST tool should I choose?

OWASP ZAP is open-source and automation-friendly. Burp Suite is excellent for manual testing and has scanner capabilities in Professional/Enterprise editions. Acunetix, Invicti, and StackHawk are commercial DAST options.

DAST choice depends on authentication support, API scanning, CI integration, reporting, and false-positive handling.

### Q38. Which secret scanning tool should I choose?

Gitleaks and TruffleHog are popular open-source tools. GitGuardian and platform-native tools provide managed detection and dashboards. GitHub and GitLab have built-in secret scanning features depending on plan.

Pick tools that support pre-commit, PR, CI, and history scanning.

### Q39. Which container scanner should I choose?

Trivy is a strong open-source default because it scans images, filesystems, IaC, Kubernetes, secrets, and SBOMs. Grype is also popular. Snyk, Aqua, Prisma Cloud, and Docker Scout offer commercial or platform-integrated features.

Decision depends on CI fit, registry integration, policy control, and reporting needs.

### Q40. Which IaC scanner should I choose?

Checkov, tfsec, Terrascan, KICS, and Snyk IaC are common choices. Checkov has broad framework support and many policies. tfsec is Terraform-focused. Terrascan and KICS cover multiple IaC types.

Use one primary scanner and tune policies to organization standards.

### Q41. Which cloud security tool should I choose?

Use native tools for baseline visibility and third-party CSPM/CNAPP for broader multi-cloud coverage. Native examples: AWS Security Hub, Azure Defender for Cloud, and GCP Security Command Center. Third-party examples: Wiz, Prisma Cloud, Orca, Lacework.

For AWS/Azure/GCP environments, multi-cloud visibility is often valuable.

### Q42. What is CNAPP?

CNAPP means Cloud-Native Application Protection Platform. It combines cloud posture, workload protection, container/Kubernetes security, IaC scanning, identity risk, and runtime detection.

Examples include Wiz, Prisma Cloud, Orca, Lacework, Aqua, and Sysdig. CNAPP is broader than CSPM.

### Q43. What is CWPP?

CWPP means Cloud Workload Protection Platform. It protects workloads such as VMs, containers, Kubernetes, and serverless functions at runtime.

It focuses more on workload behavior and vulnerability than pure cloud configuration.

### Q44. What is EASM?

EASM means External Attack Surface Management. It discovers internet-facing assets such as domains, subdomains, IPs, cloud services, exposed ports, and certificates.

It helps find unknown or forgotten assets before attackers do.

### Q45. What is the difference between CSPM and EASM?

CSPM checks cloud accounts from inside using APIs and configuration data. EASM looks from outside like an attacker, discovering exposed internet-facing assets.

Both are useful. CSPM finds misconfiguration; EASM finds external exposure.

## Vulnerability Triage Chains

### Q46. Scanner reports a critical CVE in a container base image. What do you check?

Check affected package, installed version, fixed version, exploit availability, whether the package is used at runtime, whether the container is internet-facing, and whether compensating controls exist. Then update base image or package and rebuild.

If no fix exists, document temporary mitigation and exception.

### Q47. SAST reports hardcoded password in test file. How do you handle it?

First determine if it is a real secret or dummy value. If real, rotate it and remove it. If dummy, mark appropriately or adjust rules to avoid noise, but ensure dummy secrets cannot be confused with real credentials.

Even test files can leak secrets if copied from real environments.

### Q48. DAST reports XSS but only on a parameter reflected in JSON. What do you check?

Check content type, whether the JSON is rendered into HTML by frontend code, whether browser executes it, and whether the response is consumed by clients unsafely. Reflected text in JSON is not automatically exploitable XSS unless it reaches an executable browser context.

This is a good false-positive validation example.

### Q49. Dependency scan reports CVE in a dev dependency. Is it lower risk?

Often yes, but not always. Check whether the dependency is used only during development/build or included in production artifacts. Build-time compromise can still affect supply chain.

Risk depends on execution context and exposure.

### Q50. IaC scanner flags unencrypted storage in dev. Should it be fixed?

Usually yes, especially if dev contains real or sensitive data. Even if risk is lower than production, secure defaults should apply across environments to prevent drift and bad patterns.

If there is a valid exception, document it with owner and expiry.

## Incident Response Chains

### Q51. CloudTrail shows access key used from unknown country. What do you do?

Treat as possible credential compromise. Disable or rotate the key, identify associated IAM principal, review CloudTrail actions, check created users/roles/resources, look for data access or exfiltration, preserve evidence, and notify stakeholders.

Then remediate root cause and add monitoring/prevention.

### Q52. GuardDuty reports credential exfiltration. What do you do?

Validate the finding, disable affected credentials, review IAM permissions, investigate CloudTrail activity, check for persistence, inspect affected resources, and rotate related secrets. Follow incident response process.

After containment, reduce permissions and improve detection.

### Q53. Azure Defender reports public storage exposure. What do you do?

Confirm exposure, remove public access if unintended, identify exposed data, review logs and access history, notify data owners, assess compliance impact, and add Azure Policy or Defender recommendations to prevent recurrence.

For SAS token exposure, revoke or rotate affected tokens/keys.

### Q54. GCP SCC reports public bucket. What do you do?

Remove public IAM bindings such as `allUsers` or `allAuthenticatedUsers`, enable public access prevention, review audit logs, identify exposed objects, notify stakeholders, and enforce organization policies.

Then add IaC scanning and continuous monitoring.

### Q55. SIEM reports multiple failed logins followed by success. What do you do?

Investigate whether it is brute force or credential stuffing. Check user, source IPs, geography, device, MFA status, successful actions after login, and whether other accounts are affected. Reset password or revoke sessions if suspicious.

Prevent with MFA, rate limiting, breached password checks, and alert tuning.

### Q56. WAF shows SQL injection attempts. What do you do?

Check whether requests reached the application, whether any succeeded, whether vulnerable endpoints exist, and whether logs show database errors or unusual responses. WAF blocks may indicate attempted attack, not confirmed compromise.

Use findings to improve detection and test the affected endpoint safely.

### Q57. Kubernetes pod starts crypto mining. What do you do?

Isolate or stop the pod, preserve evidence, identify image and deployment source, check container runtime events, review Kubernetes audit logs, inspect service account permissions, rotate exposed secrets, and determine entry point.

Prevention includes image scanning, runtime detection, network policy, admission control, and least privilege.

### Q58. CI pipeline suddenly deploys unexpected artifact. What do you do?

Pause deployments, identify triggering commit/job/user, verify artifact provenance, review pipeline logs, check token usage, inspect repository changes, rotate compromised credentials, and rollback if needed.

Prevention includes branch protection, artifact signing, environment approvals, and least-privilege CI permissions.

### Q59. A developer laptop with cloud keys is lost. What do you do?

Revoke or rotate keys, check cloud logs for usage, verify device encryption and MDM status, revoke sessions, notify security/IT, and assess what repositories or secrets were accessible.

Prevention includes avoiding long-lived local cloud keys, using SSO, device management, and short-lived credentials.

### Q60. A production secret appears in application logs. What do you do?

Rotate the secret, restrict log access, remove or redact logs where possible according to policy, identify who accessed logs, fix logging code, and add detection for future secret logging.

Secret exposure in logs should be treated seriously because logs are often widely accessible.

## "What If" Conditional Questions

### Q61. What if a vulnerability cannot be fixed before release?

Assess severity, exploitability, exposure, and business need. Apply compensating controls, document risk acceptance with owner and expiry, get approval, monitor closely, and schedule permanent remediation.

Critical exploitable vulnerabilities in exposed systems should rarely be accepted casually.

### Q62. What if a developer says the scanner is wrong?

Validate the finding with code review, runtime context, or safe reproduction. If false positive, document and tune. If true, explain evidence and remediation.

The answer should show collaboration, not blind trust in either tool or developer.

### Q63. What if security gate blocks too many builds?

Review rule quality, thresholds, duplicate findings, false positives, and severity policy. Start by blocking only high-confidence critical issues and leaked secrets, then gradually mature.

Security gates must be trusted or teams will bypass them.

### Q64. What if production has a critical vulnerability but no patch exists?

Apply compensating controls: restrict access, disable vulnerable feature, WAF rule, increase monitoring, isolate service, or reduce privileges. Track vendor patch and create risk exception.

Also check for indicators of compromise.

### Q65. What if S3 bucket must be public for static website hosting?

Keep only intended public static content in that bucket. Separate it from private data. Use least-privilege bucket policy, no sensitive files, versioning if needed, logging, CloudFront/OAC where appropriate, and monitoring.

Public by design is different from accidental public exposure.

### Q66. What if CI needs cloud deployment permissions?

Use a dedicated least-privilege deployment role, short-lived credentials through OIDC if possible, environment restrictions, approvals, and audit logs. Avoid long-lived admin keys.

Separate dev, staging, and production permissions.

### Q67. What if DAST cannot authenticate into the app?

Configure authenticated scanning using test accounts, session scripts, tokens, or login macros. If automation is difficult, run unauthenticated scans plus manual authenticated testing.

Authenticated areas often contain the most important vulnerabilities.

### Q68. What if scanning slows down pipelines?

Run fast checks on PRs and heavier checks asynchronously, nightly, or in staging. Cache dependencies, tune rules, scan changed code where supported, and prioritize high-signal gates.

Security should be efficient enough to support delivery.

### Q69. What if cloud logs were not enabled during an incident?

State the limitation clearly, use available evidence such as application logs, billing, network logs, endpoint logs, and resource history. Then enable required logging and retention as a corrective action.

Missing logs is itself a security finding.

### Q70. What if a vulnerability is only exploitable by an admin?

Assess impact carefully. Admin-only issues may be lower risk if they require full admin access, but they can still matter for insider risk, compromised admin accounts, or privilege boundaries between admin roles.

Do not automatically dismiss it; explain context.

## Practical Mini-Answers For Rapid Fire

### Q71. SAST or DAST first?

SAST usually runs earlier because it does not need a running app. DAST runs after deployment to a test or staging environment. Use both.

### Q72. Secret scanning or SCA first?

Both should run early. Secret scanning is often a hard block because leaked secrets require rotation. SCA identifies vulnerable dependencies and may block based on severity policy.

### Q73. CSPM or IaC scanning?

IaC scanning prevents bad infrastructure before deployment. CSPM detects deployed misconfigurations and drift. Use both.

### Q74. WAF or code fix?

Code fix is the permanent remediation. WAF is defense in depth or temporary mitigation.

### Q75. MFA or least privilege?

Both. MFA protects authentication, least privilege limits impact after access is granted or compromised.

### Q76. Encryption or access control?

Both. Encryption protects data confidentiality, but access control determines who can use or decrypt data.

### Q77. Container scanning or runtime security?

Both. Scanning finds known risks before deployment. Runtime security detects suspicious behavior after deployment.

### Q78. Branch protection or code review?

Both. Branch protection enforces the process; code review applies human judgment.

### Q79. Public IP or public DNS: which matters?

Both can expose services. Public DNS points users to resources, while public IP reachability determines network exposure. Always verify actual access.

### Q80. Is DevSecOps only tools?

No. DevSecOps includes culture, process, ownership, automation, secure design, developer training, risk management, and monitoring. Tools support the process.

