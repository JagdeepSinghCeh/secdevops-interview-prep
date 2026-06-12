# DevSecOps Interview Preparation Vault

Target: 2-year DevSecOps / AppSec / CloudSec role  
Cloud scope: AWS, Azure, GCP  
Starting point: basic Linux, networking, Git  
Timeline: 30 days  
Format: Markdown, detailed Q&A, tool matrix, testing workflows, scenario chains

## How To Use This Pack

This pack is designed to take you from zero DevSecOps confidence to interview-ready thinking. Do not try to memorize everything blindly. Read a section, write the answer in your own words, then practice explaining it out loud in 60 to 90 seconds.

For every topic, learn four layers:

1. Definition: what it is.
2. Why it matters: risk, business impact, security value.
3. How it is implemented: tools, process, checks, controls.
4. Interview scenario: what you would do in a real pipeline, cloud account, app, or incident.

## 30-Day Study Plan

### Days 1-3: Absolute Fundamentals

Goals:
- Understand DevOps, DevSecOps, CI/CD, cloud, AWS/Azure/GCP basics.
- Learn SDLC, threat modeling, vulnerability, exploit, risk, control, policy, compliance.
- Become comfortable explaining security to both technical and non-technical interviewers.

Study:
- What is DevSecOps?
- What is cloud?
- What is shared responsibility?
- What is CI/CD?
- What is AppSec vs CloudSec vs DevSecOps?
- What are vulnerabilities, threats, risks, CVEs, CWEs, CVSS?

Practice:
- Explain DevSecOps in one minute.
- Explain why security should shift left.
- Explain the difference between vulnerability, exploit, threat, and risk.

### Days 4-6: Linux, Networking, Git For Security

Goals:
- Build operational comfort with systems and network basics.
- Understand ports, protocols, DNS, HTTP, TLS, firewalls, IAM, logs.
- Explain how attackers and defenders look at systems.

Study:
- Linux permissions, users, groups, services, processes, logs.
- TCP/IP, ports, DNS, HTTP methods, status codes, headers, TLS.
- Git security: branch protection, signed commits, secrets in repositories.

Practice:
- Read HTTP request/response examples.
- Explain what happens when a browser opens a website.
- Explain how a leaked API key in Git should be handled.

### Days 7-10: Application Security Basics

Goals:
- Master OWASP Top 10 concepts.
- Learn secure coding review mindset.
- Understand how to test common web vulnerabilities in authorized environments.

Study:
- Broken access control, injection, SSRF, XSS, auth flaws, crypto failures.
- Input validation, output encoding, authentication, authorization.
- Sessions, cookies, JWT, CORS, CSRF, rate limiting.

Practice:
- Explain each OWASP Top 10 risk with example, impact, test idea, and fix.
- Create a checklist for testing a login page, upload feature, API endpoint, and admin panel.

### Days 11-13: Security Testing Tools

Goals:
- Know what tools do, when to use them, and how to explain results.
- Understand SAST, DAST, SCA, secret scanning, IaC scanning, container scanning.

Study:
- Burp Suite, OWASP ZAP, Nmap, Nikto, Nuclei.
- SonarQube, Semgrep, CodeQL.
- Snyk, OWASP Dependency-Check, Trivy, Grype.
- Gitleaks, TruffleHog.
- Checkov, tfsec, Terrascan.

Practice:
- Explain the difference between SAST and DAST.
- Explain why a scanner finding is not automatically a confirmed vulnerability.
- Explain how you triage false positives.

### Days 14-16: CI/CD And Pipeline Security

Goals:
- Understand Jenkins, GitHub Actions, GitLab CI, Azure DevOps.
- Know where security checks fit in the pipeline.
- Learn pipeline secrets, approvals, artifact signing, SBOM, release gates.

Study:
- Pipeline stages: build, test, scan, package, deploy.
- Secret handling, least privilege, branch protection.
- Build artifacts, image signing, provenance, dependency pinning.

Practice:
- Design a secure CI/CD pipeline for a web app.
- Explain what happens when SAST fails.
- Explain how to prevent secrets from leaking in CI logs.

### Days 17-19: Containers And Kubernetes

Goals:
- Understand Docker, images, registries, runtime risk, Kubernetes basics.
- Know container scanning, image hardening, admission control, network policies.

Study:
- Dockerfile best practices, non-root user, minimal base image.
- Kubernetes pods, deployments, services, ingress, configmaps, secrets.
- RBAC, service accounts, pod security, network policy.

Practice:
- Explain how you secure a Docker image.
- Explain Kubernetes RBAC.
- Explain what you check before deploying a container to production.

### Days 20-22: Cloud Security Across AWS, Azure, GCP

Goals:
- Understand cloud IAM, network security, logging, encryption, posture management.
- Compare AWS, Azure, and GCP equivalents.

Study:
- AWS IAM, S3, EC2, VPC, Security Groups, CloudTrail, GuardDuty, KMS.
- Azure Entra ID, RBAC, Storage, VNets, NSG, Key Vault, Defender for Cloud.
- GCP IAM, Cloud Storage, VPC, Firewall Rules, Cloud Audit Logs, Security Command Center, Cloud KMS.

Practice:
- Explain shared responsibility in cloud.
- Explain how to secure object storage.
- Explain how to investigate suspicious cloud activity.

### Days 23-24: Infrastructure As Code Security

Goals:
- Understand Terraform, CloudFormation, ARM/Bicep, Kubernetes YAML.
- Know how misconfigurations enter through IaC.

Study:
- Hardcoded secrets, public storage buckets, open security groups, permissive IAM.
- Policy as code: OPA, Conftest, Sentinel.
- Drift detection and secure review.

Practice:
- Explain why IaC scanning matters.
- Explain how to stop `0.0.0.0/0` SSH exposure before deployment.

### Days 25-26: Compliance, Governance, And Reporting

Goals:
- Understand SOC 2, ISO 27001, PCI DSS, HIPAA basics.
- Learn risk prioritization and reporting language.

Study:
- Evidence, controls, audit, exception, compensating control.
- CVSS, EPSS, asset criticality, exploitability, business impact.

Practice:
- Explain a high severity vulnerability to a developer.
- Explain risk acceptance.
- Write a concise vulnerability report.

### Days 27-28: Deep-Dive Scenarios

Goals:
- Practice chained interview questions.
- Connect AppSec, CloudSec, pipeline security, and incident response.

Study:
- SSRF to cloud metadata risk.
- Leaked secrets to cloud compromise.
- Vulnerable dependency in production.
- Public bucket data exposure.
- Kubernetes service account token abuse.

Practice:
- Answer scenario chains out loud.
- Always cover detection, containment, root cause, remediation, prevention.

### Days 29-30: Mock Interviews And Revision

Goals:
- Build confidence and speed.
- Turn knowledge into interview answers.

Practice:
- 2 mock interviews on fundamentals.
- 2 mock interviews on tools.
- 2 mock interviews on cloud.
- 2 mock interviews on scenarios.

Final checklist:
- Can explain DevSecOps clearly.
- Can design a secure pipeline.
- Can test common AppSec vulnerabilities safely.
- Can compare AWS, Azure, GCP security controls.
- Can explain scanner findings and false positives.
- Can reason through incidents.

## Core Skill Map

### Must-Know Foundations

- Linux basics: files, permissions, users, processes, logs, services.
- Networking: IP, DNS, HTTP, HTTPS, TLS, TCP/UDP, ports, firewalls.
- Git: branching, PRs, tags, commits, secrets, branch protection.
- Scripting: Bash basics and Python basics for automation.
- Security basics: CIA triad, least privilege, defense in depth, risk.

### AppSec Skills

- OWASP Top 10.
- API security.
- Authentication and authorization.
- Session management.
- Secure coding review.
- Threat modeling.
- Vulnerability reporting.
- SAST, DAST, SCA, secret scanning.

### CloudSec Skills

- Cloud shared responsibility.
- IAM and RBAC.
- Object storage security.
- Network segmentation.
- Logging and monitoring.
- Encryption and key management.
- CSPM and misconfiguration detection.
- Incident response in cloud.

### DevSecOps Skills

- CI/CD pipeline design.
- Security gates.
- Secret management.
- Artifact security.
- Container image hardening.
- Kubernetes security.
- IaC scanning.
- Policy as code.
- SBOM and supply-chain security.

## Interview Answer Formula

Use this structure when you do not know how deep the interviewer wants to go:

1. Short definition.
2. Why it matters.
3. Example.
4. How to test or implement.
5. How to fix or improve.
6. Tool names.
7. Common mistake.

Example:

Question: What is SAST?

Answer:
SAST means Static Application Security Testing. It analyzes source code or compiled code without running the application. It matters because it can catch insecure code patterns early in the SDLC, before deployment. For example, it may detect SQL injection patterns, hardcoded secrets, insecure crypto usage, or missing input validation. In a CI/CD pipeline, we can run tools like SonarQube, Semgrep, or CodeQL on every pull request. Findings should be triaged because scanners can produce false positives. The fix depends on the issue, but usually involves code changes, safer libraries, validation, encoding, or secure APIs.

## Sources To Know

- OWASP Top 10: https://owasp.org/Top10/2021/
- OWASP ASVS: https://owasp.org/www-project-application-security-verification-standard/
- AWS Shared Responsibility Model: https://aws.amazon.com/compliance/shared-responsibility-model/
- Microsoft Azure shared responsibility guidance: https://azure.microsoft.com/resources/shared-responsibility-for-cloud-computing/
- Google Cloud shared responsibility and shared fate: https://docs.cloud.google.com/architecture/framework/security/shared-responsibility-shared-fate

