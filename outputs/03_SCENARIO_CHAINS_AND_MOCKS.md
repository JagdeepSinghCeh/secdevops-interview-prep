# DevSecOps Scenario Chains And Mock Interviews

This file prepares you for chained interview questions. Interviewers often start simple and then go deeper: "What is SSRF?" then "How do you test it?" then "How can it affect AWS?" then "How do you prevent it in CI/CD and cloud?"

## Scenario Chain 1: SSRF In Cloud

### Q1. What is SSRF?

SSRF is Server-Side Request Forgery. It happens when an attacker can make the server send requests to attacker-controlled or internal URLs. It is dangerous because the server may access internal systems or cloud metadata services that external users cannot access.

### Q2. Where do you look for SSRF?

Look at features where the server fetches external resources: URL preview, image import by URL, webhook configuration, PDF generation, file import, XML processing, integrations, and callback URLs.

### Q3. How do you test it safely?

Use an authorized test environment and a controlled callback domain or approved OAST service. Submit the controlled URL and check whether the server makes a request. Then test whether protections block localhost, private IP ranges, cloud metadata, redirects, and unusual URL formats.

### Q4. How can SSRF impact AWS?

If a vulnerable AWS-hosted app can access instance metadata and the workload role has permissions, SSRF may expose temporary credentials. Those credentials can be used against AWS APIs. Impact depends on IAM permissions.

### Q5. How do you prevent SSRF?

Use strict allowlists, block private IP ranges, validate schemes, disable unsafe redirects, enforce egress filtering, protect metadata services, use least-privilege IAM roles, and monitor unusual outbound traffic.

### Q6. What tools help?

Burp Suite, OWASP ZAP, approved OAST/callback tools, application logs, egress logs, cloud logs, WAF logs, and cloud IAM monitoring.

## Scenario Chain 2: Secret Leaked In Git

### Q1. What happened?

A secret such as an API key, cloud access key, database password, or token was committed to source control. Even if removed later, it should be considered compromised because Git history may have been cloned or cached.

### Q2. What is your immediate action?

Rotate or revoke the secret immediately. Then check logs for usage, identify what the secret could access, and determine whether unauthorized actions occurred.

### Q3. How do you clean up?

Remove the secret from active code and history if required, but do not rely on deletion as remediation. Update the application to read secrets from a secret manager or protected CI variable.

### Q4. How do you prevent recurrence?

Use pre-commit hooks, CI secret scanning, branch protection, developer training, short-lived credentials, OIDC federation for cloud access, and secret managers.

### Q5. Which tools?

Gitleaks, TruffleHog, detect-secrets, GitGuardian, GitHub secret scanning, GitLab secret detection, AWS Secrets Manager, Azure Key Vault, GCP Secret Manager, HashiCorp Vault.

## Scenario Chain 3: Public Object Storage

### Q1. What is the risk?

Public object storage can expose sensitive files, backups, logs, customer data, source code, or secrets. Attackers frequently scan for public buckets and containers.

### Q2. How do you confirm?

Check bucket/container permissions, public access settings, IAM policies, ACLs if applicable, object-level permissions, and whether public URLs actually expose data.

### Q3. AWS fix?

Enable S3 Block Public Access, fix bucket policy, remove public ACLs, use least-privilege IAM, enable encryption, enable logging, and monitor with IAM Access Analyzer, Config, Security Hub, and Macie.

### Q4. Azure fix?

Disable anonymous blob access, restrict network access, use private endpoints, use Entra ID/RBAC where possible, limit SAS tokens, enable logging, and review Defender for Cloud findings.

### Q5. GCP fix?

Enable public access prevention, use uniform bucket-level access, remove `allUsers` and `allAuthenticatedUsers`, restrict IAM, enable audit logs, and use organization policies.

## Scenario Chain 4: CI/CD Pipeline Compromise

### Q1. Why is CI/CD high risk?

CI/CD systems often hold source code, secrets, build artifacts, deployment credentials, and production access. If compromised, attackers can alter builds, steal secrets, or deploy malicious code.

### Q2. What are common weaknesses?

Over-privileged tokens, untrusted third-party actions, secrets in logs, weak runner isolation, pull request workflows from forks, unpinned dependencies, public artifacts, and missing approvals.

### Q3. How do you secure it?

Use least-privilege tokens, protected branches, required reviews, pinned actions, isolated runners, masked secrets, short-lived cloud credentials, environment approvals, artifact signing, and audit logs.

### Q4. What tools help?

GitHub Actions security settings, GitLab protected variables, Azure DevOps service connection controls, Gitleaks, Semgrep, CodeQL, Snyk, Checkov, Trivy, Cosign, Sigstore, and SIEM integrations.

## Scenario Chain 5: Kubernetes Workload Risk

### Q1. A pod is running as privileged. Why is it risky?

Privileged containers have elevated access to the host and can bypass many container isolation boundaries. If compromised, the attacker may access host resources or other workloads.

### Q2. How do you detect it?

Review Kubernetes manifests, admission policy violations, cluster scans, and runtime alerts. Tools include kubectl, Trivy, Kubescape, Polaris, OPA Gatekeeper, and Kyverno.

### Q3. How do you fix it?

Disable privileged mode, run as non-root, drop Linux capabilities, set read-only root filesystem where possible, apply seccomp/AppArmor profiles, restrict hostPath mounts, and enforce policies through admission control.

### Q4. How does this connect to cloud security?

If the pod or node has access to cloud credentials or metadata, workload compromise can lead to cloud resource access. Least-privilege cloud IAM and workload identity reduce blast radius.

## Scenario Chain 6: Vulnerable Dependency

### Q1. SCA found a critical CVE. What do you do?

Validate whether the affected package and version are used. Check if it is direct or transitive, whether the vulnerable function is reachable, whether exploit exists, and whether a fixed version is available.

### Q2. How do you remediate?

Upgrade the dependency, patch the package, remove unused dependency, change vulnerable configuration, or apply temporary mitigation. Then rerun tests and scans.

### Q3. What if no patch exists?

Use compensating controls such as disabling vulnerable functionality, network restrictions, WAF rules, runtime monitoring, or vendor workaround. Create a risk exception with owner and expiry.

### Q4. How do you prevent this?

Use lockfiles, Dependabot/Renovate, SCA in CI, SBOM, dependency ownership, approved registries, and regular patch cycles.

## Scenario Chain 7: Access Control Bug

### Q1. What is the issue?

A user can access data or actions outside their permission. This may be horizontal escalation, vertical escalation, or object-level authorization failure.

### Q2. How do you test?

Create multiple users and roles, capture requests, change object IDs or roles, replay requests with different sessions, and verify server-side authorization.

### Q3. Why do scanners miss this?

Access control often depends on business logic, roles, workflows, and object ownership. Automated scanners may not understand valid vs invalid access for each user.

### Q4. How do you fix?

Enforce server-side authorization, centralize access checks, validate ownership on every sensitive object, deny by default, add authorization tests, and log suspicious access failures.

## Scenario Chain 8: IaC Misconfiguration

### Q1. Terraform creates a security group with SSH open to the internet. What is the risk?

SSH open to `0.0.0.0/0` increases brute-force and exploitation risk, especially if instances use weak credentials or exposed keys. Admin access should be restricted.

### Q2. Where should this be caught?

During pull request review and CI IaC scanning before Terraform apply. It can also be detected later by CSPM or cloud-native config tools.

### Q3. What is the fix?

Restrict SSH to approved IP ranges, use VPN/bastion/SSM, enforce MFA for admin access, disable password login, and prefer private access patterns.

### Q4. Which tools?

Checkov, tfsec, Terrascan, KICS, AWS Config, Security Hub, Azure Defender for Cloud, GCP Security Command Center, Wiz, Prisma Cloud.

## Mock Interview 1: Fundamentals

### Q1. Tell me about yourself for a DevSecOps role.

Sample answer:
I have a foundation in Linux, networking, and Git, and I am focusing on DevSecOps across AppSec and CloudSec. I understand how security fits into the SDLC through secure design, code review, SAST, DAST, dependency scanning, secret scanning, IaC scanning, container scanning, and runtime monitoring. I am building hands-on skills with tools like Burp Suite, OWASP ZAP, SonarQube, Semgrep, Snyk, Trivy, Checkov, and cloud-native services across AWS, Azure, and GCP. My goal is to help teams deliver faster while reducing security risk through automation, least privilege, and clear remediation processes.

### Q2. What is DevSecOps?

DevSecOps integrates security into DevOps and the SDLC. It makes security continuous through automation, developer feedback, pipeline checks, cloud posture monitoring, and runtime detection.

### Q3. Why should security be automated?

Automation makes security repeatable, fast, and scalable. Manual review alone cannot keep up with frequent releases. Automated checks catch common issues early, while humans focus on design, business logic, prioritization, and complex risk decisions.

### Q4. What is the difference between SAST, DAST, and SCA?

SAST analyzes code without running the app. DAST tests a running app from the outside. SCA checks third-party dependencies for known vulnerabilities and licenses. They complement each other.

### Q5. What is your approach to a new application security review?

Understand scope, users, architecture, data flows, authentication, roles, APIs, and sensitive data. Then map attack surface, run baseline tools, manually test high-risk flows, validate findings, prioritize by risk, and report with clear remediation.

## Mock Interview 2: Tools

### Q1. Which tools have you used or would use in DevSecOps?

For SAST: SonarQube, Semgrep, CodeQL. For DAST: Burp Suite, OWASP ZAP. For SCA: Snyk, Dependency-Check, Dependabot. For secrets: Gitleaks, TruffleHog. For containers: Trivy, Grype. For IaC: Checkov, tfsec. For cloud: Security Hub, Defender for Cloud, Security Command Center. For policy: OPA, Kyverno. For SIEM: Splunk or Sentinel.

### Q2. How do you reduce scanner noise?

Tune rules, set severity thresholds, mark validated false positives with justification, suppress only with owner and expiry where possible, map findings to code owners, and focus blocking gates on high-confidence high-risk issues.

### Q3. How do you integrate Trivy in CI?

Run Trivy after building the container image. Configure it to scan OS packages, language dependencies, secrets, and misconfigurations. Fail the build for critical/high vulnerabilities based on policy, publish reports, and create remediation tickets.

### Q4. How do you use Burp Suite?

Configure browser proxy, capture requests, study parameters and flows, use Repeater for manual testing, Intruder for controlled fuzzing where allowed, Decoder for encoding, Comparer for response differences, and Scanner if licensed and authorized.

## Mock Interview 3: Cloud

### Q1. Compare AWS IAM, Azure RBAC, and GCP IAM.

All control access to cloud resources, but their models differ. AWS uses IAM users, groups, roles, and policies. Azure uses Entra ID identities and Azure RBAC role assignments over scopes like management group, subscription, resource group, and resource. GCP uses IAM policies binding principals to roles at organization, folder, project, or resource levels.

The common principle is least privilege and regular access review.

### Q2. What logs matter in AWS?

CloudTrail for API activity, CloudWatch for service/application logs and metrics, VPC Flow Logs for network traffic metadata, GuardDuty findings for threat detection, S3 access logs or CloudTrail data events for bucket activity, and EKS audit logs for Kubernetes activity.

### Q3. How do you secure cloud keys?

Avoid long-lived keys where possible. Use roles, managed identities, workload identity, OIDC federation, secret managers, rotation, least privilege, MFA for human users, and monitoring for unusual use.

### Q4. What is CSPM?

CSPM continuously checks deployed cloud environments for misconfigurations, compliance gaps, exposed resources, and risky IAM. It complements IaC scanning because it detects drift and manual changes after deployment.

## Mock Interview 4: Deep Chained

### Q1. Your SAST, SCA, and container scans all show findings. How do you prioritize?

Prioritize confirmed critical/high issues affecting internet-facing or sensitive systems, especially with known exploits or reachable vulnerable code. Consider whether the issue is in application code, dependency, OS package, or configuration. Check fix availability and business impact. Create clear remediation tickets with owners and SLAs.

### Q2. A developer asks why security blocks release for a finding.

Explain the risk in business terms, show evidence, explain likely impact, offer remediation guidance, and identify whether temporary mitigation is possible. If the risk is severe and exploitable, blocking is justified. If not, follow exception process.

### Q3. Production has a suspected data exposure from public storage. What is your response?

Contain by removing public access, preserve evidence, identify exposed data, check access logs, determine time window and access sources, notify stakeholders, assess legal/compliance obligations, rotate exposed secrets if any, and implement preventive guardrails.

### Q4. You are asked to design DevSecOps for a company from scratch. What will you implement first?

Start with asset and repo inventory, branch protection, secret scanning, SAST/SCA in CI, container and IaC scanning if applicable, cloud logging, IAM review, vulnerability management workflow, and developer remediation guidance. Then mature into threat modeling, policy as code, SBOM, artifact signing, runtime detection, and metrics.

## Final Interview Tips

- Always ask for scope and authorization before testing.
- Explain tools by category, not as random names.
- For every vulnerability, know example, impact, test method, and fix.
- For every cloud issue, mention IAM, logging, encryption, network exposure, and monitoring.
- For every pipeline question, mention least privilege, secrets, gates, artifacts, and approvals.
- For every incident question, answer with contain, investigate, eradicate, recover, prevent.
- Never claim a scanner finding is final proof without validation.
- Never say WAF is a complete fix for insecure code.
- Always connect technical risk to business impact.

