# Security Scanner Tools Deep Q&A

This file covers SAST, DAST, SCA, secret scanning, IaC scanning, container scanning, vulnerability management tools, and scanner triage. It is designed for tool-heavy DevSecOps interviews.

## Scanner Fundamentals

### Q1. What is the purpose of security scanning?

Security scanning identifies potential vulnerabilities, misconfigurations, secrets, outdated dependencies, insecure code patterns, and policy violations. In DevSecOps, scanners provide fast feedback during development and deployment.

Scanners are not perfect. Findings must be triaged for validity, exploitability, context, and business impact.

### Q2. What is SAST?

SAST means Static Application Security Testing. It analyzes source code, bytecode, or compiled code without running the application.

SAST is useful early in SDLC because it can run on pull requests and catch insecure patterns before deployment.

### Q3. What does SAST commonly find?

SAST can find SQL injection patterns, command injection, hardcoded secrets, insecure crypto, path traversal, unsafe deserialization, XSS patterns, missing validation, and framework misuse.

It may miss runtime configuration and business logic issues.

### Q4. What are SAST limitations?

SAST may produce false positives, struggle with dynamic code, miss runtime-only issues, and lack business context. It also may not know whether a vulnerable code path is reachable.

Tuning and developer feedback are important.

### Q5. What is DAST?

DAST means Dynamic Application Security Testing. It tests a running application from the outside by sending requests and analyzing responses.

DAST is useful for runtime issues, exposed endpoints, headers, server behavior, and some injection vulnerabilities.

### Q6. What are DAST limitations?

DAST may miss code paths it cannot reach, struggle with authentication, produce false positives, and fail to understand business logic. It can also be slower and may affect systems if active scans are aggressive.

Run intrusive scans only in approved environments.

### Q7. What is SCA?

SCA means Software Composition Analysis. It identifies third-party dependencies, versions, licenses, and known vulnerabilities.

SCA is critical because modern applications rely heavily on open-source packages.

### Q8. What are SCA limitations?

SCA may report vulnerabilities in transitive or unused packages, may not know runtime reachability, and depends on vulnerability databases. It can miss private or unknown vulnerabilities.

Prioritize based on exposure, exploitability, and fix availability.

### Q9. What is secret scanning?

Secret scanning detects credentials, API keys, tokens, private keys, and passwords in code, Git history, logs, containers, and artifacts.

If a real secret is found, rotate it. Deleting it is not enough.

### Q10. What is IaC scanning?

IaC scanning checks infrastructure code like Terraform, CloudFormation, Kubernetes YAML, Helm, ARM/Bicep, and Dockerfiles for insecure configurations.

It prevents cloud and Kubernetes misconfigurations before deployment.

### Q11. What is container scanning?

Container scanning analyzes container images for OS vulnerabilities, application dependencies, secrets, malware indicators, and misconfigurations.

It runs after image build and before push/deploy.

### Q12. What is vulnerability management?

Vulnerability management is the lifecycle of discovering, prioritizing, assigning, fixing, validating, and reporting vulnerabilities.

Tools find issues, but process ensures they are remediated.

## SAST Tools

### Q13. What is SonarQube?

SonarQube analyzes code quality, bugs, vulnerabilities, code smells, and security hotspots. It supports many languages and integrates with CI/CD.

Security hotspots require human review because they may or may not be exploitable vulnerabilities.

### Q14. What is a SonarQube quality gate?

A quality gate is a set of conditions that code must meet before passing, such as no new critical vulnerabilities, coverage threshold, or maintainability rating.

In DevSecOps, quality gates can block insecure code from merging.

### Q15. What is Semgrep?

Semgrep is a static analysis tool that uses readable rules to detect code patterns. It is strong for custom AppSec rules and fast CI feedback.

Teams use Semgrep to find insecure framework usage, missing auth checks, hardcoded secrets, and organization-specific patterns.

### Q16. Why is Semgrep popular?

Semgrep rules are relatively easy to write and understand. It supports many languages and integrates well with pull requests.

It is useful when teams want custom lightweight security checks.

### Q17. What is CodeQL?

CodeQL is a semantic code analysis engine that treats code as a database and runs queries to detect vulnerabilities.

It is powerful for deeper analysis and integrates strongly with GitHub Advanced Security.

### Q18. What is Checkmarx?

Checkmarx is an enterprise SAST platform used to scan source code for security vulnerabilities. It provides governance, reporting, and integration features.

Like all SAST tools, findings require triage and developer remediation.

### Q19. What is Fortify?

Fortify is an enterprise application security testing suite with SAST and related capabilities. It is often used in large organizations with compliance requirements.

It can provide detailed findings but needs tuning to reduce noise.

### Q20. What is Veracode?

Veracode is a cloud-based application security platform offering SAST, DAST, SCA, and other capabilities depending on product usage.

It is commonly used for enterprise AppSec governance.

### Q21. How do you choose a SAST tool?

Choose based on language support, framework understanding, CI integration, false-positive rate, custom rule support, reporting, developer workflow, and cost.

No SAST tool is perfect; adoption and tuning matter.

### Q22. How do you triage SAST findings?

Review source, data flow, input source, sink, validation, exploitability, reachability, and existing controls. Reproduce where possible or create a unit/security test.

Mark false positives with justification and tune rules.

## DAST Tools

### Q23. What is OWASP ZAP?

OWASP ZAP is an open-source web application security scanner and proxy. It supports passive scanning, active scanning, spidering, API scanning, and automation.

It is useful for baseline scans and CI/CD DAST workflows.

### Q24. What is Burp Suite Scanner?

Burp Suite Scanner is a commercial DAST scanner in Burp Suite Professional/Enterprise. It tests running web apps for vulnerabilities.

Burp is also excellent for manual testing through Proxy and Repeater.

### Q25. What is StackHawk?

StackHawk is a DAST tool designed for developer workflows and CI/CD integration. It can test web apps and APIs in development pipelines.

It emphasizes automation and developer-friendly feedback.

### Q26. What is Acunetix?

Acunetix is a commercial web vulnerability scanner. It tests running web applications for common vulnerabilities such as injection, XSS, and misconfigurations.

As with all DAST, validate findings and configure authentication.

### Q27. What is Invicti?

Invicti, formerly Netsparker, is a commercial DAST platform. It provides web app scanning, reporting, and vulnerability management features.

It is often used for enterprise web scanning.

### Q28. How do you run authenticated DAST?

Configure login scripts, session tokens, test credentials, API tokens, or recorded authentication flows. Ensure the scanner can maintain sessions and access protected areas.

Authenticated scanning gives better coverage than unauthenticated scanning.

### Q29. Why can DAST be risky in production?

Active DAST sends payloads and may trigger rate limits, create test data, affect performance, or modify state. Intrusive scans should run in staging unless production scanning is approved and controlled.

Use safe scan policies for production.

### Q30. How do you triage DAST findings?

Reproduce the request, inspect response, confirm exploitability, check authentication/authorization context, assess data impact, and identify root cause.

Do not rely only on scanner labels.

## SCA Tools

### Q31. What is Snyk?

Snyk scans dependencies, containers, IaC, and code depending on product usage. It identifies vulnerabilities and remediation options.

It is common in DevSecOps because it integrates with repositories and CI/CD.

### Q32. What is OWASP Dependency-Check?

OWASP Dependency-Check is an open-source SCA tool that detects known vulnerable dependencies using vulnerability databases.

It is useful but may require suppression tuning for false positives.

### Q33. What is Dependabot?

Dependabot detects vulnerable dependencies and can automatically open pull requests to update them in GitHub repositories.

It helps automate dependency maintenance.

### Q34. What is Renovate?

Renovate automates dependency updates across many ecosystems and platforms. It is highly configurable.

Security benefit: keeps dependencies updated through controlled PRs.

### Q35. What is npm audit?

`npm audit` checks Node.js dependencies for known vulnerabilities using npm advisories.

It is useful but should be combined with broader SCA and context-based triage.

### Q36. What is pip-audit?

`pip-audit` checks Python dependencies for known vulnerabilities using Python packaging advisory data.

It is useful for Python projects in CI.

### Q37. What is OSV-Scanner?

OSV-Scanner scans dependencies against the OSV vulnerability database. It supports multiple ecosystems.

It is useful for open-source vulnerability detection.

### Q38. How do you handle vulnerable transitive dependencies?

Upgrade the direct dependency that brings it in, override dependency version if safe, wait for upstream fix with mitigation, or replace the package.

Always test after upgrades.

### Q39. What is license scanning?

License scanning identifies open-source licenses used by dependencies. It helps legal and compliance teams avoid license conflicts.

DevSecOps may include license policy gates.

### Q40. How do you prioritize SCA findings?

Consider severity, exploit availability, reachability, internet exposure, sensitive data, direct vs transitive dependency, fix availability, and runtime usage.

Do not blindly treat every CVE equally.

## Secret Scanning Tools

### Q41. What is Gitleaks?

Gitleaks detects secrets in Git repositories, working trees, and history. It can run locally, in pre-commit hooks, and in CI.

If it finds a real secret, rotate the secret immediately.

### Q42. What is TruffleHog?

TruffleHog scans repositories and other sources for secrets. It can verify some secrets to reduce false positives.

Verification can be useful but must be handled carefully to avoid unintended use of credentials.

### Q43. What is GitGuardian?

GitGuardian is a secret detection and remediation platform. It monitors repositories and can alert on leaked secrets.

It is commonly used by organizations needing dashboards and workflow management.

### Q44. What is detect-secrets?

detect-secrets is an open-source tool for finding secrets and managing a baseline of known findings.

It is useful for pre-commit and CI workflows.

### Q45. Where should secret scanning run?

Run secret scanning locally with pre-commit, on pull requests, in CI, on default branches, on Git history, and sometimes on containers/artifacts.

Multiple layers reduce accidental leaks.

### Q46. What do you do when scanner finds a secret?

Validate whether it is real. If real, revoke or rotate it, investigate usage, remove from source/history if needed, update application to use secret manager, and add prevention.

Never close by only deleting the line.

### Q47. What is a secret scanning false positive?

A false positive may be a dummy key, test fixture, random string, or example value that matches a secret pattern but has no access.

Document and suppress carefully so real secrets are not ignored.

### Q48. Why scan Git history?

Secrets may be deleted from current code but remain in previous commits. Attackers search history.

History scanning helps find older leaks that still require rotation.

## IaC And Container Scanners

### Q49. What is Checkov?

Checkov scans IaC files such as Terraform, CloudFormation, Kubernetes, Helm, Dockerfiles, and more for security and compliance issues.

It is commonly used in pull request checks.

### Q50. What is tfsec?

tfsec scans Terraform code for cloud security misconfigurations. It is focused and easy to use for Terraform-heavy teams.

It can detect open security groups, public storage, missing encryption, and weak IAM.

### Q51. What is Terrascan?

Terrascan scans IaC for policy violations across multiple platforms and formats.

It supports DevSecOps workflows where infrastructure security is checked before deployment.

### Q52. What is KICS?

KICS scans IaC for vulnerabilities and misconfigurations across Terraform, Kubernetes, Dockerfile, CloudFormation, Ansible, and other formats.

It is another broad IaC scanning option.

### Q53. What is Trivy?

Trivy is a broad open-source scanner for container images, filesystems, repositories, IaC, Kubernetes, secrets, and SBOMs.

It is often a default DevSecOps tool because it covers many areas.

### Q54. What is Grype?

Grype scans container images and filesystems for vulnerabilities. It is often paired with Syft for SBOM generation.

It is useful in container security pipelines.

### Q55. What is Syft?

Syft generates SBOMs for container images and filesystems. It identifies packages and components.

SBOMs help with vulnerability response and supply-chain visibility.

### Q56. What is Docker Scout?

Docker Scout analyzes container images for vulnerabilities and package information. It integrates with Docker tooling.

It can guide image remediation and dependency visibility.

### Q57. How do you scan Kubernetes manifests?

Use Checkov, Trivy, Kubescape, Polaris, kube-score, Datree, or Kyverno policies. Check for privileged pods, missing limits, hostPath, root containers, broad RBAC, and public services.

Manifest scanning should run before deployment.

### Q58. How do you scan Terraform plans?

Generate a plan file or JSON plan and scan it using supported tools like Checkov or OPA/Conftest. Plan scanning can catch resolved module values.

Scan both source and plan when possible.

## Vulnerability Management And Triage

### Q59. What is CVE?

CVE is a standardized identifier for publicly known vulnerabilities. It helps tools and teams refer to the same issue.

A CVE ID alone does not prove exploitability in your environment.

### Q60. What is CVSS?

CVSS is a scoring system for vulnerability severity. It considers factors like attack vector, complexity, privileges, user interaction, and impact.

Use CVSS as one input, not the only prioritization factor.

### Q61. What is EPSS?

EPSS estimates the probability that a vulnerability will be exploited in the wild. It helps prioritize vulnerabilities likely to be attacked.

Use EPSS alongside CVSS, asset criticality, and exposure.

### Q62. What is CISA KEV?

CISA Known Exploited Vulnerabilities catalog lists vulnerabilities known to be exploited. KEV-listed vulnerabilities should be prioritized strongly.

Even non-US organizations can use it as practical threat intelligence.

### Q63. What is false positive triage?

False positive triage determines whether a finding is invalid or not exploitable. It involves reviewing evidence, context, code, configuration, and runtime behavior.

Suppress only with justification.

### Q64. What is false negative risk?

False negative means a tool misses a real vulnerability. Business logic flaws, auth issues, and chained attacks are common false negatives for automated tools.

Manual testing and threat modeling are still needed.

### Q65. What is deduplication in vulnerability management?

Deduplication groups duplicate findings from multiple tools or repeated assets. It reduces noise and helps focus remediation.

For example, the same vulnerable package may appear in SCA and container scanning.

### Q66. What is risk-based prioritization?

Risk-based prioritization ranks findings by actual risk, not just scanner severity. Inputs include exploitability, exposure, asset criticality, sensitive data, active exploitation, and business impact.

This is how mature security teams manage large finding volumes.

### Q67. What is remediation SLA?

Remediation SLA defines expected fix timelines by severity or risk. Example: critical in 7 days, high in 30 days, medium in 60 days.

SLAs should consider exploitability and business context.

### Q68. What is exception management?

Exception management tracks approved deviations from security policy. Exceptions should include reason, owner, expiry, compensating controls, and approval.

Do not use exceptions as permanent hiding places for risk.

### Q69. What is retesting?

Retesting confirms a vulnerability is fixed by repeating the original test and checking related variants.

A finding should not be closed only because a developer says it is fixed.

### Q70. What metrics matter for scanner programs?

Useful metrics include critical/high open findings, mean time to remediate, SLA compliance, false-positive rate, scan coverage, recurring root causes, and findings by team/application.

Metrics should drive improvement, not blame.

## Scanner Scenario Questions

### Q71. SAST says SQL injection, DAST does not find it. What do you do?

Review the code path. DAST may not reach the vulnerable path or may lack authentication. Validate whether untrusted input reaches SQL and whether parameterization is used.

Conflicting tool results require investigation, not automatic dismissal.

### Q72. SCA finds critical CVE in package not used at runtime. What do you do?

Validate reachability. If truly unused, remove the package or document lower risk. If it is included in production artifact, still consider updating because unused packages increase attack surface.

Risk may be lower but not always zero.

### Q73. Trivy finds critical OS CVE but no fix available. What do you do?

Check exploitability and exposure, apply compensating controls, monitor, consider alternative base image, and create a time-bound exception until a fix exists.

Do not ignore it silently.

### Q74. Secret scanner finds test API key. What do you do?

Validate if it is real. If it grants access anywhere, rotate it. If dummy, mark as test safely and tune scanner rules.

Many real incidents start with "it was only a test key."

### Q75. DAST scan reports missing CSP. How do you rate it?

Usually low or medium depending on application sensitivity and XSS exposure. Missing CSP is defense-in-depth weakness, not automatically confirmed XSS.

Recommend CSP but prioritize based on risk.

### Q76. Scanner reports public S3 in Terraform but bucket is for public website. What do you do?

Validate intended public use, ensure only public static content is stored, separate private data, document exception, and apply safe bucket policy.

Context matters in IaC triage.

### Q77. Multiple scanners report same dependency CVE. How do you manage it?

Deduplicate findings, identify affected applications/images, assign one remediation owner, update dependency/base image, rescan all sources, and close duplicates with evidence.

This reduces noise.

### Q78. A tool has too many false positives. What do you do?

Tune rules, configure paths, suppress known false positives with justification, improve custom rules, set severity thresholds, and train developers on high-confidence findings.

Do not remove the tool without understanding root cause.

### Q79. How do you introduce a new scanner to developers?

Start in monitor/audit mode, share findings, tune noise, document remediation guidance, integrate with PR comments, then enforce gates for high-confidence critical issues.

Adoption matters as much as tool capability.

### Q80. How do you summarize scanner strategy?

Use layered scanning: SAST for code, SCA for dependencies, secret scanning for credentials, IaC scanning for infrastructure, container scanning for images, DAST for running apps, and CSPM/runtime tools after deployment.

Then triage by risk and feed results into remediation workflows.

