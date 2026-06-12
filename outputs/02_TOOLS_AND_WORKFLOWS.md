# DevSecOps Tools, Skills, And Practical Workflows

This file is for tool-based interview preparation. Interviewers often ask not only "what is the tool?" but also "where will you use it?", "what does it find?", "how do you handle false positives?", and "how do you connect it to the pipeline?"

## Tool Categories

| Category | Purpose | Common Tools | Where Used |
|---|---|---|---|
| SAST | Static code analysis | SonarQube, Semgrep, CodeQL, Checkmarx, Fortify, Veracode | Pull request, CI build |
| DAST | Test running apps | OWASP ZAP, Burp Suite, Acunetix, Invicti, StackHawk | Staging, test env, scheduled scans |
| SCA | Dependency vulnerability and license scan | Snyk, OWASP Dependency-Check, Dependabot, Renovate, Mend | PR, CI, scheduled jobs |
| Secret scanning | Find leaked keys/tokens | Gitleaks, TruffleHog, GitGuardian, detect-secrets | Pre-commit, PR, CI, Git history |
| IaC scanning | Find cloud config risks before deploy | Checkov, tfsec, Terrascan, KICS, Snyk IaC | PR, Terraform plan, CI |
| Container scanning | Find image OS/app vulnerabilities | Trivy, Grype, Snyk, Docker Scout, Prisma Cloud | After image build, before push/deploy |
| Kubernetes policy | Enforce cluster rules | OPA Gatekeeper, Kyverno, Kubewarden | Admission control, CI |
| Runtime security | Detect suspicious workload behavior | Falco, Sysdig, Aqua, Prisma Cloud | Runtime production/staging |
| CSPM | Cloud posture detection | Wiz, Orca, Prisma Cloud, Lacework, AWS Security Hub, Defender for Cloud, GCP SCC | Cloud accounts/subscriptions/projects |
| SIEM | Log correlation and alerting | Splunk, Sentinel, QRadar, Elastic, Chronicle | SOC, detection, incident response |
| Secrets management | Store and rotate secrets | AWS Secrets Manager, Azure Key Vault, GCP Secret Manager, HashiCorp Vault | Apps, CI/CD, cloud workloads |
| WAF | HTTP attack filtering | AWS WAF, Azure WAF, Google Cloud Armor, Cloudflare, F5 | Edge, load balancer, CDN |

## Secure CI/CD Pipeline Blueprint

### Stage 1: Developer Workstation

Security controls:
- Pre-commit secret scanning with Gitleaks or detect-secrets.
- Local dependency checks for high-risk projects.
- Secure coding guidelines.
- Branch naming and commit hygiene.

Interview answer:
Security starts before CI. If developers catch secrets and obvious vulnerabilities locally, the team saves time and avoids accidental exposure. However, local hooks should not be the only control because developers may bypass them.

### Stage 2: Pull Request

Security controls:
- Mandatory code review.
- SAST with Semgrep, CodeQL, or SonarQube.
- SCA with Snyk, Dependabot, or OWASP Dependency-Check.
- IaC scanning with Checkov or tfsec.
- Secret scanning.
- Branch protection and CODEOWNERS.

Interview answer:
Pull request security is the main shift-left control. It gives developers fast feedback before merging. The pipeline should block critical findings and leaked secrets, while lower-risk issues can create tickets depending on policy.

### Stage 3: Build

Security controls:
- Build in isolated runners.
- Avoid privileged runners unless required.
- Use trusted base images.
- Do not print secrets in logs.
- Generate SBOM.
- Sign artifacts.

Interview answer:
The build system is part of the software supply chain. If attackers compromise CI, they can alter production artifacts. Therefore build runners, tokens, dependencies, and artifacts must be protected.

### Stage 4: Package And Image

Security controls:
- Container image scanning.
- Minimal base image.
- Non-root user.
- No secrets in image layers.
- Image signing.
- Push only to trusted registry.

Interview answer:
Container security is not only Kubernetes security. A vulnerable or secret-filled image becomes risky before it reaches the cluster.

### Stage 5: Deploy To Staging

Security controls:
- Deploy using least-privilege service account.
- DAST baseline scan.
- API security tests.
- Auth/access control tests.
- Infrastructure policy checks.

Interview answer:
DAST needs a running application, so staging is usually the right place. Avoid running intrusive scans directly against production unless approved and controlled.

### Stage 6: Production Release

Security controls:
- Manual approval for high-risk services.
- Change record.
- Signed artifact verification.
- Deployment environment protections.
- Rollback plan.

Interview answer:
Production deployment should be automated but controlled. Automation reduces human error, while approvals and gates reduce risk for sensitive systems.

### Stage 7: Runtime Monitoring

Security controls:
- Cloud logs.
- Application logs.
- WAF logs.
- SIEM alerts.
- Runtime container detection.
- Vulnerability re-scanning.

Interview answer:
Shift-right security detects what shift-left missed and what changes after deployment. Cloud drift, new CVEs, credential abuse, and runtime attacks require continuous monitoring.

## Tool-Based Interview Questions

### 1. If you have SonarQube and Semgrep, do you need both?

They overlap but are not identical. SonarQube is strong for code quality, bugs, maintainability, and security hotspots. Semgrep is strong for customizable security rules and lightweight CI integration. A company may use both if SonarQube is the main quality gate and Semgrep is used for custom AppSec rules.

The key is avoiding duplicate noisy findings. Tools should be tuned and mapped to ownership.

### 2. Can DAST replace SAST?

No. SAST and DAST find different types of issues. SAST analyzes code without running the app, so it can catch insecure patterns early. DAST tests runtime behavior, so it can catch deployment and configuration issues. SAST may not know runtime exploitability; DAST may not reach hidden code paths.

Strong DevSecOps uses both, plus SCA, secret scanning, and manual review.

### 3. What tool will you use for SSRF?

Burp Suite is commonly used for manual SSRF testing because it can intercept and modify requests. Burp Collaborator or another approved OAST service can confirm server-side callbacks. OWASP ZAP can help with automated testing but SSRF often requires manual understanding of application features.

For cloud SSRF prevention, use app allowlists, egress controls, metadata protections, and cloud IAM least privilege.

### 4. What tool will you use for cloud misconfigurations?

For code before deployment, use Checkov, tfsec, Terrascan, KICS, or Snyk IaC. For deployed cloud environments, use CSPM tools like Wiz, Orca, Prisma Cloud, Lacework, AWS Security Hub, Microsoft Defender for Cloud, or GCP Security Command Center.

The interview point is preventive plus detective control: scan IaC before deployment and monitor actual cloud after deployment.

### 5. What tool will you use for Kubernetes misconfigurations?

Use kube-bench for CIS benchmark checks, kube-hunter for cluster exposure testing in authorized environments, kubescape for posture scanning, Polaris for workload best practices, Trivy for Kubernetes YAML and cluster scanning, and Kyverno or OPA Gatekeeper for policy enforcement.

In production, admission control is stronger than only reporting because it can block unsafe deployments.

### 6. What tool will you use for secrets?

Use Gitleaks, TruffleHog, detect-secrets, GitGuardian, GitHub secret scanning, or GitLab secret detection. For storage and runtime access, use AWS Secrets Manager, Azure Key Vault, GCP Secret Manager, or HashiCorp Vault.

Finding secrets is only half the process. Rotation and incident review are required.

### 7. What tool will you use for container image scanning?

Use Trivy, Grype, Snyk Container, Docker Scout, Anchore, Prisma Cloud, or Aqua. Trivy is popular because it is open-source, fast, and easy to integrate in CI.

After scanning, prioritize critical and high findings with fixes, especially in internet-facing workloads.

### 8. What tool will you use for network scanning?

Use Nmap for authorized network discovery and port/service enumeration. For web server baseline checks, Nikto can help. For TLS, use testssl.sh or SSL Labs.

Network scanning should be scoped and approved because it can trigger alerts or affect fragile systems.

### 9. What tool will you use for dependency scanning?

Use Snyk, OWASP Dependency-Check, Dependabot, Renovate, Mend, npm audit, pip-audit, cargo audit, or osv-scanner. Choice depends on language, ecosystem, budget, and CI platform.

Good dependency management also includes upgrade strategy, lockfiles, SBOM, and ownership.

### 10. What tool will you use for AWS security monitoring?

Use CloudTrail for API audit logs, GuardDuty for threat detection, Security Hub for centralized findings, Config for compliance drift, Inspector for vulnerability scanning, Macie for sensitive data discovery, IAM Access Analyzer for access review, and CloudWatch for metrics/logs.

In interviews, do not answer with only one service. Explain the purpose of each.

### 11. What tool will you use for Azure security monitoring?

Use Microsoft Defender for Cloud for posture and workload protection, Microsoft Sentinel for SIEM, Azure Monitor and Log Analytics for logs, Entra ID logs for identity events, Key Vault logging for secret access, and NSG flow logs or network watcher for network visibility.

Azure interviews often focus on Entra ID, RBAC, managed identities, Key Vault, Defender for Cloud, and Sentinel.

### 12. What tool will you use for GCP security monitoring?

Use Cloud Audit Logs for activity, Security Command Center for posture and findings, Cloud Logging and Monitoring for telemetry, Cloud Armor for edge protection, Cloud IDS for network threat detection, IAM Recommender for access optimization, and Secret Manager audit logs.

For GKE, include audit logs, workload identity, binary authorization, and network policies.

## Website Security Review Checklist

Use this when asked: "What all do you check on any site?"

### 1. Scope And Recon

Check:
- Authorized domains and IPs.
- Application purpose.
- User roles.
- Tech stack.
- Login and registration flows.
- APIs and mobile/backend endpoints.
- Sensitive data handled.

Answer:
I start by understanding scope and functionality. Security testing without mapping the application leads to missed logic issues and noisy tool output.

### 2. Transport Security

Check:
- HTTPS enforced.
- TLS version and cipher strength.
- Certificate validity.
- HSTS.
- Mixed content.

Answer:
Transport security protects data in transit. Weak TLS or HTTP fallback can expose credentials and sensitive traffic.

### 3. Authentication

Check:
- Brute force protection.
- MFA.
- Password policy.
- Password reset.
- Username enumeration.
- Session rotation after login.
- Logout invalidation.

Answer:
Authentication is a high-risk area because account takeover has direct business impact.

### 4. Authorization

Check:
- Horizontal privilege escalation.
- Vertical privilege escalation.
- Object ownership.
- Admin APIs.
- Forced browsing.
- Method tampering.

Answer:
Authorization must be tested server-side with multiple accounts and roles. It is one of the most common serious web risks.

### 5. Input Validation

Check:
- SQL injection.
- NoSQL injection.
- Command injection.
- XSS.
- Path traversal.
- Template injection.
- XML external entity risk if XML is used.

Answer:
Inputs include parameters, JSON fields, headers, cookies, file names, and uploaded content. Every untrusted input should be validated and safely handled.

### 6. Session And Token Security

Check:
- Cookie flags.
- JWT validation.
- Token expiry.
- Refresh token handling.
- Session fixation.
- Concurrent sessions.

Answer:
Session flaws can turn small bugs into account takeover.

### 7. File Uploads

Check:
- File type allowlist.
- Size limits.
- Malware scanning.
- Storage permissions.
- Direct execution.
- Metadata.
- Access control.

Answer:
File upload features are high risk because they combine untrusted content, storage, and sometimes server-side processing.

### 8. API Security

Check:
- Broken object-level authorization.
- Broken function-level authorization.
- Mass assignment.
- Excessive data exposure.
- Rate limits.
- Schema validation.
- CORS.

Answer:
APIs must be tested directly, not only through the UI.

### 9. Security Headers

Check:
- CSP.
- HSTS.
- X-Content-Type-Options.
- X-Frame-Options or frame-ancestors.
- Referrer-Policy.
- Permissions-Policy.

Answer:
Headers are defense in depth. They reduce browser-side exploitation impact.

### 10. Error Handling And Logging

Check:
- Stack traces.
- Verbose errors.
- Sensitive data in logs.
- Audit logs for sensitive actions.
- Alerting for suspicious behavior.

Answer:
Errors should help defenders, not attackers. Logs should support investigation without exposing secrets.

## Secure Cloud Review Checklist

### AWS

Check:
- IAM users, roles, policies, access keys, MFA.
- CloudTrail enabled all regions.
- GuardDuty enabled.
- Security Hub/Config status.
- S3 public access and encryption.
- VPC security groups and NACLs.
- Public EC2 exposure.
- RDS public access and encryption.
- KMS key policies.
- Secrets Manager usage.
- EKS cluster and node role permissions.

### Azure

Check:
- Entra ID users, groups, risky sign-ins, MFA.
- RBAC assignments.
- Managed identities.
- Storage public access and SAS tokens.
- Key Vault access and logging.
- Defender for Cloud recommendations.
- NSGs and public IPs.
- Azure Activity Logs.
- AKS RBAC and network policies.

### GCP

Check:
- IAM policies and service accounts.
- Service account keys.
- Cloud Audit Logs.
- Cloud Storage public access.
- VPC firewall rules.
- Security Command Center findings.
- Secret Manager access.
- KMS key permissions.
- GKE workload identity and RBAC.

## Must-Learn Hands-On Labs

Use legal labs only:

- OWASP Juice Shop for web vulnerabilities.
- PortSwigger Web Security Academy for AppSec.
- OWASP WebGoat for secure coding concepts.
- DVWA for basic web vulnerabilities.
- Kubernetes Goat for Kubernetes security.
- CloudGoat for AWS security scenarios.
- flaws.cloud for AWS misconfiguration learning.
- Terraform + Checkov local examples.
- Docker image scan with Trivy.
- Git secret scan with Gitleaks.

## 30-Day Hands-On Minimum

By the end of 30 days, you should be able to do these without panic:

- Run a SAST scan and explain findings.
- Run dependency scan and prioritize CVEs.
- Run secret scan and explain remediation.
- Scan a Docker image and improve Dockerfile.
- Scan Terraform for public cloud exposure.
- Use Burp Repeater to test access control.
- Explain SSRF testing safely.
- Review IAM policy for over-permission.
- Explain S3/Azure Storage/GCS public exposure.
- Design a secure CI/CD pipeline.
- Explain a cloud incident response flow.

