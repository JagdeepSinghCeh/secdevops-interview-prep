# DevSecOps Foundations Deep Q&A

This file expands the absolute basics. These are the questions interviewers ask when they want to confirm your foundation is real before going into DevSecOps tools and cloud security.

## Computing And Internet Basics

### Q1. What is a computer system?

A computer system is a combination of hardware, operating system, software, network, storage, and users working together to process data. In security, we care about every part because each can introduce risk. Hardware can be stolen, operating systems can be misconfigured, software can have vulnerabilities, networks can be exposed, storage can leak data, and users can be phished.

For DevSecOps, understanding systems helps you secure applications from code to deployment. A web app is not just code; it also depends on servers, containers, secrets, logs, cloud resources, IAM, networks, and pipelines.

### Q2. What is a server?

A server is a system or program that provides services to clients over a network. A web server serves web pages or APIs. A database server stores and retrieves data. An authentication server verifies identity.

In cloud and DevSecOps interviews, servers may be physical machines, virtual machines, containers, or serverless functions. Security checks include patching, hardening, access control, logging, encryption, network exposure, and least privilege.

### Q3. What is a client?

A client is a system or application that requests services from a server. A browser requesting a website is a client. A mobile app calling an API is also a client. A backend service calling another service can also act as a client.

Security point: never fully trust the client. Client-side validation, hidden buttons, disabled fields, and UI restrictions can be bypassed. Sensitive authorization must happen server-side.

### Q4. What is an application?

An application is software designed to perform a function for users or systems. Examples include banking apps, e-commerce websites, APIs, internal dashboards, and mobile apps.

Application security means protecting the application from vulnerabilities in design, code, dependencies, configuration, authentication, authorization, and runtime behavior.

### Q5. What is a web application?

A web application is an application accessed through a browser or HTTP client. It usually has a frontend, backend, database, APIs, and authentication system.

Common web security risks include broken access control, XSS, SQL injection, CSRF, SSRF, insecure session management, file upload flaws, and security misconfiguration.

### Q6. What is a browser?

A browser is a client application used to access web content. It sends HTTP/HTTPS requests and renders HTML, CSS, JavaScript, images, and other resources.

Browser security matters because attacks like XSS, clickjacking, CSRF, CORS misconfiguration, cookie theft, and mixed content happen in or through browser behavior.

### Q7. What happens when you type a URL in a browser?

The browser parses the URL, checks cache, resolves the domain using DNS, connects to the server IP, performs a TCP handshake, negotiates TLS for HTTPS, sends an HTTP request, receives an HTTP response, renders the page, and loads additional resources like JavaScript, CSS, images, and API calls.

Security checks exist at many points: DNS security, TLS certificate, HTTP headers, cookies, authentication, authorization, CORS, CSP, and server-side input handling.

### Q8. What is a URL?

URL means Uniform Resource Locator. It identifies the location of a resource on the internet or network. A URL usually includes scheme, host, port, path, query string, and fragment.

Example: `https://example.com:443/api/users?id=10#profile`. Here `https` is the scheme, `example.com` is the host, `443` is the port, `/api/users` is the path, `id=10` is the query, and `profile` is the fragment.

Security relevance: attackers test paths, parameters, query strings, redirects, and URL parsing differences for vulnerabilities like IDOR, SSRF, open redirect, and injection.

### Q9. What is DNS?

DNS means Domain Name System. It converts domain names like `example.com` into IP addresses like `93.184.216.34`.

Security relevance: DNS can be abused through phishing domains, DNS hijacking, subdomain takeover, misconfigured records, and SSRF bypasses. During testing, DNS helps identify subdomains, services, and external attack surface.

### Q10. What is an IP address?

An IP address is a numerical address used to identify a device or service on a network. IPv4 addresses look like `192.168.1.10`, while IPv6 addresses are longer, like `2001:db8::1`.

Security relevance: IP addresses are used in firewall rules, access control lists, logging, threat detection, and network segmentation. Public IPs are reachable from the internet; private IPs are used inside internal networks.

### Q11. What is a private IP address?

A private IP address is used inside internal networks and is not directly routable on the public internet. Common private IPv4 ranges include `10.0.0.0/8`, `172.16.0.0/12`, and `192.168.0.0/16`.

Security relevance: private IPs matter in SSRF and network segmentation. A public user should not be able to make a vulnerable server access internal private IPs.

### Q12. What is localhost?

Localhost refers to the current machine. The IPv4 address is usually `127.0.0.1`, and IPv6 is `::1`.

Security relevance: SSRF vulnerabilities often try to access localhost services from the server's point of view. If an internal admin panel listens only on localhost, SSRF may still reach it if the vulnerable application runs on the same host.

### Q13. What is a port?

A port is a logical communication endpoint used by network services. For example, HTTP commonly uses port 80, HTTPS uses 443, SSH uses 22, DNS uses 53, and RDP uses 3389.

Security relevance: open ports define attack surface. Unnecessary exposed ports increase risk. During cloud review, check security groups, NSGs, firewall rules, and public IP exposure.

### Q14. What is TCP?

TCP means Transmission Control Protocol. It is connection-oriented and reliable. It ensures packets arrive in order and retransmits lost packets.

Common services using TCP include HTTP, HTTPS, SSH, SMTP, and databases. Security tools like Nmap often scan TCP ports to identify exposed services.

### Q15. What is UDP?

UDP means User Datagram Protocol. It is connectionless and faster but does not guarantee delivery or order. DNS, DHCP, streaming, and some VPN protocols use UDP.

Security relevance: UDP services can be abused in DDoS amplification attacks if misconfigured. UDP scanning is also different from TCP scanning because there is no handshake.

### Q16. What is HTTP?

HTTP means Hypertext Transfer Protocol. It is the protocol used by browsers and clients to communicate with web servers. HTTP requests include methods like GET, POST, PUT, PATCH, and DELETE. Responses include status codes like 200, 301, 403, 404, and 500.

Security relevance: most AppSec testing involves manipulating HTTP requests and responses. Tools like Burp Suite and OWASP ZAP help inspect and modify HTTP traffic.

### Q17. What is HTTPS?

HTTPS is HTTP over TLS. It encrypts data in transit between client and server, protecting confidentiality and integrity against network attackers.

Security relevance: HTTPS prevents sniffing and tampering on the network, but it does not automatically make the application secure. A site can use HTTPS and still have SQL injection, IDOR, XSS, or broken access control.

### Q18. What is TLS?

TLS means Transport Layer Security. It provides encryption, integrity, and authentication for network communication. It replaced older SSL protocols.

Security checks include certificate validity, supported protocol versions, cipher suites, HSTS, and whether weak protocols like TLS 1.0 or TLS 1.1 are disabled.

### Q19. What is an HTTP request?

An HTTP request is sent by a client to a server. It contains method, path, headers, cookies, query parameters, and sometimes a body.

Example parts include `GET /profile?id=10`, `Host`, `Authorization`, `Cookie`, and JSON body. Security testing often manipulates these fields to test authentication, authorization, injection, and business logic.

### Q20. What is an HTTP response?

An HTTP response is sent by the server to the client. It contains a status code, headers, cookies, and response body.

Security testers review responses for sensitive data leakage, security headers, error messages, authorization bypass, token exposure, and inconsistent behavior.

### Q21. What are HTTP methods?

HTTP methods describe the intended action. GET retrieves data, POST creates or submits data, PUT replaces data, PATCH updates partially, and DELETE removes data. OPTIONS may show allowed methods.

Security relevance: testers check whether sensitive actions can be performed with unexpected methods, whether authorization applies to all methods, and whether APIs expose dangerous methods.

### Q22. What are HTTP status codes?

HTTP status codes describe response outcome. `2xx` means success, `3xx` means redirect, `4xx` means client error, and `5xx` means server error.

Security relevance: `401` means unauthenticated, `403` means authenticated but forbidden, `404` means not found, and `500` may reveal server errors. Differences in status codes can reveal username enumeration, access control issues, or hidden endpoints.

### Q23. What are HTTP headers?

HTTP headers are metadata sent with requests and responses. Request headers include `Authorization`, `Cookie`, `User-Agent`, `Origin`, and `Content-Type`. Response headers include `Set-Cookie`, `Content-Security-Policy`, and `Strict-Transport-Security`.

Security relevance: headers control authentication, caching, CORS, content type, clickjacking protection, CSP, and cookie security.

### Q24. What is a cookie?

A cookie is a small piece of data stored by the browser and sent to the server with matching requests. Cookies are often used for sessions, preferences, and tracking.

Security relevance: session cookies must use `Secure`, `HttpOnly`, and appropriate `SameSite` settings. Cookies should not store sensitive data directly unless strongly protected.

### Q25. What is a session?

A session is a server-side or token-based way to remember a user's authenticated state across multiple requests. Without sessions, users would need to authenticate on every request.

Security checks include session expiry, logout invalidation, session fixation, session rotation after login, cookie flags, and protection against theft.

### Q26. What is an API?

API means Application Programming Interface. It allows software systems to communicate. Web APIs commonly use HTTP and return JSON.

Security relevance: APIs need authentication, authorization, input validation, schema validation, rate limiting, logging, and protection against excessive data exposure.

### Q27. What is REST API?

REST is an architectural style where resources are accessed using standard HTTP methods and URLs. Example: `GET /users/10` retrieves user 10, while `DELETE /users/10` deletes that user if authorized.

Security relevance: REST APIs are often vulnerable to broken object-level authorization if the backend does not verify whether the user can access the requested resource.

### Q28. What is JSON?

JSON means JavaScript Object Notation. It is a lightweight data format used by APIs and applications.

Security relevance: JSON fields are user input. Testers check JSON for injection, mass assignment, access control bypass, hidden fields, and schema validation issues.

### Q29. What is XML?

XML is a markup format used to structure data. Some older APIs, SAML, SOAP services, and document-processing features use XML.

Security relevance: XML parsers can introduce XXE vulnerabilities if external entities are enabled. XML input should be parsed securely with external entity resolution disabled.

### Q30. What is a proxy?

A proxy sits between client and server and forwards requests. It can inspect, modify, log, or filter traffic. Burp Suite and OWASP ZAP act as web proxies for security testing.

Security relevance: proxies help testers intercept requests and test how applications behave when parameters, tokens, methods, or headers are modified.

### Q31. What is a reverse proxy?

A reverse proxy sits in front of servers and forwards client requests to backend services. Examples include Nginx, Apache, HAProxy, AWS ALB, Azure Application Gateway, and Google Cloud Load Balancer.

Security relevance: reverse proxies can terminate TLS, enforce WAF rules, add headers, rate limit requests, route traffic, and hide backend topology.

### Q32. What is a load balancer?

A load balancer distributes traffic across multiple backend servers. It improves availability and scalability.

Security relevance: load balancers can terminate TLS, integrate with WAF, expose public entry points, and control routing. Misconfigured load balancers may expose internal services.

### Q33. What is a firewall?

A firewall filters network traffic based on rules such as source, destination, port, protocol, and direction. Cloud examples include AWS Security Groups, Azure NSGs, and GCP firewall rules.

Security relevance: firewalls reduce attack surface by allowing only required traffic. They are not replacements for application-level security.

### Q34. What is VPN?

VPN means Virtual Private Network. It creates an encrypted tunnel between users or networks. Organizations use VPNs to provide private access to internal systems.

Security relevance: admin interfaces should often be accessible through VPN, bastion hosts, or private networks instead of being directly exposed to the internet.

### Q35. What is a CDN?

CDN means Content Delivery Network. It caches and serves content from edge locations closer to users. Examples include Cloudflare, Akamai, AWS CloudFront, Azure CDN, and Google Cloud CDN.

Security relevance: CDNs can provide DDoS protection, WAF, TLS termination, bot filtering, and caching. Misconfiguration can expose origin servers or cache sensitive data.

## Linux Security Basics

### Q36. Why is Linux important for DevSecOps?

Linux is widely used for servers, containers, CI runners, cloud instances, and Kubernetes nodes. A DevSecOps engineer should understand Linux permissions, processes, logs, services, networking commands, and package updates.

Security tasks often require reading logs, checking running processes, validating permissions, reviewing SSH access, and troubleshooting pipeline or container issues.

### Q37. What are Linux users and groups?

Linux users represent accounts. Groups allow permissions to be assigned to multiple users. Files and processes run under users and groups.

Security relevance: services should run under dedicated low-privilege users instead of root. Group membership should be reviewed because groups can grant powerful access such as sudo or Docker socket access.

### Q38. What is root in Linux?

Root is the superuser account with full administrative privileges. Root can modify system files, change permissions, install software, and control processes.

Security relevance: running applications as root increases impact if compromised. Containers should also avoid running as root unless absolutely required.

### Q39. What is sudo?

`sudo` allows authorized users to run commands with elevated privileges, usually as root. It is controlled by sudoers configuration.

Security relevance: sudo access must be limited and audited. Overly broad sudo permissions can lead to privilege escalation.

### Q40. What are Linux file permissions?

Linux file permissions define read, write, and execute permissions for owner, group, and others. Example: `rwxr-x---` means owner can read/write/execute, group can read/execute, others have no access.

Security relevance: weak permissions can expose secrets, allow unauthorized modification, or enable execution of malicious files.

### Q41. What is chmod?

`chmod` changes file permissions. For example, `chmod 600 key.pem` allows only the owner to read and write the private key.

Security relevance: private keys, config files, and secret files should have restrictive permissions. Avoid broad permissions like `777` because they allow everyone to read, write, and execute.

### Q42. What is chown?

`chown` changes file ownership. It can assign a file to a specific user and group.

Security relevance: correct ownership prevents unauthorized users from modifying application files, binaries, logs, or secrets.

### Q43. What is a Linux process?

A process is a running program. Each process has a process ID, owner, memory, open files, and command-line arguments.

Security relevance: checking processes can reveal suspicious commands, exposed secrets in command arguments, crypto miners, reverse shells, or unexpected services.

### Q44. What is a Linux service?

A service is a background process managed by the operating system, often through systemd. Examples include SSH, nginx, database services, and application daemons.

Security relevance: unnecessary services should be disabled. Running services should be patched, configured securely, and monitored.

### Q45. What are Linux logs?

Linux logs record system, authentication, service, and application events. Common locations include `/var/log/auth.log`, `/var/log/syslog`, `/var/log/secure`, application logs, and systemd journal.

Security relevance: logs help investigate failed logins, privilege escalation, service crashes, suspicious commands, and incidents.

### Q46. What is SSH?

SSH means Secure Shell. It provides secure remote command-line access to servers. It commonly uses port 22.

Security best practices include disabling password login where possible, using keys, limiting source IPs, disabling root login, using MFA or bastion access, logging attempts, and rotating keys.

### Q47. Why is exposing SSH to the internet risky?

Public SSH is constantly scanned and attacked with brute force, stolen keys, and exploit attempts. If credentials are weak or keys are leaked, attackers can gain server access.

In cloud, restrict SSH to VPN, bastion, SSM, approved IP ranges, or private networks. Also monitor login attempts.

### Q48. What is patching?

Patching means applying updates to fix vulnerabilities, bugs, or stability issues. Patching applies to OS packages, libraries, applications, containers, Kubernetes clusters, and cloud services.

Security relevance: unpatched systems are a common attack path. DevSecOps should automate vulnerability detection and patch workflows.

### Q49. What is hardening?

Hardening means reducing attack surface and configuring systems securely. Examples include disabling unnecessary services, enforcing least privilege, applying patches, enabling logs, restricting network access, and setting secure permissions.

Hardening should use baselines such as CIS Benchmarks where applicable.

### Q50. What is a package manager?

A package manager installs, updates, and removes software packages. Examples include `apt`, `yum`, `dnf`, `apk`, `npm`, `pip`, `maven`, and `go mod`.

Security relevance: package managers pull dependencies that may contain vulnerabilities or malicious packages. Use trusted repositories, lockfiles, scanning, and version control.

## Git And Source Control Security

### Q51. Why is Git important in DevSecOps?

Git stores source code and infrastructure code. DevSecOps integrates security into Git workflows through pull request checks, branch protection, secret scanning, code review, signed commits, and CODEOWNERS.

Many serious incidents begin with leaked secrets or malicious changes in source control, so Git security is foundational.

### Q52. What is a repository?

A repository stores project files and their change history. It may contain source code, IaC, CI/CD workflows, documentation, and configuration.

Security relevance: repositories can leak secrets, internal architecture, vulnerabilities, and deployment logic if access is not controlled.

### Q53. What is a branch?

A branch is an independent line of development in Git. Teams use branches to develop features, fixes, and releases separately before merging.

Security relevance: protected branches ensure important branches like `main` cannot be changed without review and checks.

### Q54. What is a pull request?

A pull request is a request to merge code changes into another branch. It allows review, discussion, and automated checks before merging.

Security relevance: PRs are a natural point for SAST, SCA, secret scanning, IaC scanning, code owner review, and security gates.

### Q55. What is branch protection?

Branch protection enforces rules before changes can be merged. Rules may require reviews, passing checks, signed commits, no force pushes, and restricted access.

Security relevance: branch protection reduces risk of unauthorized or unreviewed changes reaching production.

### Q56. What is CODEOWNERS?

CODEOWNERS defines who must review changes to specific files or directories. For example, security team review may be required for authentication, Terraform, or CI/CD workflow changes.

Security relevance: it ensures sensitive areas are reviewed by responsible experts.

### Q57. Why are secrets in Git dangerous?

Git keeps history. If a secret is committed and then deleted, it may still exist in previous commits, forks, clones, CI logs, or caches. Attackers scan public and private repositories for secrets.

Correct response is rotation or revocation, not only deletion.

### Q58. What is Git history rewriting?

History rewriting changes past commits, often to remove sensitive files. Tools include `git filter-repo` or BFG Repo-Cleaner.

Security relevance: rewriting history can reduce exposure in the repository, but it does not make leaked secrets safe. Rotate secrets first.

### Q59. What are signed commits?

Signed commits use cryptographic signatures to prove that a commit came from a trusted key. They help protect integrity and accountability.

Security relevance: signed commits are useful in high-security environments but require key management and policy enforcement.

### Q60. What are GitHub Actions security risks?

Risks include over-permissive tokens, untrusted third-party actions, secrets exposed to pull requests, command injection in workflows, unsafe use of pull request events, unpinned actions, and compromised runners.

Controls include least-privilege token permissions, pinning actions, protected branches, environment approvals, secret masking, and runner isolation.

## Security Concepts

### Q61. What is an asset?

An asset is anything valuable that needs protection, such as data, application, server, cloud account, source code, secret, pipeline, or user identity.

Security work starts with knowing assets because you cannot protect what you do not know exists.

### Q62. What is attack surface?

Attack surface is the set of points where an attacker can interact with a system. Examples include web endpoints, APIs, open ports, login pages, file uploads, cloud permissions, CI/CD workflows, and third-party integrations.

Reducing attack surface means removing unnecessary exposure and hardening required entry points.

### Q63. What is an attack vector?

An attack vector is a path or method used to attack a system. Examples include phishing, SQL injection, leaked credentials, exposed SSH, vulnerable dependency, or public storage bucket.

In interviews, use attack vector to explain how a vulnerability can realistically be exploited.

### Q64. What is exploitability?

Exploitability describes how easy or realistic it is to exploit a vulnerability. Factors include network exposure, authentication required, complexity, user interaction, known exploit availability, and environmental conditions.

Exploitability is important for prioritizing vulnerabilities beyond raw scanner severity.

### Q65. What is impact?

Impact is the damage that can result from a vulnerability or incident. It may include data breach, account takeover, privilege escalation, financial loss, downtime, compliance violation, or reputation damage.

Good vulnerability reports clearly explain impact in business terms.

### Q66. What is mitigation?

Mitigation reduces risk, sometimes before a full fix is available. Examples include WAF rule, network restriction, disabling a feature, rotating credentials, or adding monitoring.

Mitigation may be temporary. Permanent remediation should still be tracked.

### Q67. What is remediation?

Remediation is the actual fix that removes or corrects the vulnerability. Examples include patching a library, adding authorization checks, changing IAM policy, or enabling encryption.

After remediation, retesting should confirm the issue is fixed.

### Q68. What is validation in security testing?

Validation means confirming whether a finding is real, exploitable, and impactful. It involves reviewing evidence, reproducing safely, checking context, and confirming affected assets.

Validation prevents false positives from wasting time and prevents real issues from being dismissed.

### Q69. What is a control?

A control is a safeguard that reduces risk. Controls can be preventive, detective, corrective, or compensating. Examples include MFA, encryption, logging, WAF, SAST, patching, and access review.

DevSecOps automates many preventive and detective controls.

### Q70. What is a preventive control?

A preventive control stops an issue before it happens. Examples include branch protection, SAST gates, IaC scanning, MFA, least privilege, and admission control.

Preventive controls are valuable because they reduce production incidents.

### Q71. What is a detective control?

A detective control identifies issues or suspicious activity after they occur. Examples include logs, SIEM alerts, CSPM findings, GuardDuty alerts, and vulnerability scans.

Detective controls are essential because prevention is never perfect.

### Q72. What is a corrective control?

A corrective control helps recover or fix after an issue. Examples include patching, rotating credentials, restoring backups, disabling compromised accounts, and rollback.

In incident response, corrective controls help return systems to a secure state.

### Q73. What is a compensating control?

A compensating control is an alternate control used when the preferred fix cannot be immediately applied. Example: if patching is delayed, restrict network access and add WAF detection temporarily.

Compensating controls should have an owner, expiry, and plan for permanent remediation.

### Q74. What is vulnerability disclosure?

Vulnerability disclosure is the process of reporting security vulnerabilities to the responsible organization. It can be private, coordinated, or through bug bounty programs.

Professional behavior includes respecting scope, avoiding data damage, providing clear evidence, and not publicly disclosing before agreed timelines.

### Q75. What is responsible testing?

Responsible testing means testing only systems you are authorized to test, staying within scope, avoiding harm, protecting data, and reporting findings clearly.

In interviews, always mention authorization and scope before discussing active testing.

## Cloud Foundations

### Q76. What is cloud computing in simple words?

Cloud computing means renting computing resources from a provider instead of owning physical infrastructure. You can create servers, databases, storage, networks, and security services on demand.

Security relevance: cloud makes deployment faster, but misconfigurations also happen faster. DevSecOps uses automation and guardrails to reduce cloud risk.

### Q77. What is IaaS?

IaaS means Infrastructure as a Service. The cloud provider gives basic infrastructure like virtual machines, disks, and networks. The customer manages operating systems, applications, data, and many security configurations.

Example: AWS EC2, Azure Virtual Machines, GCP Compute Engine.

### Q78. What is PaaS?

PaaS means Platform as a Service. The provider manages more of the platform, while the customer deploys applications and manages data/configuration.

Examples include Azure App Service, Google App Engine, AWS Elastic Beanstalk. Security responsibility shifts more toward application, identity, configuration, and data.

### Q79. What is SaaS?

SaaS means Software as a Service. The provider delivers a complete application over the internet. Examples include Microsoft 365, Salesforce, Google Workspace, and GitHub.

Customer security responsibilities include identity, access control, configuration, data governance, and integration security.

### Q80. What is serverless?

Serverless lets developers run code without managing servers directly. Examples include AWS Lambda, Azure Functions, and Google Cloud Functions.

Security relevance: serverless still needs secure code, IAM least privilege, secret management, dependency scanning, logging, and event source validation.

### Q81. What is a region?

A cloud region is a geographic area containing cloud data centers. Examples include `us-east-1`, `eastus`, or `asia-south1`.

Security relevance: regions matter for latency, availability, compliance, data residency, and disaster recovery.

### Q82. What is an availability zone?

An availability zone is an isolated data center or group of data centers within a cloud region. Deploying across multiple zones improves availability.

Security relevance: availability is part of security. Highly available architecture reduces downtime from failures or attacks.

### Q83. What is object storage?

Object storage stores files as objects in buckets or containers. Examples include AWS S3, Azure Blob Storage, and Google Cloud Storage.

Security relevance: object storage misconfiguration is a common source of data leaks. Check public access, IAM, encryption, logs, retention, and sensitive data.

### Q84. What is a virtual private cloud?

A virtual private cloud is an isolated cloud network where resources run. AWS calls it VPC, Azure uses VNet, and GCP uses VPC.

Security relevance: VPC/VNet design controls segmentation, internet exposure, routing, private access, and network inspection.

### Q85. What is a security group?

In AWS, a security group is a stateful virtual firewall attached to resources like EC2 instances. It controls inbound and outbound traffic.

Security relevance: avoid exposing admin ports like SSH or RDP to the internet. Allow only required ports and sources.

### Q86. What is Azure NSG?

An Azure Network Security Group controls inbound and outbound traffic for subnets or network interfaces using rules.

Security relevance: NSGs are used to restrict public exposure and segment workloads in Azure VNets.

### Q87. What are GCP firewall rules?

GCP firewall rules control traffic to and from resources in a VPC network. They can target instances by network tags or service accounts.

Security relevance: overly broad rules like `0.0.0.0/0` on admin ports are high-risk.

### Q88. What is cloud IAM policy?

A cloud IAM policy defines permissions assigned to users, groups, roles, or service accounts. It determines who can perform actions on cloud resources.

Security relevance: over-permissive IAM is one of the most dangerous cloud risks. Use least privilege and review access regularly.

### Q89. What is MFA?

MFA means Multi-Factor Authentication. It requires more than one factor, such as password plus authenticator app or hardware key.

Security relevance: MFA reduces risk from stolen passwords. It is especially important for cloud consoles, admin users, Git platforms, and production systems.

### Q90. What is encryption?

Encryption transforms data into unreadable form unless someone has the correct key. It protects confidentiality.

Security relevance: use encryption in transit with TLS and encryption at rest for disks, databases, object storage, backups, and secrets.

## Interview Communication

### Q91. How should a beginner answer when they do not know a tool deeply?

Be honest but structured. Say what category the tool belongs to, what problem it solves, where it fits in the pipeline, and how you would validate results. For example: "I have not used Checkmarx deeply, but I understand it is a SAST tool used to detect insecure code patterns in CI/CD. I would triage findings by reachability, severity, and evidence, similar to Semgrep or SonarQube."

This sounds mature because you show transferable understanding instead of pretending.

### Q92. How do you answer "Have you worked on production DevSecOps?"

If you have not, do not fake production experience. Say you have built understanding through labs and structured workflows, and explain exactly how you would implement production controls: repo protection, SAST, SCA, secrets, IaC scanning, container scanning, cloud logging, IAM review, and vulnerability management.

Interviewers respect clarity more than fake confidence.

### Q93. How do you sound like a 2-year candidate without lying?

Speak in process and tradeoffs. A 2-year candidate should know not only definitions but also where controls fit, common false positives, developer communication, and prioritization.

Use phrases like "I would first validate scope", "I would prioritize based on exposure and impact", "I would not rely only on scanner output", and "I would add prevention so it does not recur."

### Q94. What should you never say in a security interview?

Avoid saying "tools will find everything", "HTTPS means secure", "WAF fixes the vulnerability", "we can delete leaked keys from Git and it is solved", "all critical CVEs are always exploitable", or "I can test any site without permission."

These answers show immature security thinking.

### Q95. How do you answer tool comparison questions?

Compare by category, use case, strengths, limitations, and pipeline fit. Example: Burp is strong for manual web testing, ZAP is open-source and automation-friendly, SAST tools find code issues early, and DAST tools test runtime behavior.

The goal is to show judgment, not just tool name memorization.

### Q96. How do you answer scenario questions?

Use a consistent flow: understand scope, validate evidence, assess impact, contain if urgent, remediate root cause, retest, document, and add prevention.

For incident-style scenarios, use: contain, investigate, eradicate, recover, prevent.

### Q97. How do you explain a finding to a developer?

Use respectful, specific, reproducible language. Explain what is wrong, why it matters, how to reproduce safely, and how to fix it. Avoid blaming.

Example: "This endpoint returns another user's data when the object ID is changed. The fix is to check object ownership server-side before returning the record."

### Q98. How do you explain a finding to management?

Use business impact, likelihood, affected assets, urgency, and remediation status. Avoid excessive technical detail.

Example: "A storage bucket containing internal reports was publicly accessible. We removed public access, are checking logs for exposure, and are adding preventive policy checks."

### Q99. What makes a good security report?

A good report includes title, severity, affected asset, summary, business impact, reproduction steps, evidence, root cause, remediation, references, and retest status.

It should be clear enough for developers to fix and managers to understand risk.

### Q100. What is your 30-day learning strategy for DevSecOps?

Start with fundamentals: Linux, networking, Git, cloud basics, and AppSec basics. Then learn OWASP Top 10, testing workflows, and core tools. Next learn CI/CD security, containers, Kubernetes, IaC, and cloud security across AWS/Azure/GCP. Finally practice scenario chains and mock interviews.

Hands-on labs should include OWASP Juice Shop, PortSwigger Academy, Trivy image scanning, Gitleaks secret scanning, Checkov Terraform scanning, and cloud security review labs.

