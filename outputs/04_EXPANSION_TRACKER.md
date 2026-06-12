# DevSecOps Q&A Expansion Tracker

This file tracks how to grow the prep vault from the current foundation into a very large interview bank. Use it as the table of contents for future expansion.

## Current Pack Status

Completed:
- 30-day roadmap.
- Skill map.
- 160 detailed master Q&A.
- DevSecOps tool matrix.
- Website security review workflow.
- Cloud review checklist for AWS, Azure, GCP.
- CI/CD security workflow.
- Scenario chains.
- Mock interview answers.

## Recommended Expansion Order

### Batch 1: Absolute Basics - 200 More Questions

Topics:
- What is software?
- What is server?
- What is client?
- What is browser?
- What is DNS?
- What is IP address?
- What is TCP?
- What is UDP?
- What is HTTP?
- What is HTTPS?
- What is TLS?
- What is API?
- What is REST?
- What is JSON?
- What is Linux service?
- What is process?
- What is port?
- What is firewall?
- What is proxy?
- What is reverse proxy?

Why:
Interviewers often start shallow to check whether fundamentals are real. Strong basics make deep answers easier.

### Batch 2: OWASP Top 10 - 300 More Questions

Topics:
- Broken access control deep dive.
- Cryptographic failures.
- Injection variants.
- Insecure design.
- Security misconfiguration.
- Vulnerable and outdated components.
- Identification and authentication failures.
- Software and data integrity failures.
- Logging and monitoring failures.
- SSRF.
- OWASP API Security Top 10.
- OWASP ASVS checklist questions.

Question styles:
- Definition questions.
- Testing questions.
- Fix questions.
- Tool questions.
- Real-world scenario questions.
- "What if developer disagrees?" questions.

### Batch 3: Burp Suite And Web Testing - 300 More Questions

Topics:
- Proxy.
- Repeater.
- Intruder.
- Sequencer.
- Decoder.
- Comparer.
- Collaborator.
- Logger.
- Extensions.
- Authenticated scanning.
- Session handling.
- Access control testing.
- API testing.
- SSRF testing.
- XSS testing.
- SQLi testing.
- File upload testing.

### Batch 4: CI/CD Security - 300 More Questions

Topics:
- Jenkins.
- GitHub Actions.
- GitLab CI.
- Azure DevOps.
- Build runners.
- Secrets in pipelines.
- Third-party actions.
- Artifact signing.
- SBOM.
- SLSA.
- Deployment gates.
- Release approvals.
- Rollback.
- Environment protection.

### Batch 5: SAST, DAST, SCA, Secrets - 400 More Questions

Topics:
- SonarQube.
- Semgrep.
- CodeQL.
- Checkmarx.
- Fortify.
- Veracode.
- OWASP ZAP.
- Burp Scanner.
- Snyk.
- Dependency-Check.
- Dependabot.
- Renovate.
- Gitleaks.
- TruffleHog.
- GitGuardian.
- False positives.
- False negatives.
- Security quality gates.

### Batch 6: AWS Security - 500 More Questions

Topics:
- IAM.
- S3.
- EC2.
- VPC.
- Security Groups.
- NACLs.
- CloudTrail.
- CloudWatch.
- GuardDuty.
- Security Hub.
- Inspector.
- Macie.
- KMS.
- Secrets Manager.
- EKS.
- Lambda.
- RDS.
- WAF.
- Organizations.
- SCPs.
- IAM Access Analyzer.
- Config.

### Batch 7: Azure Security - 400 More Questions

Topics:
- Entra ID.
- RBAC.
- Managed identities.
- Key Vault.
- Storage accounts.
- VNets.
- NSGs.
- Defender for Cloud.
- Sentinel.
- Azure Monitor.
- Log Analytics.
- AKS.
- App Service.
- Azure DevOps security.

### Batch 8: GCP Security - 400 More Questions

Topics:
- Cloud IAM.
- Service accounts.
- Cloud Storage.
- VPC firewall rules.
- Cloud Audit Logs.
- Security Command Center.
- Secret Manager.
- Cloud KMS.
- GKE.
- Cloud Armor.
- Artifact Registry.
- Cloud Build.
- Organization policies.

### Batch 9: Containers And Kubernetes - 500 More Questions

Topics:
- Dockerfile security.
- Image scanning.
- Registry security.
- Distroless images.
- Rootless containers.
- Kubernetes RBAC.
- NetworkPolicy.
- Pod Security Standards.
- Admission control.
- OPA Gatekeeper.
- Kyverno.
- Secrets.
- Service accounts.
- Helm security.
- Runtime detection with Falco.
- EKS, AKS, GKE differences.

### Batch 10: Terraform And IaC - 300 More Questions

Topics:
- Terraform basics.
- State security.
- Remote backends.
- Secrets in state.
- Checkov.
- tfsec.
- Terrascan.
- KICS.
- OPA/Conftest.
- Sentinel.
- Drift.
- Secure module usage.
- Pull request reviews for IaC.

### Batch 11: Vulnerability Management - 250 More Questions

Topics:
- CVE.
- CWE.
- CVSS.
- EPSS.
- KEV catalog.
- SLAs.
- Risk acceptance.
- Exceptions.
- Compensating controls.
- Patch management.
- Reporting.
- Retesting.
- Metrics.

### Batch 12: Compliance And Governance - 250 More Questions

Topics:
- SOC 2.
- ISO 27001.
- PCI DSS.
- HIPAA basics.
- CIS benchmarks.
- NIST basics.
- Audit evidence.
- Control ownership.
- Policy exceptions.
- Data classification.

### Batch 13: Incident Response - 300 More Questions

Topics:
- Cloud credential compromise.
- Public bucket exposure.
- Container compromise.
- Pipeline compromise.
- Leaked secret.
- Suspicious IAM activity.
- Web app attack.
- WAF alert triage.
- Log investigation.
- Containment and recovery.

### Batch 14: Scenario Chains - 500 More Questions

Topics:
- SSRF to metadata to IAM.
- XSS to session theft.
- IDOR to data breach.
- SQLi to data exfiltration.
- Secret leak to cloud compromise.
- Vulnerable dependency to production incident.
- Public registry image tampering.
- Kubernetes pod to cloud IAM escalation.
- Terraform misconfig to public exposure.

## Interview Practice Method

For each question, practice answering in this format:

1. Definition.
2. Why it matters.
3. Example.
4. How to test or detect.
5. How to fix.
6. Tools.
7. Common mistake.

## Daily Revision Split

Use this daily:

- 30 minutes: revise old Q&A.
- 60 minutes: learn new theory.
- 60 minutes: hands-on lab.
- 30 minutes: speak answers out loud.
- 15 minutes: write weak topics.

## Weak Topic Log

Use this section manually while studying:

| Date | Weak Topic | What Confused Me | Next Action |
|---|---|---|---|
| | | | |

