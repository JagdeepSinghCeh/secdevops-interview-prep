# DevSecOps Master Q&A Bank

This file starts from very basic questions and moves toward deeper AppSec, CloudSec, DevSecOps, tool-based, and conditional interview questions. Every answer is written in interview style: definition, purpose, example, and practical angle.

## Section 1: Very Basic DevSecOps Questions

### Q1. What is DevSecOps?

DevSecOps means Development, Security, and Operations working together so security becomes part of the software delivery lifecycle instead of a final-stage activity. In traditional development, security testing often happens near release, which causes late defects, delays, and production risk. DevSecOps shifts security left by adding security checks into coding, pull requests, CI/CD pipelines, infrastructure provisioning, deployment, monitoring, and incident response.

In practical terms, DevSecOps includes secure coding, threat modeling, SAST, DAST, SCA, secret scanning, container scanning, IaC scanning, cloud security checks, logging, monitoring, and automated security gates. The goal is not only to find vulnerabilities but to prevent insecure changes from reaching production.

### Q2. Why is DevSecOps important?

DevSecOps is important because modern teams release software quickly, sometimes many times per day. If security is manual and late, it cannot keep up with DevOps speed. DevSecOps helps reduce risk by finding issues earlier, automating repeatable checks, and making developers responsible for secure changes with support from security teams.

It also reduces cost. Fixing a vulnerability during coding is much cheaper than fixing it after production deployment. For interviews, say that DevSecOps improves speed, security, compliance, visibility, and accountability.

### Q3. What is the difference between DevOps and DevSecOps?

DevOps focuses on collaboration between development and operations to deliver software faster and more reliably. DevSecOps extends DevOps by embedding security into the same process. DevOps asks, "Can we build, test, and deploy efficiently?" DevSecOps asks, "Can we do that securely and continuously?"

For example, a DevOps pipeline may build code, run unit tests, and deploy to Kubernetes. A DevSecOps pipeline adds SAST, dependency scanning, secret scanning, image scanning, IaC scanning, policy checks, approvals, signing, and runtime monitoring.

### Q4. What are the main components of DevSecOps?

The main components are secure planning, secure coding, code review, automated security testing, dependency management, secrets management, infrastructure security, container and Kubernetes security, cloud security, compliance, monitoring, incident response, and feedback loops.

Tool categories include SAST, DAST, SCA, secret scanning, IaC scanning, container scanning, CSPM, SIEM, vulnerability management, and policy as code. The process side is equally important: ownership, risk acceptance, remediation SLAs, developer training, and metrics.

### Q5. What is shift-left security?

Shift-left security means moving security activities earlier in the SDLC. Instead of testing only after deployment, teams perform security checks during design, coding, pull requests, and CI builds. The "left" side refers to the earlier stages of a typical software timeline.

Examples include threat modeling before development, secure coding guidelines, SAST during pull requests, SCA during builds, and IaC scanning before cloud resources are created. The benefit is faster remediation and fewer production vulnerabilities.

### Q6. What is shift-right security?

Shift-right security means security activities after deployment, during runtime and production operations. Examples include runtime monitoring, WAF alerts, cloud threat detection, SIEM correlation, container runtime security, anomaly detection, incident response, and penetration testing after release.

Good DevSecOps uses both shift-left and shift-right. Shift-left prevents issues early, while shift-right detects real-world attacks and misconfigurations that appear in production.

### Q7. What is SDLC?

SDLC means Software Development Life Cycle. It is the process used to plan, design, develop, test, deploy, and maintain software. Security can be added at each phase: requirements include security requirements, design includes threat modeling, development includes secure coding, testing includes SAST/DAST/SCA, deployment includes secure configuration, and maintenance includes monitoring and patching.

In interviews, connect SDLC to DevSecOps by saying DevSecOps integrates security controls into every SDLC phase.

### Q8. What is CI/CD?

CI/CD means Continuous Integration and Continuous Delivery or Continuous Deployment. Continuous Integration means developers merge code frequently and automated builds/tests run on each change. Continuous Delivery means code is always in a deployable state but may require manual approval. Continuous Deployment means successful changes are automatically deployed to production.

Security fits into CI/CD through automated scans, policy gates, secrets checks, artifact signing, deployment approvals, and post-deployment monitoring.

### Q9. What is a pipeline?

A pipeline is an automated workflow that takes code from source control through build, test, scan, package, deploy, and monitor stages. A typical pipeline may include checkout, dependency install, unit tests, SAST, SCA, container build, image scan, IaC scan, deployment to staging, DAST, approval, and production deployment.

In DevSecOps, the pipeline becomes a security enforcement point. It helps make security repeatable and measurable.

### Q10. What is cloud computing?

Cloud computing means using computing resources such as servers, storage, databases, networking, and security services over the internet from providers like AWS, Azure, and GCP. Instead of buying physical hardware, organizations rent resources on demand.

The main benefits are scalability, availability, faster provisioning, managed services, and pay-as-you-go cost. Security is shared between the cloud provider and the customer, which is why shared responsibility is a key interview topic.

### Q11. What is AWS?

AWS, or Amazon Web Services, is a cloud provider offering services for compute, storage, databases, networking, security, analytics, machine learning, and DevOps. Common AWS services include EC2, S3, VPC, IAM, Lambda, RDS, CloudTrail, GuardDuty, Security Hub, KMS, and EKS.

For DevSecOps interviews, focus on AWS IAM, logging, encryption, network security, S3 security, vulnerability management, CI/CD, and container security.

### Q12. What is Azure?

Microsoft Azure is a cloud platform from Microsoft. Important services for security include Microsoft Entra ID, Azure RBAC, Key Vault, Defender for Cloud, Azure Monitor, Log Analytics, Storage Accounts, VNets, NSGs, Azure Kubernetes Service, and Azure DevOps.

In interviews, show that Azure identity is central. Many Azure security questions revolve around Entra ID, RBAC, managed identities, Key Vault, Defender for Cloud recommendations, and network security groups.

### Q13. What is GCP?

Google Cloud Platform is Google's cloud platform. Important security services include Cloud IAM, Cloud Storage, VPC Firewall Rules, Cloud Audit Logs, Security Command Center, Cloud KMS, Secret Manager, Artifact Registry, GKE, and Cloud Build.

For interviews, focus on IAM, service accounts, audit logs, organization policies, storage permissions, GKE security, and Security Command Center.

### Q14. What is the cloud shared responsibility model?

The shared responsibility model defines what the cloud provider secures and what the customer secures. Generally, the provider secures the cloud infrastructure, physical data centers, hardware, and managed service foundation. The customer secures identities, access permissions, data, configurations, workloads, applications, and network exposure.

For example, AWS secures the physical infrastructure behind S3, but the customer must configure bucket access, encryption, logging, lifecycle policies, and data protection correctly.

### Q15. What is the CIA triad?

CIA stands for Confidentiality, Integrity, and Availability. Confidentiality means only authorized users can access data. Integrity means data remains accurate and unmodified except by authorized actions. Availability means systems and data are accessible when needed.

Most security controls map to these goals. Encryption protects confidentiality, hashing and signatures protect integrity, and redundancy and DDoS protection support availability.

### Q16. What is defense in depth?

Defense in depth means using multiple layers of security controls so one failure does not cause total compromise. For example, a web app may use input validation, authentication, authorization, WAF, network segmentation, logging, least privilege, and monitoring.

In DevSecOps, defense in depth appears across code, pipeline, infrastructure, cloud, containers, and runtime. It is a strong interview concept because it shows mature security thinking.

### Q17. What is least privilege?

Least privilege means giving users, services, roles, and applications only the permissions required to perform their tasks, and no more. It reduces blast radius if an account or service is compromised.

In cloud, this means avoiding wildcard permissions like `*:*`, using scoped IAM roles, separating duties, and regularly reviewing access. In CI/CD, it means pipeline tokens should only access required repositories, secrets, and deployment targets.

### Q18. What is a vulnerability?

A vulnerability is a weakness in software, configuration, design, or process that could be exploited to cause harm. Examples include SQL injection, public storage buckets, weak passwords, outdated libraries, exposed admin panels, and overly permissive IAM policies.

A vulnerability is not the same as an exploit. The vulnerability is the weakness; the exploit is the method used to abuse it.

### Q19. What is a threat?

A threat is a potential cause of harm. It could be an attacker, malware, insider, accidental misconfiguration, compromised credential, or natural event. In security analysis, we identify threats to understand what can go wrong and what controls are needed.

Example: for an API handling payment data, threats include unauthorized access, token theft, injection, replay attacks, and data leakage.

### Q20. What is risk?

Risk is the likelihood and impact of a threat exploiting a vulnerability. A low severity bug on a public payment system can become high risk if it exposes sensitive data. A high CVSS issue on an internal isolated test system may have lower practical risk.

In interviews, say risk prioritization should consider CVSS, exploitability, asset criticality, internet exposure, sensitive data, existing controls, and business impact.

## Section 2: AppSec And OWASP

### Q21. What is OWASP?

OWASP is the Open Worldwide Application Security Project, a community that publishes security guidance, tools, and standards for application security. Interviewers often expect knowledge of OWASP Top 10, ASVS, API Security Top 10, and testing guides.

OWASP Top 10 is an awareness document listing common web application risks. OWASP ASVS is more detailed and can be used as a verification standard for application security requirements.

### Q22. What is OWASP Top 10?

OWASP Top 10 is a list of major web application security risk categories. It helps developers, security engineers, and organizations understand common classes of vulnerabilities such as broken access control, injection, insecure design, security misconfiguration, vulnerable components, and SSRF.

It is not a complete testing checklist. A mature security program should also use ASVS, threat modeling, secure code review, API testing, cloud security checks, and business logic testing.

### Q23. What is broken access control?

Broken access control happens when users can perform actions or access data they are not authorized to access. Examples include viewing another user's invoice by changing an ID, accessing admin APIs as a normal user, bypassing role checks, or modifying records without ownership validation.

Testing involves checking horizontal privilege escalation, vertical privilege escalation, missing server-side authorization, insecure direct object references, forced browsing, and method-level authorization. Fixes include server-side authorization checks, deny-by-default policies, object ownership validation, centralized access control, and automated tests.

### Q24. What is IDOR?

IDOR means Insecure Direct Object Reference. It occurs when an application exposes an object identifier and fails to verify whether the current user is allowed to access that object. For example, changing `/invoice/1001` to `/invoice/1002` may reveal another user's invoice.

To test safely in an authorized environment, create two test users and try accessing one user's resources from the other user's session. The fix is server-side authorization based on the authenticated user and object ownership, not hiding IDs.

### Q25. What is injection?

Injection happens when untrusted input is interpreted as a command or query by a backend system. SQL injection, command injection, LDAP injection, and NoSQL injection are common examples. The root problem is mixing user input with executable syntax.

Testing includes checking inputs, parameters, headers, cookies, and JSON fields for unsafe behavior. Fixes include parameterized queries, safe APIs, input validation, output encoding where appropriate, avoiding shell execution, and least-privileged database accounts.

### Q26. What is SQL injection?

SQL injection occurs when user input is inserted into SQL queries without proper parameterization, allowing an attacker to change the query logic. Impact can include authentication bypass, data extraction, data modification, or database takeover depending on privileges.

In interviews, mention that the correct fix is prepared statements or parameterized queries, not only blacklisting characters. Additional protections include ORM safe usage, least-privileged DB users, error handling, logging, and WAF as a supporting control.

### Q27. What is XSS?

XSS, or Cross-Site Scripting, occurs when an application includes untrusted data in a web page without proper output encoding or sanitization, allowing attacker-controlled JavaScript to run in another user's browser.

Types include stored XSS, reflected XSS, and DOM-based XSS. Impact includes session theft, account actions, phishing, token theft, and data access in the user's browser context. Fixes include contextual output encoding, safe templating, sanitization for HTML input, Content Security Policy, and avoiding unsafe DOM APIs.

### Q28. What is CSRF?

CSRF, or Cross-Site Request Forgery, tricks an authenticated user's browser into submitting an unwanted request to a site where the user is logged in. It matters when the application relies only on cookies for authentication and does not verify request intent.

Fixes include anti-CSRF tokens, SameSite cookies, checking origin/referer where appropriate, re-authentication for sensitive actions, and using proper HTTP methods. APIs using bearer tokens in authorization headers are usually less exposed to classic CSRF, but still need CORS and token protection.

### Q29. What is SSRF?

SSRF means Server-Side Request Forgery. It occurs when an attacker can make the server send requests to attacker-chosen URLs or internal resources. This is dangerous because the server may have access to internal networks, metadata services, cloud credentials, or private APIs that external users cannot reach.

Testing should only be done with permission. Safe testing includes checking URL fetch features, webhooks, import-by-URL features, PDF generators, image fetchers, and integrations. Use allowlisted test domains or approved OAST/callback tools to confirm whether the server makes outbound requests. Fixes include strict allowlists, blocking internal IP ranges, disabling redirects, validating DNS resolution safely, network egress controls, and cloud metadata protections like IMDSv2 in AWS.

### Q30. How do you test for SSRF in an authorized application?

First identify features where the server fetches a URL: webhooks, image upload by URL, file import, PDF generation, link preview, XML parsers, or integrations. Then provide a controlled URL you own or an approved collaborator endpoint and check if the server calls it. Next, test whether the application blocks private IPs, localhost, cloud metadata addresses, redirects, DNS rebinding, and alternate IP formats.

Do not test random public targets. In a professional assessment, document the feature, request, callback evidence, reachable network scope, impact, and recommended fixes. Always include mitigation: allowlist domains, validate scheme, block private ranges, restrict egress, avoid following redirects blindly, and protect metadata endpoints.

### Q31. What is insecure deserialization?

Insecure deserialization happens when an application accepts serialized data from users and converts it back into objects without safe validation. If the format or library allows object creation, method execution, or gadget chains, attackers may cause remote code execution, privilege escalation, or logic abuse.

Fixes include avoiding native object deserialization for untrusted input, using safe formats like JSON with strict schemas, signing serialized data, validating integrity, and keeping libraries patched.

### Q32. What is security misconfiguration?

Security misconfiguration means insecure default settings, unnecessary services, verbose errors, public admin consoles, missing security headers, weak cloud permissions, open storage buckets, or debug mode in production.

In DevSecOps, misconfigurations are reduced through hardening baselines, IaC scanning, configuration review, CSPM tools, automated checks, and continuous monitoring.

### Q33. What are vulnerable and outdated components?

This risk occurs when applications use dependencies, libraries, containers, operating systems, or services with known vulnerabilities. Attackers often exploit public CVEs in outdated components.

Controls include SCA scanning, dependency pinning, SBOM generation, patch management, removing unused dependencies, monitoring advisories, and testing updates before release.

### Q34. What is authentication?

Authentication verifies who a user or service is. Examples include passwords, MFA, SSO, certificates, API keys, and workload identities. A login system performs authentication.

Good authentication uses strong password storage, MFA for sensitive access, rate limiting, account lockout or risk-based controls, secure recovery flows, and session protection.

### Q35. What is authorization?

Authorization decides what an authenticated user or service is allowed to do. For example, a normal user may view their own profile but not another user's profile or admin settings.

Authorization must be enforced server-side. UI hiding is not security because attackers can call APIs directly. Strong systems use RBAC, ABAC, object ownership checks, and deny-by-default rules.

### Q36. Authentication vs authorization?

Authentication answers "Who are you?" Authorization answers "What are you allowed to access?" A user can be authenticated but still unauthorized for a specific resource.

Example: logging into a banking app proves identity, but the app must still verify that the account being viewed belongs to that user.

### Q37. What is a JWT?

JWT means JSON Web Token. It is a token format commonly used to carry claims such as subject, issuer, audience, expiry, and roles. JWTs are signed so the receiver can verify integrity.

Security checks include validating signature, algorithm, expiry, issuer, audience, and token storage. Common mistakes include accepting `none` algorithm, not checking expiry, using weak secrets, storing tokens insecurely, or trusting client-side claims without proper validation.

### Q38. What are secure cookie flags?

Important cookie flags include `Secure`, `HttpOnly`, and `SameSite`. `Secure` ensures the cookie is sent only over HTTPS. `HttpOnly` prevents JavaScript from reading the cookie, reducing token theft through XSS. `SameSite` helps reduce CSRF risk.

For session cookies, these flags are standard security expectations. Also set proper expiry, path, domain, and avoid storing sensitive data directly in cookies.

### Q39. What is CORS?

CORS, or Cross-Origin Resource Sharing, controls which origins can read responses from a web application in browsers. It is enforced by browsers, not by backend-to-backend requests.

Insecure CORS can expose sensitive API responses to malicious websites. Risky patterns include reflecting arbitrary origins, using wildcard origin with credentials, and trusting origin for authentication. Fixes include strict origin allowlists and avoiding credentialed cross-origin access unless needed.

### Q40. What is rate limiting?

Rate limiting restricts how many requests a user, IP, token, or client can make in a time window. It helps prevent brute force, credential stuffing, scraping, denial of service, and abuse of expensive operations.

Good rate limiting considers endpoint sensitivity, identity, IP, device, geography, and failure rate. Authentication, password reset, OTP, and payment endpoints need strong anti-abuse controls.

## Section 3: Testing Workflows

### Q41. What do you check first on any website during a security review?

First understand scope, functionality, technology, user roles, authentication flow, exposed endpoints, and sensitive data. Then map the application: pages, APIs, parameters, file uploads, integrations, admin areas, and third-party services. After mapping, test common risks like access control, authentication, input validation, session management, business logic, file upload, security headers, TLS, and dependency exposure.

In interviews, emphasize that good testing is systematic. You do not randomly run tools. You understand the app, identify attack surfaces, test manually and with tools, validate impact, and report clearly.

### Q42. How do you test a login page?

Check password policy, brute-force protection, rate limiting, account lockout behavior, error messages, MFA, session cookie flags, password reset flow, username enumeration, CAPTCHA or risk controls if applicable, and secure transport over HTTPS.

Also check whether successful login rotates session ID, whether logout invalidates session, whether remember-me tokens are secure, and whether default credentials exist. Tools like Burp Suite help intercept requests, but many login issues require manual logic testing.

### Q43. How do you test access control?

Create users with different roles, such as normal user, manager, and admin. Capture requests for sensitive functions and replay them using lower-privileged accounts. Test horizontal access, vertical access, direct API access, forced browsing, object ID changes, HTTP method changes, and hidden endpoints.

Strong access control testing must verify server-side enforcement. If the UI hides a button but the API still allows the action, access control is broken.

### Q44. How do you test an API?

Start by understanding authentication, authorization, endpoints, methods, request bodies, rate limits, and data sensitivity. Test missing auth, broken object-level authorization, mass assignment, injection, schema validation, excessive data exposure, rate limiting, CORS, JWT validation, and error handling.

Use Postman, Burp Suite, OWASP ZAP, curl, or API specs like OpenAPI. Always test with different roles and negative cases, not only happy paths.

### Q45. How do you test file upload functionality?

Check allowed file types, MIME type validation, extension validation, file size limits, storage location, access control, malware scanning, random file names, direct execution, metadata handling, and public URL exposure. Try uploading unexpected formats only in an authorized test environment.

Fixes include allowlisting file types, validating content server-side, storing files outside executable paths, scanning files, removing metadata, enforcing access control, and using pre-signed URLs carefully.

### Q46. How do you test security headers?

Check headers like Content-Security-Policy, Strict-Transport-Security, X-Content-Type-Options, X-Frame-Options or frame-ancestors, Referrer-Policy, Permissions-Policy, and cookie flags. Tools can identify missing headers, but the correct policy depends on application behavior.

Security headers are defense-in-depth controls. Missing headers may not always be critical alone, but they reduce browser-side attack impact.

### Q47. How do you test TLS/SSL?

Check certificate validity, hostname match, expiry, trusted CA, supported TLS versions, weak ciphers, HSTS, redirect from HTTP to HTTPS, and mixed content. Tools like SSL Labs, testssl.sh, or nmap scripts can help.

For production, TLS 1.2 or TLS 1.3 should be used, old protocols like SSLv3/TLS 1.0/TLS 1.1 should be disabled, and certificates should be monitored for expiry.

### Q48. How do you test for secrets exposure?

Search source code, Git history, CI logs, container images, environment files, Terraform state, documentation, and public repositories. Tools include Gitleaks, TruffleHog, GitGuardian, detect-secrets, and secret scanning in GitHub/GitLab.

If a secret is found, do not only delete it. Rotate or revoke the secret, check logs for usage, remove it from history if needed, and move secret storage to a proper secrets manager.

### Q49. How do you test dependencies?

Use SCA tools to identify known vulnerable dependencies and licenses. Tools include Snyk, OWASP Dependency-Check, Dependabot, Renovate, npm audit, pip-audit, Trivy, and Grype. Review severity, exploitability, direct vs transitive dependency, available fix version, and runtime exposure.

The answer should include triage. Not every CVE is exploitable in your app, but vulnerable internet-facing components should be prioritized quickly.

### Q50. How do you test cloud storage security?

Check whether buckets or containers are public, whether encryption is enabled, whether access logs are enabled, whether versioning and retention are configured, whether policies are overly permissive, and whether sensitive data is stored. For AWS S3, check bucket policy, ACLs, Block Public Access, encryption, logging, and IAM permissions.

For Azure Storage, check public blob access, shared access signatures, account keys, private endpoints, encryption, and Defender alerts. For GCP Cloud Storage, check IAM, public access prevention, uniform bucket-level access, encryption, retention, and audit logs.

## Section 4: Tool-Based Q&A

### Q51. What is Burp Suite?

Burp Suite is a web application security testing platform. It works as an intercepting proxy between browser and application, allowing testers to inspect and modify HTTP requests and responses. Common features include Proxy, Repeater, Intruder, Decoder, Comparer, Scanner, and Collaborator.

In interviews, say Burp is useful for manual AppSec testing because you can understand application logic, test access control, replay requests, manipulate tokens, and validate vulnerabilities.

### Q52. What is OWASP ZAP?

OWASP ZAP is an open-source web application security scanner and proxy. It can perform passive scanning, active scanning, spidering, AJAX spidering, and automation in CI/CD. It is often used as a DAST tool.

ZAP is useful for baseline scans and automated checks, but manual validation is still needed because scanners may miss business logic issues and may produce false positives.

### Q53. What is Nmap?

Nmap is a network scanning tool used to discover hosts, open ports, services, and sometimes service versions. Security teams use it for asset discovery and exposure validation.

In interviews, explain that Nmap should be used only on authorized targets. Results help identify unnecessary exposed services, weak segmentation, and possible attack surface.

### Q54. What is Nuclei?

Nuclei is a fast template-based vulnerability scanner. It uses YAML templates to check for known exposures, misconfigurations, CVEs, panels, technologies, and weak configurations.

It is powerful because templates are community-driven and easy to customize. However, findings need validation because template matches can be noisy or context-dependent.

### Q55. What is Nikto?

Nikto is a web server scanner that checks for dangerous files, outdated server software, default files, misconfigurations, and common web server issues. It is useful for quick baseline checks but can be noisy and should not replace manual testing.

In interviews, position Nikto as an initial reconnaissance and misconfiguration scanner, not a complete web application security solution.

### Q56. What is SonarQube?

SonarQube is a code quality and security analysis platform. It can detect bugs, code smells, vulnerabilities, and security hotspots across multiple languages. In DevSecOps, it is often integrated into CI/CD pipelines and pull request checks.

Security hotspots require review because they may not always be vulnerabilities. A good answer should mention quality gates, false-positive triage, and developer feedback.

### Q57. What is Semgrep?

Semgrep is a static analysis tool that uses patterns to find insecure code, bugs, and policy violations. It is popular because rules are readable and can be customized for organization-specific patterns.

Examples include finding hardcoded secrets, unsafe deserialization, missing authorization checks, insecure crypto, and dangerous framework usage.

### Q58. What is CodeQL?

CodeQL is a semantic code analysis engine from GitHub. It treats code like a database and runs queries to detect vulnerabilities and code patterns. It supports languages like JavaScript, Python, Java, C/C++, C#, Go, and others.

In interviews, say CodeQL is useful for deeper static analysis and can be integrated into GitHub Advanced Security workflows.

### Q59. What is Snyk?

Snyk is a developer security platform used for dependency scanning, container scanning, IaC scanning, and code security. It helps identify vulnerabilities and suggests fixed versions or remediation steps.

In a pipeline, Snyk can fail builds based on severity thresholds, but teams should tune policies to avoid blocking unnecessarily on low-risk issues.

### Q60. What is Trivy?

Trivy is an open-source security scanner by Aqua Security. It can scan container images, filesystems, Git repositories, Kubernetes configurations, IaC files, dependencies, secrets, and SBOMs.

It is widely used in DevSecOps because it is simple to automate in CI/CD and gives broad coverage for containers and infrastructure.

### Q61. What is Gitleaks?

Gitleaks is a secret scanning tool that detects hardcoded credentials, tokens, keys, and secrets in Git repositories. It can scan working trees, commits, and history.

In DevSecOps, use it in pre-commit hooks, pull request checks, and CI pipelines. If a secret is found, rotate it immediately because Git history may already be copied.

### Q62. What is TruffleHog?

TruffleHog is a secret scanning tool that can scan repositories, filesystems, and other sources for secrets. It can also verify some secrets, which helps reduce false positives.

Interview point: secret scanning is prevention and detection, but remediation requires rotation, audit, and migration to secrets managers.

### Q63. What is Checkov?

Checkov is an IaC scanning tool that checks Terraform, CloudFormation, Kubernetes YAML, Helm, Dockerfile, and other configuration files for misconfigurations and compliance violations.

Example findings include public S3 buckets, unencrypted storage, open security groups, weak IAM policies, and missing logging.

### Q64. What is tfsec?

tfsec is a Terraform security scanner. It detects insecure Terraform configurations such as public ingress, disabled encryption, weak logging, and overly permissive policies. It is often used in CI before Terraform plan or apply.

The value is stopping cloud misconfigurations before resources are deployed.

### Q65. What is Terraform?

Terraform is an Infrastructure as Code tool used to define and provision cloud resources through configuration files. It supports many providers, including AWS, Azure, GCP, Kubernetes, and SaaS platforms.

Security concerns include secrets in variables/state, overly permissive IAM, public resources, state file protection, module trust, and drift.

### Q66. What is Jenkins?

Jenkins is an automation server commonly used for CI/CD. It can build, test, scan, and deploy software through pipelines. Security concerns include plugin vulnerabilities, credential exposure, weak access control, unsafe scripts, and over-privileged build agents.

Good Jenkins security includes RBAC, credential binding, plugin patching, agent isolation, least privilege, audit logs, and avoiding secrets in console output.

### Q67. What is GitHub Actions?

GitHub Actions is GitHub's CI/CD automation platform. Workflows are defined in YAML and can run on events like push, pull request, or release. Security concerns include third-party actions, token permissions, secrets in logs, pull request workflows from forks, and deployment approvals.

Best practices include pinning actions, limiting `GITHUB_TOKEN` permissions, using environments, protecting branches, and scanning workflows.

### Q68. What is Azure DevOps?

Azure DevOps provides repositories, pipelines, boards, artifacts, and test plans. For DevSecOps, Azure Pipelines can run security scans, deploy to Azure, manage approvals, and integrate with Defender for Cloud or third-party tools.

Security focus areas include service connections, variable groups, secret variables, environment approvals, RBAC, and pipeline permissions.

### Q69. What is GitLab CI/CD?

GitLab CI/CD is GitLab's pipeline system. It supports stages, jobs, runners, variables, artifacts, environments, and security scanning features. Security concerns include runner isolation, protected variables, secrets, third-party images, and deployment permissions.

In interviews, connect GitLab CI to SAST, DAST, dependency scanning, container scanning, secret detection, and merge request gates.

### Q70. What is a WAF?

WAF means Web Application Firewall. It filters HTTP traffic to block or monitor common web attacks like SQL injection, XSS, malicious bots, and suspicious request patterns. Examples include AWS WAF, Azure Web Application Firewall, Google Cloud Armor, Cloudflare WAF, and F5.

WAF is not a replacement for secure code. It is a compensating control and detection layer.

## Section 5: CI/CD Security

### Q71. Where do you add security in a CI/CD pipeline?

Add security at multiple stages: pre-commit secret scanning, pull request SAST and IaC scanning, build-time SCA, container image scanning, artifact signing, staging DAST, deployment approvals, production runtime monitoring, and post-deployment vulnerability management.

The goal is layered security. Fast checks should run early; slower or intrusive checks can run nightly or in staging.

### Q72. What is a security gate?

A security gate is a rule that blocks or requires approval before code moves forward. Example gates include blocking critical vulnerabilities, leaked secrets, public cloud resources, unsigned artifacts, or high-risk policy violations.

Good gates are risk-based. If gates are too strict or noisy, developers may bypass them. Tune thresholds and provide clear remediation guidance.

### Q73. What is a false positive?

A false positive is a scanner finding that appears to be a vulnerability but is not exploitable or not valid in context. For example, a SAST tool may flag a value as SQL injection even though it is safely parameterized later.

Triage involves understanding code flow, runtime context, reachability, data sensitivity, and exploitability. Never dismiss findings only because a developer says so; validate evidence.

### Q74. What is a false negative?

A false negative is when a tool fails to detect a real vulnerability. This is common with business logic flaws, authorization bugs, chained attacks, and custom framework behavior.

This is why DevSecOps needs both automated tools and manual review. Tools are coverage helpers, not complete replacements for security thinking.

### Q75. How do you handle a critical finding in CI?

First confirm whether the finding is valid and reachable. If valid and high impact, block deployment or require security approval based on policy. Notify the responsible team, create a ticket with evidence and remediation, identify owner and SLA, and track until closure.

If already deployed, assess exposure, check logs for exploitation, apply mitigation, patch or rollback, and add preventive controls so the issue does not recur.

### Q76. How do you protect secrets in CI/CD?

Store secrets in secret managers or CI secret stores, not in code. Limit secret access to required jobs and protected branches. Mask secrets in logs, rotate regularly, avoid echoing variables, use short-lived credentials where possible, and prefer workload identity or OIDC federation instead of long-lived cloud keys.

Also scan repositories and logs for accidental exposure. If leaked, rotate immediately and investigate use.

### Q77. What is artifact signing?

Artifact signing means cryptographically signing build outputs such as container images, packages, or binaries so consumers can verify authenticity and integrity. It helps prevent tampering and supply-chain attacks.

Tools and concepts include Cosign, Sigstore, in-toto, SLSA, provenance, and trusted build pipelines.

### Q78. What is SBOM?

SBOM means Software Bill of Materials. It is an inventory of software components, dependencies, versions, and sometimes licenses included in an application or image.

SBOM helps vulnerability management because teams can quickly identify whether they are affected by a vulnerable component. Common formats include CycloneDX and SPDX.

### Q79. What is supply-chain security?

Supply-chain security protects the dependencies, build systems, CI/CD workflows, artifacts, registries, and deployment process used to deliver software. Risks include dependency confusion, compromised packages, malicious maintainers, build tampering, and leaked signing keys.

Controls include dependency pinning, trusted registries, SCA, SBOM, artifact signing, provenance, branch protection, least privilege CI tokens, and reviewed third-party actions.

### Q80. How do you secure Git repositories?

Use branch protection, mandatory reviews, signed commits where appropriate, secret scanning, CODEOWNERS, least-privilege access, protected tags, dependency alerts, status checks, and audit logs. Disable force pushes on protected branches and review third-party integrations.

Also educate developers not to commit secrets, large sensitive files, production dumps, or private keys.

## Section 6: Container And Kubernetes Security

### Q81. What is Docker?

Docker is a platform for building and running containers. A container packages application code, runtime, libraries, and dependencies into an isolated process environment.

Security concerns include vulnerable base images, running as root, secrets in images, excessive Linux capabilities, writable filesystems, exposed ports, and untrusted registries.

### Q82. How do you secure a Dockerfile?

Use minimal trusted base images, pin versions, run as non-root, avoid secrets in layers, reduce packages, update dependencies, use multi-stage builds, define health checks where useful, and avoid unnecessary privileges.

After building, scan the image with tools like Trivy, Grype, Snyk, or Docker Scout. Also sign and store images in a trusted registry.

### Q83. What is container image scanning?

Container image scanning analyzes image layers, OS packages, language dependencies, secrets, and misconfigurations to identify vulnerabilities and risks. It should run during CI before images are pushed or deployed.

Results should be prioritized based on severity, fix availability, exploitability, and whether the affected package is actually used at runtime.

### Q84. What is Kubernetes?

Kubernetes is a container orchestration platform. It manages container deployment, scaling, networking, service discovery, storage, and availability. Key objects include pods, deployments, services, ingress, configmaps, secrets, namespaces, and service accounts.

Security focus areas include RBAC, network policies, pod security, image security, secrets, admission control, audit logs, and cluster upgrades.

### Q85. What is Kubernetes RBAC?

Kubernetes RBAC controls which users, groups, or service accounts can perform actions on resources. It uses Roles, ClusterRoles, RoleBindings, and ClusterRoleBindings.

Least privilege is critical. Avoid binding service accounts to cluster-admin unless absolutely necessary. Review permissions regularly and separate namespace-level access from cluster-wide access.

### Q86. What are Kubernetes Secrets?

Kubernetes Secrets store sensitive values like tokens, passwords, or certificates. By default, they are base64-encoded, not strongly encrypted unless encryption at rest is configured.

Best practices include enabling encryption at rest, restricting RBAC access, using external secret managers, avoiding secret exposure through environment dumps, and rotating secrets.

### Q87. What is a Kubernetes NetworkPolicy?

NetworkPolicy controls traffic between pods and namespaces. By default, many clusters allow broad pod-to-pod communication. Network policies help enforce segmentation and reduce lateral movement.

Example: only allow the frontend namespace to talk to the backend service on a specific port, and only allow backend to talk to the database.

### Q88. What is admission control?

Admission control evaluates requests to the Kubernetes API before objects are created or modified. It can enforce policies such as no privileged containers, required labels, allowed registries, non-root users, and resource limits.

Tools include Kubernetes built-in admission controllers, OPA Gatekeeper, Kyverno, and cloud provider policy integrations.

### Q89. What is runtime container security?

Runtime container security monitors container behavior after deployment. It can detect suspicious processes, file modifications, network connections, privilege escalation, crypto mining, or shell execution.

Tools include Falco, Sysdig, Aqua, Prisma Cloud, and cloud-native runtime security services.

### Q90. What do you check before deploying a container to production?

Check base image trust, vulnerabilities, secrets, non-root user, exposed ports, image size, dependency versions, configuration, environment variables, resource limits, health checks, network policies, RBAC, image signature, and deployment approvals.

Also verify logs, monitoring, rollback strategy, and whether runtime policies are in place.

## Section 7: Cloud Security

### Q91. What is IAM?

IAM means Identity and Access Management. It controls who can access what resources and what actions they can perform. In AWS this includes users, groups, roles, policies, and permissions. In Azure it includes Entra ID identities and Azure RBAC. In GCP it includes principals, roles, policies, and service accounts.

IAM is one of the most important cloud security areas because compromised or over-privileged identities can lead to full cloud compromise.

### Q92. What is RBAC?

RBAC means Role-Based Access Control. Permissions are assigned to roles, and users or services receive roles based on job function. It is easier to manage than assigning permissions individually to every user.

RBAC should follow least privilege. Avoid broad admin roles and regularly review role assignments.

### Q93. What is a service account?

A service account is a non-human identity used by applications, workloads, pipelines, or services to access resources. Service accounts need careful permission control because they are often used automatically and may have powerful access.

Best practices include least privilege, short-lived credentials, workload identity, key rotation, avoiding hardcoded keys, and monitoring usage.

### Q94. What is cloud logging?

Cloud logging captures activity and events from cloud resources, identities, networks, and services. AWS CloudTrail records API activity. Azure Activity Logs and Entra logs track management and identity events. GCP Cloud Audit Logs track admin, data access, and system events.

Logging is essential for detection, investigation, compliance, and incident response. Logs should be centralized, protected from tampering, retained appropriately, and monitored.

### Q95. What is AWS CloudTrail?

AWS CloudTrail records AWS API calls and account activity. It helps answer who did what, from where, when, and against which resource. It is critical for auditing and incident response.

Best practices include enabling CloudTrail across all regions, sending logs to a protected S3 bucket, enabling log file validation, integrating with CloudWatch or SIEM, and monitoring high-risk events.

### Q96. What is AWS GuardDuty?

AWS GuardDuty is a threat detection service that analyzes sources like CloudTrail events, VPC Flow Logs, DNS logs, EKS audit logs, and other telemetry to detect suspicious activity.

Examples include credential compromise indicators, unusual API calls, crypto mining, reconnaissance, and suspicious network behavior. GuardDuty findings need triage and response playbooks.

### Q97. What is AWS Security Hub?

AWS Security Hub aggregates security findings and checks AWS accounts against security standards and best practices. It can collect findings from GuardDuty, Inspector, IAM Access Analyzer, Macie, and third-party tools.

It helps centralize cloud posture and vulnerability findings, but teams still need ownership, prioritization, and remediation workflows.

### Q98. What is AWS KMS?

AWS KMS is Key Management Service. It creates and manages cryptographic keys used to encrypt data in AWS services and applications. KMS supports key policies, IAM integration, rotation, audit logs, and envelope encryption.

Interview focus: encryption is only as strong as key access. Restrict who can use and administer keys, monitor key usage, and separate duties.

### Q99. What is Azure Key Vault?

Azure Key Vault stores secrets, keys, and certificates. Applications can access it using managed identities instead of hardcoded credentials. It integrates with Azure RBAC and access policies.

Best practices include private endpoints, logging, soft delete, purge protection, least privilege, and rotation.

### Q100. What is GCP Secret Manager?

GCP Secret Manager stores API keys, passwords, certificates, and other secrets. It supports IAM-based access, versioning, audit logging, and integration with GCP workloads.

Use service accounts with least privilege and avoid embedding service account keys in source code or CI variables when workload identity is available.

### Q101. How do you secure AWS S3?

Enable S3 Block Public Access unless public access is explicitly required. Use bucket policies carefully, disable legacy ACLs where possible, enable encryption, enable versioning for important data, configure access logging or CloudTrail data events, restrict access with IAM, and monitor using IAM Access Analyzer, Macie, Security Hub, and Config.

Also classify data and avoid storing secrets or credentials in buckets. For public static websites, separate public content from private data.

### Q102. How do you secure Azure Storage?

Disable anonymous public access unless required, use private endpoints where possible, restrict network access, use managed identities, rotate account keys, prefer Azure AD authorization over shared keys, configure encryption, enable logging, and use Defender for Cloud recommendations.

Be careful with Shared Access Signatures. Use short expiry, limited permissions, HTTPS-only, and avoid broad account-level SAS tokens.

### Q103. How do you secure GCP Cloud Storage?

Use uniform bucket-level access, public access prevention, least-privilege IAM, encryption, retention policies where needed, audit logs, and organization policies. Avoid granting `allUsers` or `allAuthenticatedUsers` unless intentionally public.

For sensitive buckets, restrict service accounts, monitor access, and classify data.

### Q104. What is CSPM?

CSPM means Cloud Security Posture Management. It continuously checks cloud environments for misconfigurations, compliance issues, risky permissions, exposed resources, and security best-practice violations.

Examples include Prisma Cloud, Wiz, Orca, Lacework, AWS Security Hub, Azure Defender for Cloud, and GCP Security Command Center. CSPM helps detect drift after deployment.

### Q105. What is cloud network security?

Cloud network security controls traffic between internet, cloud services, subnets, workloads, and private networks. It includes VPC/VNet design, subnets, route tables, security groups, NSGs, firewall rules, NACLs, private endpoints, load balancers, VPNs, and WAF.

The goal is least exposure: only required ports should be open, admin access should be restricted, and sensitive systems should not be directly internet-facing.

## Section 8: IaC And Policy

### Q106. What is Infrastructure as Code?

Infrastructure as Code means managing infrastructure using code files instead of manual console changes. Examples include Terraform, CloudFormation, ARM/Bicep, Pulumi, Kubernetes YAML, and Helm.

Security benefit: infrastructure can be reviewed, scanned, version-controlled, approved, and reproduced. Risk: insecure code can deploy insecure infrastructure quickly.

### Q107. What is IaC scanning?

IaC scanning checks infrastructure code for insecure configurations before deployment. Examples include public buckets, open security groups, disabled encryption, missing logging, weak IAM, and unrestricted Kubernetes pods.

Tools include Checkov, tfsec, Terrascan, KICS, Snyk IaC, and Prisma Cloud. It is a key shift-left cloud security control.

### Q108. What is policy as code?

Policy as code means defining security and compliance rules in machine-readable code so they can be automatically enforced. Examples include "S3 buckets must not be public", "containers must not run privileged", and "all disks must be encrypted".

Tools include OPA, Conftest, Gatekeeper, Kyverno, HashiCorp Sentinel, and cloud-native policy engines.

### Q109. What is OPA?

OPA means Open Policy Agent. It is a general-purpose policy engine that evaluates policies written in Rego. It can be used with Kubernetes, CI/CD, APIs, and infrastructure tools.

OPA allows organizations to enforce consistent policies across systems, such as deployment rules, access decisions, and configuration requirements.

### Q110. What is drift?

Drift happens when actual infrastructure differs from the code definition. For example, Terraform says a security group allows only port 443, but someone manually opens port 22 in the cloud console.

Drift is risky because reviewed code no longer reflects real infrastructure. Controls include restricted manual access, drift detection, Terraform plan reviews, CSPM, and change management.

## Section 9: Conditional And Scenario Questions

### Q111. A developer committed AWS keys to Git. What will you do?

First treat the keys as compromised. Revoke or rotate them immediately. Check cloud logs such as CloudTrail to see whether the keys were used and from where. Remove the secret from the repository and history if required, but remember removal does not make the old key safe. Create an incident ticket, notify stakeholders, identify impact, and document actions.

Prevention includes secret scanning, pre-commit hooks, CI secret scanning, developer training, using IAM roles/OIDC instead of long-lived keys, and storing secrets in a secret manager.

### Q112. SAST reports SQL injection but developer says it is false positive. What do you do?

Review the code path and confirm whether untrusted input reaches a SQL query. Check whether parameterized queries are used correctly. If the input is sanitized, parameterized, and not executable, document why it is false positive. If uncertain, create a small proof in a safe test environment or ask for peer review.

The key is balanced triage. Do not blindly accept scanner output, and do not blindly dismiss developer feedback.

### Q113. DAST finds missing security headers. Is it high severity?

Usually missing security headers are low to medium severity depending on the header and application context. For example, missing CSP may increase XSS impact, but it is not the same as confirmed XSS. Missing HSTS on a sensitive HTTPS app can be more important if HTTP access is possible.

Severity should consider exploitability, impact, existing controls, data sensitivity, and whether the issue enables a larger attack.

### Q114. A public S3 bucket is found. What do you do?

First verify whether it is intentionally public. If not, restrict public access immediately using Block Public Access, bucket policy changes, or IAM updates. Identify what data was exposed, check access logs or CloudTrail data events if enabled, notify stakeholders, and assess regulatory impact.

Prevent recurrence with IaC scanning, SCPs, AWS Config rules, Security Hub controls, IAM Access Analyzer, and bucket creation guardrails.

### Q115. A container image has many critical CVEs. What do you do?

Prioritize by exploitability, fix availability, runtime exposure, internet exposure, and package usage. Update the base image, rebuild dependencies, remove unused packages, and rescan. If the fix is not available, consider compensating controls, runtime restrictions, or temporary exception with owner and expiry.

Do not simply ignore all CVEs because they are in base images. Base image hygiene is part of container security.

### Q116. A pipeline needs production cloud admin permissions. Is that acceptable?

Usually no. A pipeline should have only the permissions required for deployment. Full admin permissions create huge blast radius if CI is compromised. Use scoped roles, separate environments, approval gates, short-lived credentials, OIDC federation, and audit logs.

If admin is temporarily required for bootstrapping, document it, time-bound it, and remove it after setup.

### Q117. How do you respond to suspected cloud credential compromise?

Disable or rotate the credential, identify all actions taken with it using audit logs, check created users/roles/keys/resources, look for persistence, isolate affected workloads, preserve evidence, and notify stakeholders. Then remediate root cause and add prevention.

Cloud incidents often involve IAM persistence, new access keys, modified policies, public snapshots, data exfiltration, and crypto mining resources.

### Q118. A critical CVE is announced for a dependency your company uses. What do you do?

Identify affected applications using SCA, SBOM, asset inventory, package manifests, and container scans. Confirm versions and exploitability. Prioritize internet-facing and sensitive systems. Patch, upgrade, or apply vendor mitigation. Deploy through emergency change if needed. Monitor for exploitation and document actions.

Afterward, improve dependency inventory, alerting, and patch SLAs.

### Q119. How do you explain risk to a developer?

Use clear impact and evidence. Avoid blame. Example: "This endpoint lets any authenticated user access another user's invoice by changing the ID. That can expose personal data, so we need a server-side ownership check before release."

Good security communication is specific, reproducible, prioritized, and solution-oriented.

### Q120. How do you handle disagreements on severity?

Use a risk-based discussion. Consider CVSS, exploitability, exposure, data sensitivity, business impact, compensating controls, and evidence. If disagreement remains, involve application owner, security lead, or risk committee.

The goal is not to win an argument. The goal is accurate risk treatment: fix, mitigate, accept, transfer, or avoid.

## Section 10: Deep Concepts Interviewers Chain Together

### Q121. How can SSRF lead to cloud compromise?

If an application running in cloud can be tricked into requesting internal metadata services, SSRF may expose temporary credentials assigned to the workload. Those credentials can then be used to access cloud APIs depending on their permissions. If the role is over-privileged, impact can include data access, privilege escalation, or resource manipulation.

Controls include SSRF prevention in the app, metadata service hardening such as AWS IMDSv2, least-privilege instance roles, egress filtering, cloud logging, and detection of unusual credential use.

### Q122. How can a leaked CI token become a production incident?

A CI token may access repositories, secrets, artifacts, deployment environments, or cloud accounts. If leaked, an attacker could modify code, inject malicious dependencies, read secrets, publish malicious artifacts, or deploy to production.

Controls include least-privilege tokens, short-lived credentials, branch protection, environment approvals, audit logs, secret scanning, token rotation, and artifact signing.

### Q123. How does SCA connect to container scanning?

SCA checks application dependencies such as npm, Maven, pip, or NuGet packages. Container scanning checks OS packages, application dependencies, secrets, and image configuration inside the container image. Both are needed because an app can be safe at source dependency level but vulnerable due to the base image, or vice versa.

In a strong pipeline, run SCA before build and container scanning after image build.

### Q124. How does IaC scanning connect to cloud security?

IaC scanning catches cloud misconfigurations before deployment. Cloud security tools like CSPM catch issues after deployment, including manual drift. Together they provide preventive and detective control.

Example: Checkov blocks a public bucket in Terraform; Security Hub or Defender for Cloud detects if someone later makes storage public manually.

### Q125. How does Kubernetes security connect to cloud IAM?

Managed Kubernetes clusters run in cloud environments and often use cloud IAM for nodes, workloads, storage, registries, and control-plane integrations. If Kubernetes service accounts or node roles are over-privileged, a pod compromise can become cloud compromise.

Controls include workload identity, least-privilege cloud roles, RBAC, network policies, pod security, image scanning, and audit logging.

## Section 11: More Quick But Detailed Interview Q&A

### Q126. What is CVE?

CVE means Common Vulnerabilities and Exposures. It is a publicly known vulnerability identifier, such as `CVE-2021-44228`. CVEs help standardize tracking across tools, vendors, and organizations.

A CVE ID alone does not define your actual risk. You still need to check whether your environment uses the affected version, whether the vulnerable code path is reachable, and whether exploitation is possible.

### Q127. What is CWE?

CWE means Common Weakness Enumeration. It categorizes types of software weaknesses, such as SQL injection, improper authentication, or path traversal. CVE identifies a specific vulnerability; CWE identifies the weakness category.

SAST tools often map findings to CWE because they detect patterns of weakness in code.

### Q128. What is CVSS?

CVSS means Common Vulnerability Scoring System. It gives a numerical severity score based on factors like attack vector, complexity, privileges required, user interaction, scope, confidentiality, integrity, and availability impact.

CVSS helps prioritization, but it should not be the only input. Asset criticality, exposure, exploit availability, and business context matter too.

### Q129. What is threat modeling?

Threat modeling is a structured process to identify what can go wrong in a system and how to reduce risk. It is usually done during design or major change review.

Common methods include STRIDE, attack trees, data flow diagrams, and abuse cases. Output should include threats, mitigations, assumptions, and security requirements.

### Q130. What is STRIDE?

STRIDE is a threat modeling framework: Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, and Elevation of Privilege.

It helps teams think systematically. For example, for an API, spoofing maps to authentication, tampering maps to integrity, repudiation maps to logs, information disclosure maps to encryption and access control, denial of service maps to rate limits, and elevation of privilege maps to authorization.

### Q131. What is DAST?

DAST means Dynamic Application Security Testing. It tests a running application from the outside by sending HTTP requests and observing responses. Tools include OWASP ZAP, Burp Suite Scanner, Acunetix, Invicti, and StackHawk.

DAST can find runtime issues like missing headers, injection behavior, exposed files, and some auth/session issues. It may miss code paths that require complex authentication or business logic.

### Q132. What is SAST?

SAST means Static Application Security Testing. It analyzes source code or compiled code without running the application. Tools include SonarQube, Semgrep, CodeQL, Fortify, Checkmarx, and Veracode.

SAST is good for early detection but can produce false positives. It is strongest when tuned to the codebase and integrated into developer workflows.

### Q133. What is SCA?

SCA means Software Composition Analysis. It identifies open-source dependencies, versions, licenses, and known vulnerabilities. Tools include Snyk, OWASP Dependency-Check, Mend, Dependabot, Renovate, and GitHub Dependabot alerts.

SCA is important because modern applications depend heavily on third-party packages.

### Q134. What is IAST?

IAST means Interactive Application Security Testing. It observes application behavior from inside while the application is running, often through an agent during testing. It combines runtime context with security analysis.

IAST can reduce false positives because it sees actual executed code paths, but it requires integration with the application runtime.

### Q135. What is RASP?

RASP means Runtime Application Self-Protection. It runs inside or alongside an application to detect and sometimes block attacks at runtime. It can observe actual application behavior more deeply than a network WAF.

RASP can be useful as defense in depth, but it does not replace secure coding or proper testing.

### Q136. What is secret management?

Secret management is the secure storage, access, rotation, and auditing of sensitive values such as passwords, API keys, tokens, certificates, and encryption keys. Tools include AWS Secrets Manager, Azure Key Vault, GCP Secret Manager, HashiCorp Vault, Doppler, and CyberArk.

The key principle is that secrets should not live in source code, images, logs, or local files. Access should be least privilege and auditable.

### Q137. What is HashiCorp Vault?

HashiCorp Vault is a secrets management and encryption platform. It can store static secrets, generate dynamic credentials, manage leases, rotate secrets, and provide encryption as a service.

It is useful in DevSecOps for centralizing secrets and reducing long-lived credentials across applications and pipelines.

### Q138. What is a vulnerability management process?

Vulnerability management is the process of discovering, prioritizing, assigning, remediating, validating, and reporting vulnerabilities. Sources include scanners, pentests, bug bounty, cloud posture tools, SCA, SAST, DAST, and advisories.

A mature process includes SLAs, asset inventory, risk-based prioritization, exception handling, retesting, and metrics.

### Q139. What is an SLA in vulnerability management?

An SLA is a time target for fixing vulnerabilities based on severity or risk. For example, critical issues may require remediation within 7 days, high within 30 days, medium within 60 days, and low within 90 days.

Actual SLA depends on business policy, exposure, exploitability, and compensating controls.

### Q140. What is risk acceptance?

Risk acceptance means the business knowingly accepts a security risk instead of fixing it immediately. It should include justification, owner, expiry date, compensating controls, and approval from appropriate authority.

Security teams should not casually close issues as accepted without documentation.

### Q141. What is compensating control?

A compensating control is an alternate control used when the primary fix is not immediately possible. For example, if a vulnerable service cannot be patched today, a WAF rule, network restriction, or feature disablement may reduce risk temporarily.

Compensating controls should be time-bound and should not replace permanent remediation forever.

### Q142. What is SIEM?

SIEM means Security Information and Event Management. It collects, correlates, and analyzes logs from systems, applications, cloud, network, and security tools to detect threats and support investigations.

Examples include Splunk, Microsoft Sentinel, QRadar, Elastic Security, and Google Chronicle.

### Q143. What is SOAR?

SOAR means Security Orchestration, Automation, and Response. It automates response workflows such as enriching alerts, opening tickets, disabling accounts, blocking IPs, or notifying teams.

SOAR helps reduce manual repetitive work, but response actions should be carefully controlled to avoid accidental disruption.

### Q144. What is EDR?

EDR means Endpoint Detection and Response. It monitors endpoints like laptops and servers for suspicious behavior, malware, process activity, and attack techniques.

Examples include CrowdStrike, Microsoft Defender for Endpoint, SentinelOne, and Carbon Black. DevSecOps may interact with EDR for server/container host monitoring and incident response.

### Q145. What is CSP?

Content Security Policy is a browser security header that controls which scripts, styles, images, frames, and other resources a page can load. It helps reduce XSS impact.

CSP must be carefully tested because overly strict policies can break applications, while overly broad policies provide little protection.

### Q146. What is path traversal?

Path traversal occurs when an attacker manipulates file paths to access files outside an intended directory. For example, a download feature may allow reading sensitive system or application files if it fails to validate paths.

Fixes include allowlisting file IDs, canonical path validation, avoiding direct user-controlled paths, and enforcing access control.

### Q147. What is command injection?

Command injection occurs when user input is passed into operating system commands unsafely. Impact can be severe, including remote command execution.

Fixes include avoiding shell execution, using safe libraries/APIs, strict allowlists, parameter separation, least-privileged service accounts, and sandboxing.

### Q148. What is clickjacking?

Clickjacking tricks users into clicking hidden or framed UI elements on another site. It can cause unintended actions if the application can be embedded in an attacker's page.

Fixes include `X-Frame-Options` or CSP `frame-ancestors`.

### Q149. What is open redirect?

Open redirect occurs when an application redirects users to attacker-controlled URLs based on unvalidated input. It is often used for phishing, OAuth abuse, or chaining with other vulnerabilities.

Fixes include relative redirects, allowlisted destinations, and avoiding user-controlled redirect targets.

### Q150. What is business logic vulnerability?

A business logic vulnerability occurs when application workflows can be abused in ways that scanners may not detect. Examples include applying a coupon multiple times, bypassing payment steps, changing order ownership, or abusing refund logic.

Testing requires understanding the business process, roles, state transitions, and negative cases. Automation alone rarely finds these reliably.

## Section 12: Final Rapid Revision Questions

### Q151. What is AppSec?

Application Security is the practice of securing software applications through secure design, coding, testing, deployment, and monitoring. It includes OWASP risks, secure SDLC, code review, dependency management, and vulnerability remediation.

### Q152. What is CloudSec?

Cloud Security is the practice of securing cloud environments, workloads, identities, networks, data, and services. It includes IAM, logging, encryption, network security, posture management, workload protection, and incident response.

### Q153. What is DevSecOps engineer responsible for?

A DevSecOps engineer integrates security into development and operations workflows. Responsibilities include security scanning in CI/CD, cloud and IaC security, vulnerability management, secrets management, container security, developer enablement, policy automation, and security monitoring.

### Q154. What is the difference between AppSec and DevSecOps?

AppSec focuses mainly on application-level security: secure code, vulnerabilities, testing, and design. DevSecOps focuses on embedding security automation and controls into DevOps pipelines and operations. They overlap heavily in modern teams.

### Q155. What is the difference between CloudSec and DevSecOps?

CloudSec focuses on securing cloud platforms and workloads. DevSecOps focuses on securing the software delivery lifecycle. They connect through IaC, CI/CD deployments, cloud IAM, containers, and runtime monitoring.

### Q156. How do you prioritize vulnerabilities?

Use severity, exploitability, asset criticality, internet exposure, sensitive data, exploit availability, business impact, and compensating controls. Prioritize confirmed, exploitable, internet-facing, high-impact issues first.

### Q157. What is a secure SDLC?

Secure SDLC is an SDLC with security activities built into every phase: requirements, design, coding, testing, release, and maintenance. It includes threat modeling, secure coding, code review, security testing, release gates, monitoring, and remediation.

### Q158. What is API security?

API security protects APIs from unauthorized access, data exposure, injection, abuse, and business logic flaws. Key areas include authentication, authorization, rate limiting, input validation, schema validation, logging, and token security.

### Q159. What is zero trust?

Zero trust is a security model based on "never trust, always verify." It assumes networks are not automatically trusted and requires strong identity, device posture, least privilege, segmentation, and continuous verification.

### Q160. What is encryption at rest and in transit?

Encryption at rest protects stored data, such as database records, disks, and object storage. Encryption in transit protects data moving over networks, usually using TLS. Both are required for strong data protection.

