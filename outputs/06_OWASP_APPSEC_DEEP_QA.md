# OWASP And AppSec Deep Q&A

This file expands AppSec and OWASP-style interview questions. Learn every vulnerability with this structure: definition, example, impact, test method, fix, tools, and common mistake.

## OWASP Overview

### Q1. What is OWASP Top 10 used for?

OWASP Top 10 is used as an awareness guide for common web application security risks. It helps developers, testers, security engineers, and managers understand the most important categories of web risks.

It should not be treated as a complete testing standard. For deeper verification, use OWASP ASVS, OWASP Testing Guide, threat modeling, secure code review, and business logic testing.

### Q2. What is OWASP ASVS?

OWASP ASVS means Application Security Verification Standard. It provides detailed application security requirements and verification controls across authentication, session management, access control, input validation, cryptography, error handling, APIs, and more.

ASVS is useful when a company wants a structured security baseline instead of only saying "follow OWASP Top 10."

### Q3. What is OWASP API Security Top 10?

OWASP API Security Top 10 focuses on API-specific risks such as broken object-level authorization, broken authentication, broken object property-level authorization, unrestricted resource consumption, broken function-level authorization, unrestricted access to sensitive business flows, SSRF, security misconfiguration, improper inventory management, and unsafe consumption of APIs.

APIs are heavily used in modern applications, mobile apps, microservices, and cloud-native systems, so API security is essential for DevSecOps roles.

### Q4. What is the difference between OWASP Top 10 and ASVS?

OWASP Top 10 is an awareness document with broad risk categories. ASVS is a detailed verification standard with specific security requirements. Top 10 tells you common risk areas; ASVS helps define what secure implementation should look like.

In interviews, say Top 10 is good for awareness and prioritization, while ASVS is better for checklists, requirements, and maturity.

### Q5. How would you use OWASP in DevSecOps?

Use OWASP Top 10 for developer awareness, ASVS for security requirements, OWASP Dependency-Check for SCA, OWASP ZAP for DAST, OWASP Cheat Sheets for secure coding guidance, and OWASP Testing Guide for manual testing methodology.

In CI/CD, OWASP concepts can become automated checks and review criteria.

## Broken Access Control

### Q6. Why is broken access control so critical?

Broken access control is critical because it allows users to access data or actions they should not access. It can lead to data breaches, account takeover, privilege escalation, fraud, and compliance violations.

It is often high impact because attackers do not always need advanced exploitation. Sometimes changing an ID, role, method, or endpoint is enough.

### Q7. What is horizontal privilege escalation?

Horizontal privilege escalation happens when a user accesses another user’s data or actions at the same privilege level. Example: user A changes `user_id=1001` to `user_id=1002` and sees user B's profile.

Testing requires at least two users with the same role. The fix is server-side object ownership validation.

### Q8. What is vertical privilege escalation?

Vertical privilege escalation happens when a lower-privileged user accesses higher-privileged functionality. Example: a normal user accesses `/admin/deleteUser` or changes their role to admin.

Testing requires users with different roles. The fix is centralized role/permission checks on every sensitive function.

### Q9. What is forced browsing?

Forced browsing means directly accessing URLs or APIs that are not linked in the UI. Example: visiting `/admin`, `/debug`, `/backup.zip`, or hidden API endpoints.

Fixes include server-side authorization, removing sensitive files, disabling debug routes, and not relying on obscurity.

### Q10. What is IDOR?

IDOR means Insecure Direct Object Reference. It occurs when an application exposes object identifiers and fails to check whether the current user is authorized to access the object.

Example: `/download?file_id=55` returns another user's file when the ID is changed. Fix with object-level authorization.

### Q11. How do you test IDOR?

Create two test accounts. Capture a request from user A accessing their resource. Replace the resource ID with user B's resource ID and send the request using user A's session. If user A can access user B's resource, IDOR exists.

Also test IDs in URL paths, query parameters, JSON bodies, headers, GraphQL IDs, and file references.

### Q12. Are UUIDs enough to prevent IDOR?

No. UUIDs make guessing harder but do not replace authorization. If a UUID leaks through logs, URLs, browser history, analytics, or another endpoint, attackers can still use it.

The correct fix is server-side authorization on every object access.

### Q13. What is mass assignment?

Mass assignment happens when an application automatically binds user-provided fields to backend objects without restricting which fields can be changed. Example: a user sends `"role": "admin"` in a profile update request and becomes admin.

Fix by allowlisting fields that users are allowed to update, ignoring sensitive fields, and enforcing server-side authorization.

### Q14. How do you test mass assignment?

Inspect API requests and responses for hidden or sensitive fields like `role`, `isAdmin`, `balance`, `status`, `verified`, or `discount`. Add or modify those fields in update requests and check whether the backend accepts them.

Tools like Burp Repeater and Postman are useful, but the key is understanding object properties.

### Q15. What is broken function-level authorization?

Broken function-level authorization occurs when users can call functions or endpoints outside their role. Example: a normal user can call an admin API because the frontend hides the button but the backend lacks role checks.

Fix with server-side authorization at route, service, and business logic layers.

## Injection

### Q16. What is injection in general?

Injection occurs when untrusted input is interpreted as part of a command, query, or code expression. The attacker changes the meaning of the instruction sent to another interpreter.

Types include SQL injection, NoSQL injection, command injection, LDAP injection, template injection, and XPath injection.

### Q17. What is the root cause of SQL injection?

The root cause is building SQL queries by concatenating untrusted input into query strings. The database then treats attacker input as SQL syntax instead of data.

The primary fix is parameterized queries or prepared statements.

### Q18. What is blind SQL injection?

Blind SQL injection occurs when the application does not directly show database errors or query results, but the attacker can infer true or false conditions through behavior, timing, or response differences.

Examples include boolean-based blind SQLi and time-based blind SQLi. Fix is still parameterized queries.

### Q19. What is NoSQL injection?

NoSQL injection occurs when untrusted input changes NoSQL database queries. For example, in MongoDB, unsafe handling of JSON operators like `$ne` or `$gt` can alter authentication logic.

Fixes include schema validation, rejecting unexpected operators, using safe query builders, and treating user input as data.

### Q20. What is command injection?

Command injection occurs when an application passes untrusted input to an operating system command. Impact can include remote command execution.

Fix by avoiding shell commands, using safe APIs, allowlisting input, separating arguments safely, and running services with least privilege.

### Q21. What is LDAP injection?

LDAP injection happens when untrusted input is inserted into LDAP queries. It can bypass authentication or extract directory information.

Fix with parameterized LDAP APIs, escaping special characters, input validation, and least-privilege directory accounts.

### Q22. What is server-side template injection?

Server-side template injection occurs when user input is processed as a template expression by a server-side template engine. Depending on the engine, impact may range from data exposure to remote code execution.

Fix by not allowing user-controlled template code, using safe template contexts, sandboxing, and validation.

### Q23. How do you prevent injection broadly?

Treat all input as untrusted. Use parameterized queries, safe APIs, allowlists, schema validation, context-aware encoding, avoid dynamic code execution, and apply least privilege.

WAF can help detect or block attacks but does not replace secure coding.

### Q24. How do you explain injection to a beginner?

Injection is like asking an application to search for a name, but your input changes the command itself. Instead of treating your input as plain text, the backend accidentally treats it as instructions.

That is why safe separation between code and data is essential.

## XSS

### Q25. What is XSS?

XSS means Cross-Site Scripting. It allows attacker-controlled JavaScript to run in another user's browser because the application outputs untrusted data unsafely.

Impact includes session theft, account actions, phishing, data access, keylogging, and bypassing UI controls.

### Q26. What is stored XSS?

Stored XSS occurs when malicious input is stored by the application and later shown to other users. Example: an attacker posts a malicious comment that executes whenever others view the page.

Stored XSS is often more severe because it affects multiple users without needing a crafted link.

### Q27. What is reflected XSS?

Reflected XSS occurs when malicious input is reflected immediately in a response, often through a query parameter or form submission. The victim usually needs to click a crafted link.

Fix with contextual output encoding and safe handling of reflected input.

### Q28. What is DOM-based XSS?

DOM-based XSS happens when client-side JavaScript reads attacker-controlled data and writes it to the DOM using unsafe APIs like `innerHTML`.

Fix by using safe DOM APIs, sanitizing HTML where needed, and avoiding unsafe sinks.

### Q29. What is output encoding?

Output encoding converts special characters into a safe representation for the output context. For example, HTML encoding converts `<` into `&lt;`.

Encoding must be context-aware. HTML body, HTML attribute, JavaScript, CSS, and URL contexts have different rules.

### Q30. What is CSP and how does it help XSS?

Content Security Policy is a browser security header that restricts where scripts and other resources can load from. It can reduce XSS impact by blocking inline scripts or unauthorized script sources.

CSP is defense in depth. It should not replace output encoding and safe coding.

### Q31. Can HttpOnly cookies fully prevent XSS?

No. HttpOnly prevents JavaScript from reading the cookie, which reduces session theft. But XSS can still perform actions as the user, read page content, change UI, or steal tokens stored elsewhere.

HttpOnly is important but not a complete XSS fix.

### Q32. How do you test for XSS safely?

In an authorized test environment, identify input fields and reflection points, use harmless proof-of-concept payloads, check different contexts, and verify whether input executes as script. Avoid destructive payloads or stealing real data.

Tools like Burp Suite, ZAP, and browser developer tools help, but context understanding is essential.

## Authentication And Session Security

### Q33. What is broken authentication?

Broken authentication occurs when flaws in login, session, password reset, MFA, or token handling allow account takeover or impersonation.

Examples include weak passwords, no rate limiting, predictable reset tokens, session fixation, insecure JWT validation, and missing MFA for admin users.

### Q34. What is username enumeration?

Username enumeration happens when an application reveals whether a username or email exists. Example: login says "user not found" for one email and "wrong password" for another.

Fix with generic messages, consistent timing where possible, and monitoring for enumeration attempts.

### Q35. What is brute force?

Brute force means trying many password combinations or credentials until one works. Credential stuffing uses known leaked username/password pairs.

Controls include rate limiting, MFA, account lockout or risk-based controls, password breach checks, bot detection, and monitoring.

### Q36. What is session fixation?

Session fixation occurs when an attacker sets or knows a victim's session ID before login, and the application does not rotate the session after authentication.

Fix by generating a new session ID after login and privilege changes.

### Q37. What is secure password storage?

Passwords should be hashed using slow, adaptive password hashing algorithms such as bcrypt, Argon2, or PBKDF2 with salts. Passwords should never be stored in plaintext or encrypted for later retrieval.

Hashing protects passwords if the database is leaked.

### Q38. What is MFA bypass?

MFA bypass occurs when attackers can avoid or weaken multi-factor authentication. Examples include unprotected backup codes, weak recovery flows, remembered devices without proper checks, or APIs that do not enforce MFA.

Testing must include login, password reset, device trust, session reuse, and sensitive actions.

### Q39. What is JWT algorithm confusion?

JWT algorithm confusion happens when a server incorrectly accepts a different signing algorithm than intended, such as accepting `none` or confusing asymmetric and symmetric verification.

Fix by explicitly enforcing expected algorithms and validating signature, issuer, audience, and expiry.

### Q40. What is token expiry?

Token expiry defines how long a token remains valid. Shorter lifetimes reduce exposure if tokens are stolen. Refresh tokens can provide longer sessions but need strong protection and rotation.

Security checks include expiry validation, revocation, rotation, secure storage, and logout invalidation.

## Cryptographic Failures

### Q41. What are cryptographic failures?

Cryptographic failures occur when sensitive data is not protected properly. Examples include no encryption, weak algorithms, poor key management, plaintext passwords, weak TLS, or sensitive data in logs.

The impact is usually data exposure or integrity compromise.

### Q42. What is encryption at rest?

Encryption at rest protects stored data such as databases, disks, object storage, backups, and files. Cloud services often support managed encryption using provider-managed or customer-managed keys.

Security depends not only on encryption being enabled, but also on who can access keys.

### Q43. What is encryption in transit?

Encryption in transit protects data moving between systems, usually through TLS. It prevents network attackers from reading or modifying traffic.

Use HTTPS for web apps, TLS for APIs, and secure protocols for service-to-service communication.

### Q44. What is hashing?

Hashing converts data into a fixed-length digest. It is one-way, meaning the original data should not be recoverable from the hash.

Security uses include password hashing, integrity checks, and digital signatures. For passwords, use slow password hashing, not fast general hashes like plain SHA-256.

### Q45. What is salting?

Salting means adding a unique random value to each password before hashing. It prevents attackers from using precomputed hash tables and makes identical passwords have different hashes.

Modern password hashing libraries handle salts automatically.

### Q46. What is key management?

Key management is the secure creation, storage, rotation, access control, and auditing of cryptographic keys. Poor key management can break otherwise strong encryption.

Cloud tools include AWS KMS, Azure Key Vault, GCP Cloud KMS, and HashiCorp Vault.

### Q47. What is hardcoded cryptographic key risk?

If encryption keys are hardcoded in source code, anyone with repository access can decrypt data or forge protected values. If code leaks, the key is compromised.

Fix by storing keys in KMS or secret managers and controlling access.

## Security Misconfiguration

### Q48. What is security misconfiguration?

Security misconfiguration occurs when systems, applications, cloud resources, or platforms are configured insecurely. Examples include debug mode enabled, public buckets, default credentials, verbose errors, open admin ports, and missing headers.

DevSecOps reduces this through baselines, IaC scanning, CSPM, hardening, and automated policy enforcement.

### Q49. What are default credentials?

Default credentials are vendor-provided usernames and passwords such as `admin/admin`. If not changed, attackers can easily log in.

Controls include changing defaults, disabling unused accounts, secrets rotation, and automated configuration checks.

### Q50. Why is debug mode dangerous in production?

Debug mode can expose stack traces, environment variables, internal paths, secret values, framework details, and interactive consoles. This information helps attackers.

Production should use safe error pages and detailed errors should go only to protected logs.

### Q51. What is directory listing?

Directory listing occurs when a web server shows file listings for a directory without an index file. It can expose backups, source code, logs, or config files.

Fix by disabling directory indexing and removing sensitive files from web roots.

### Q52. What are missing security headers?

Missing security headers means the application does not set browser defense headers such as CSP, HSTS, X-Frame-Options/frame-ancestors, X-Content-Type-Options, Referrer-Policy, or secure cookie flags.

Severity depends on context. Headers are defense in depth but should still be implemented.

## Vulnerable Components

### Q53. Why are vulnerable dependencies dangerous?

Applications use many third-party packages. If a package has a known vulnerability, attackers may exploit it without needing to find a new bug.

Dependency risk is managed through SCA, patching, SBOM, dependency ownership, and update automation.

### Q54. What is a transitive dependency?

A transitive dependency is a dependency used by another dependency. Your application may not directly import it, but it still becomes part of your software.

Security relevance: many SCA findings are transitive, so remediation may require upgrading the direct dependency that brings it in.

### Q55. What is dependency confusion?

Dependency confusion is a supply-chain attack where a malicious package with the same name as an internal package is published to a public registry. Build tools may download the malicious public package instead of the private one.

Controls include private registry configuration, scoped packages, dependency pinning, repository policies, and monitoring.

### Q56. What is typosquatting?

Typosquatting is when attackers publish packages with names similar to popular packages, hoping developers install them by mistake.

Controls include careful dependency review, approved package registries, lockfiles, and security scanning.

### Q57. What is SBOM useful for in dependency risk?

SBOM lists software components and versions. When a major CVE is announced, teams can search SBOMs to quickly identify affected applications.

SBOM improves visibility but must be connected to vulnerability management to be useful.

## Logging And Monitoring

### Q58. What is security logging?

Security logging records events relevant to detection and investigation. Examples include login attempts, access denied events, admin actions, password resets, token failures, data exports, and configuration changes.

Logs should include enough context to investigate but should not expose secrets or sensitive data unnecessarily.

### Q59. What is insufficient logging and monitoring?

Insufficient logging and monitoring means attacks or abuse may happen without detection. If logs are missing, incomplete, or not reviewed, incidents may go unnoticed.

Controls include centralized logs, alert rules, SIEM correlation, retention, access protection, and incident playbooks.

### Q60. What should not be logged?

Do not log passwords, full tokens, private keys, credit card numbers, sensitive personal data, or session cookies. If sensitive identifiers are needed, mask or tokenize them.

Logging secrets can turn normal logs into a data breach source.

## SSRF Deep Dive

### Q61. Why is SSRF in OWASP Top 10?

SSRF is included because modern applications often fetch URLs, integrate with internal services, and run in cloud environments. SSRF can bypass network boundaries and access internal services or metadata endpoints.

Its impact is high when combined with cloud IAM or internal admin services.

### Q62. What features commonly cause SSRF?

Common features include URL upload, image import, PDF generation, webhook callbacks, link previews, integrations, XML parsers, server-side file fetchers, and proxy endpoints.

Any feature where the server makes a request based on user input should be reviewed.

### Q63. Why is blocking only `localhost` not enough for SSRF?

Attackers can use private IP ranges, IPv6, decimal/hex/octal IP formats, DNS rebinding, redirects, cloud metadata IPs, internal hostnames, and open redirects to bypass simple filters.

Robust SSRF defense requires strict allowlisting, safe DNS/IP validation, redirect control, and network egress restrictions.

### Q64. What is cloud metadata service?

Cloud metadata service provides instance or workload metadata, sometimes including temporary credentials. AWS uses metadata endpoints for EC2 instance role credentials. Other clouds have similar metadata services.

If SSRF reaches metadata and IAM permissions are broad, impact can be severe.

### Q65. How does AWS IMDSv2 help?

AWS IMDSv2 requires a session token for metadata requests, making many simple SSRF attacks harder. It is a defense-in-depth control for EC2 metadata access.

However, applications still need SSRF protections and IAM roles must remain least privilege.

## API Security

### Q66. What is BOLA?

BOLA means Broken Object Level Authorization. It is an API risk where users can access objects they do not own or are not allowed to access.

It is similar to IDOR and is one of the most common API vulnerabilities.

### Q67. What is excessive data exposure?

Excessive data exposure occurs when an API returns more data than the client needs, relying on the frontend to hide sensitive fields.

Fix by filtering response data server-side and using response schemas.

### Q68. What is broken object property-level authorization?

This occurs when users can read or modify object properties they should not access. Example: updating `isAdmin`, `creditLimit`, or `accountStatus` through an API request.

Fix through field-level authorization and allowlisting.

### Q69. What is unrestricted resource consumption?

Unrestricted resource consumption occurs when APIs allow expensive or excessive requests without limits. Examples include large file uploads, unlimited pagination, expensive searches, or repeated OTP requests.

Fix with rate limits, quotas, pagination limits, size limits, timeouts, and cost controls.

### Q70. What is improper API inventory management?

Improper inventory management means organizations do not know all APIs, versions, environments, or exposed endpoints. Shadow APIs and old versions may remain vulnerable.

Controls include API inventory, gateway logging, documentation, deprecation process, and external attack surface monitoring.

## File Upload Security

### Q71. Why is file upload risky?

File upload allows users to send content to the server. Risk includes malware, web shells, stored XSS, oversized files, path traversal, metadata leakage, public exposure, and server-side parser vulnerabilities.

File upload must be handled with strict validation and safe storage.

### Q72. Is extension validation enough?

No. Attackers can rename files or use double extensions. Validate content type server-side, check magic bytes where appropriate, allowlist extensions, and store files safely.

Also prevent direct execution from upload directories.

### Q73. What is a web shell?

A web shell is a malicious script uploaded to a web server that allows remote command execution through web requests.

Prevention includes blocking executable uploads, storing files outside web root, disabling script execution in upload directories, and scanning uploads.

### Q74. Why should uploaded files be renamed?

Renaming files to random server-generated names prevents path manipulation, collisions, predictable URLs, and dangerous original filenames.

Original filenames can be stored as metadata after sanitization if needed.

### Q75. What is malware scanning in uploads?

Malware scanning checks uploaded files for known malicious content. Tools may include antivirus engines or cloud malware scanning services.

It is a defense layer, not a complete replacement for file type restrictions and safe storage.

## CORS And CSRF

### Q76. What is insecure CORS?

Insecure CORS occurs when an API allows untrusted origins to read sensitive responses, especially with credentials enabled.

Dangerous patterns include reflecting arbitrary `Origin` values and using `Access-Control-Allow-Credentials: true` with broad origin trust.

### Q77. Is CORS an authentication control?

No. CORS is a browser access control for reading cross-origin responses. It does not stop non-browser clients like curl or backend services from making requests.

Authentication and authorization must be enforced by the server.

### Q78. What is CSRF token?

A CSRF token is an unpredictable value included in legitimate requests to prove the request came from the application flow. The server validates it before performing sensitive actions.

CSRF tokens help protect cookie-authenticated applications from cross-site request forgery.

### Q79. Does SameSite cookie prevent all CSRF?

SameSite reduces CSRF risk but may not cover every case, depending on browser behavior, method, navigation type, and application requirements.

Sensitive applications should combine SameSite with CSRF tokens and origin checks where appropriate.

### Q80. What is preflight request in CORS?

A preflight request is an `OPTIONS` request sent by browsers before certain cross-origin requests to check whether the server allows the method and headers.

Security relevance: CORS policy should allow only trusted origins, methods, and headers.

## AppSec Reporting And Triage

### Q81. How do you assign severity to an AppSec finding?

Consider impact, exploitability, authentication required, user interaction, affected data, affected users, internet exposure, existing controls, and business context.

Scanner severity is a starting point, not the final decision.

### Q82. What is proof of concept in security reporting?

A proof of concept demonstrates that a vulnerability is real. It should be safe, minimal, and non-destructive.

For example, an IDOR proof may show user A accessing user B's test invoice, not real customer data.

### Q83. What makes remediation guidance good?

Good remediation guidance is specific, practical, and mapped to root cause. Instead of saying "fix access control", say "add server-side ownership check before returning invoice records and add tests for cross-user access."

Developers need actionable fixes.

### Q84. How do you retest a vulnerability?

Repeat the original proof of concept after the fix. Also test nearby variants to ensure the root cause was fixed, not only one endpoint or one parameter.

Document retest result and close only when evidence confirms remediation.

### Q85. How do you handle duplicate findings?

Group duplicate findings by root cause where possible. For example, missing authorization on many endpoints may be one systemic issue rather than dozens of unrelated tickets.

This helps developers fix the pattern and reduces ticket noise.

## Secure Design

### Q86. What is insecure design?

Insecure design means the system architecture or business flow lacks necessary security controls. Even bug-free code can be insecure if the design is flawed.

Example: a money transfer system that does not require authorization checks or transaction limits has insecure design.

### Q87. How does threat modeling prevent insecure design?

Threat modeling identifies risks before implementation. It helps teams add controls like authorization, rate limiting, logging, encryption, and abuse prevention early.

It is cheaper to fix design risks before code is built.

### Q88. What is abuse case?

An abuse case describes how a feature could be misused. For example, "An attacker repeatedly requests OTP codes to spam a victim" or "A user changes price in checkout API."

Abuse cases help design anti-abuse controls.

### Q89. What is secure by default?

Secure by default means the default configuration is safe unless explicitly changed. Example: storage buckets should be private by default, containers should not be privileged by default, and debug mode should be off in production.

Secure defaults reduce accidental risk.

### Q90. What is fail securely?

Fail securely means that when something goes wrong, the system fails into a safe state. Example: if authorization service is unavailable, deny access instead of allowing everyone.

This prevents errors from becoming bypasses.

## Advanced AppSec Interview Chains

### Q91. XSS is found in an admin panel. How do you rate it?

It may be high severity if attackers can exploit it against admins and perform privileged actions. Severity depends on exploitability, whether authentication is required, who can trigger it, data accessible to admin, and whether CSP/HttpOnly reduce impact.

Admin-context XSS is usually more serious than low-privilege self-XSS.

### Q92. SQL injection is found but database user is read-only. Is it still serious?

Yes. Read-only SQL injection can still expose sensitive data, authentication records, personal information, or business data. It may also allow further attacks depending on database features and configuration.

Least privilege reduces impact but does not eliminate the vulnerability.

### Q93. IDOR exposes only email address. Is it low?

It depends. One email may seem low impact, but bulk enumeration of emails can be privacy-sensitive and useful for phishing. Severity depends on scale, sensitivity, user base, and regulatory context.

Always evaluate realistic impact.

### Q94. Missing rate limit on login but MFA enabled. Is it still an issue?

Yes, but severity may be reduced. Attackers can still enumerate accounts, lock users out, abuse password checks, or attempt credential stuffing. MFA is a strong compensating control but rate limiting is still needed.

Layered controls are better than relying on one control.

### Q95. A scanner reports X-Frame-Options missing. How do you validate impact?

Check whether the page can be framed and whether framed actions could cause harm. If the application has sensitive click actions and no CSP `frame-ancestors`, clickjacking risk is higher.

If the page is static public content with no sensitive actions, severity is lower.

### Q96. API returns password hash to user. What is the issue?

This is excessive data exposure and sensitive data leakage. Password hashes should not be returned to clients. If exposed, attackers may attempt offline cracking if they obtain enough hashes.

Fix by filtering sensitive fields server-side.

### Q97. An app uses Base64 for sensitive data. Is that encryption?

No. Base64 is encoding, not encryption. Anyone can decode Base64 without a key.

Sensitive data needs proper encryption using strong algorithms and managed keys.

### Q98. Can input validation alone prevent XSS?

Input validation helps, but output encoding is the primary defense for XSS because the risk depends on output context. Some valid input may still contain characters dangerous in HTML or JavaScript contexts.

Use validation plus contextual output encoding and sanitization for rich HTML.

### Q99. Can WAF replace fixing SQL injection?

No. WAF can reduce exploit attempts and provide temporary mitigation, but the root cause remains vulnerable code. The correct fix is parameterized queries and safe database access.

WAF is defense in depth, not permanent remediation.

### Q100. How do you summarize AppSec maturity?

AppSec maturity means security is built into SDLC through threat modeling, secure coding standards, code review, SAST, DAST, SCA, secret scanning, API testing, vulnerability management, developer training, and runtime monitoring.

Mature teams focus on preventing patterns, not just closing individual tickets.

