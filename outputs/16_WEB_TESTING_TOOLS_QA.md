# Web Testing Tools Deep Q&A

This file covers practical AppSec testing tools: Burp Suite, OWASP ZAP, Nmap, Nuclei, Nikto, Postman, curl, browser developer tools, and TLS/security header tools. It focuses on authorized testing and interview-safe explanations.

## Web Testing Methodology

### Q1. What is your first step before testing a website?

Confirm authorization, scope, environments, testing window, accounts, rate limits, and rules of engagement. Then understand the application purpose, roles, data sensitivity, APIs, and technology.

Professional testing starts with scope. Testing random sites without permission is not acceptable.

### Q2. How do you map a web application?

Browse the app as different roles, capture traffic, list endpoints, identify APIs, understand authentication, note parameters, find file uploads, map admin functions, and check integrations.

Tools help, but manual exploration is essential for business logic and access control testing.

### Q3. What is passive testing?

Passive testing observes traffic and responses without sending attack payloads. Examples include reviewing headers, cookies, technologies, comments, error messages, and visible endpoints.

It is lower risk and useful early in testing.

### Q4. What is active testing?

Active testing sends crafted inputs or payloads to check for vulnerabilities. Examples include testing injection, XSS, SSRF, brute force protections, and file upload handling.

Active testing should be authorized and controlled because it can affect systems.

### Q5. What is manual testing?

Manual testing uses human reasoning to test logic, roles, workflows, and context. It is essential for access control, business logic, and chained vulnerabilities.

Automated scanners cannot fully replace manual testing.

### Q6. What is automated testing?

Automated testing uses tools to scan for known patterns, misconfigurations, and common vulnerabilities. It improves coverage and repeatability.

Findings still need validation.

### Q7. How do you test safely?

Use test accounts, test data, approved environments, rate limits, non-destructive payloads, and clear communication. Avoid data exfiltration, destructive actions, or testing outside scope.

Always document what you tested and evidence.

### Q8. How do you write a finding?

Include title, severity, affected asset, summary, impact, reproduction steps, evidence, root cause, remediation, and retest status.

Good reports are clear enough for developers to fix.

## Burp Suite

### Q9. What is Burp Suite?

Burp Suite is a web application security testing platform. It works as an intercepting proxy and includes tools for inspecting, modifying, replaying, scanning, and analyzing HTTP traffic.

It is one of the most important manual AppSec tools.

### Q10. What is Burp Proxy?

Burp Proxy intercepts HTTP/S traffic between browser and application. It lets testers view and modify requests and responses.

Use it to understand application behavior and capture requests for deeper testing.

### Q11. What is Burp Repeater?

Repeater lets you manually modify and resend individual HTTP requests. It is used for testing access control, injection, headers, tokens, and business logic.

Repeater is one of the most useful Burp tools for interview scenarios.

### Q12. What is Burp Intruder?

Intruder automates sending many request variations, useful for fuzzing parameters, testing rate limits, enumerating values, or controlled brute-force tests.

Use only with authorization and rate limits.

### Q13. What is Burp Collaborator?

Burp Collaborator detects out-of-band interactions such as DNS or HTTP callbacks from a target server. It helps test SSRF, blind XXE, blind command injection, and blind SQLi.

Use approved collaborator infrastructure in professional tests.

### Q14. What is Burp Decoder?

Decoder encodes and decodes data formats such as URL encoding, Base64, HTML entities, and hex.

It helps analyze tokens, parameters, and payload transformations.

### Q15. What is Burp Comparer?

Comparer compares two pieces of data, such as HTTP responses. It helps identify differences in responses during access control, auth, or injection testing.

Small response differences can reveal vulnerabilities.

### Q16. What is Burp Sequencer?

Sequencer analyzes randomness of tokens such as session IDs or CSRF tokens.

It helps assess whether tokens are predictable.

### Q17. What is Burp Scanner?

Burp Scanner automatically tests for web vulnerabilities. It is available in Burp Professional/Enterprise depending on product.

Scanner findings still require validation and context.

### Q18. What Burp extensions are useful?

Useful extensions include Logger++, Autorize, Param Miner, Retire.js, JSON Web Tokens, Turbo Intruder, and ActiveScan++ depending on need.

Install trusted extensions and understand what they do because they process traffic.

### Q19. What is Autorize extension?

Autorize helps test authorization by replaying requests with another user's cookies or without authentication.

It is useful for IDOR and broken access control testing, but results need manual validation.

### Q20. What is Param Miner?

Param Miner helps discover hidden parameters, headers, and cache poisoning vectors.

It is useful when testing for hidden functionality and input handling.

### Q21. How do you test IDOR with Burp?

Create two users, capture a request for user A's resource, change object ID to user B's resource, send using user A's session in Repeater, and observe whether access is allowed.

Also test APIs, JSON fields, headers, and indirect object references.

### Q22. How do you test SSRF with Burp?

Identify URL-fetching functionality, send a controlled callback URL, check Collaborator/OAST interaction, then test filters for redirects, private IP ranges, localhost, and metadata endpoints within authorized scope.

Document impact and recommend allowlists, egress controls, and metadata protection.

### Q23. How do you test JWT with Burp?

Decode JWT, inspect header and claims, check algorithm, expiry, issuer, audience, signature validation, role claims, and token storage. Attempt safe negative tests like expired token or modified claim to verify rejection.

Never rely on client-side claims without server validation.

### Q24. How do you test access control with Burp?

Capture requests from different roles, replay high-privilege requests as low-privilege users, test object ID changes, hidden endpoints, method changes, and direct API access.

Server response should enforce authorization regardless of UI.

## OWASP ZAP

### Q25. What is OWASP ZAP?

OWASP ZAP is an open-source web application security scanner and proxy. It supports manual proxying, passive scan, active scan, spidering, API scanning, and automation.

It is widely used for DAST in DevSecOps.

### Q26. What is ZAP baseline scan?

ZAP baseline scan performs passive scanning against a target without active attacks. It is useful for CI checks against staging or review apps.

It is safer than active scanning but less comprehensive.

### Q27. What is ZAP active scan?

Active scan sends attack payloads to test for vulnerabilities. It can find issues like injection, XSS, and misconfigurations.

Run active scans only in authorized environments because they may affect application state.

### Q28. What is ZAP spider?

Spider crawls links and discovers pages/endpoints. AJAX spider helps discover JavaScript-driven routes.

Good crawling improves scan coverage.

### Q29. How do you use ZAP in CI/CD?

Run baseline scans on pull request review apps or staging, publish reports, fail builds based on policy, and run deeper authenticated scans on schedule.

Tune rules to avoid noise.

### Q30. Burp vs ZAP?

Burp is widely used for manual testing and has strong professional features. ZAP is open-source and automation-friendly. Both can proxy traffic and scan web apps.

Choice depends on budget, workflow, and testing needs.

## Nmap And Network Tools

### Q31. What is Nmap?

Nmap is a network discovery and port scanning tool. It identifies hosts, open ports, services, versions, and sometimes operating systems or scripts.

Use only on authorized targets.

### Q32. What does an open port mean?

An open port means a service is listening and accepting connections. It may be intended or accidental.

Security review checks whether the service should be exposed and whether it is patched and authenticated.

### Q33. What is service version detection?

Service version detection tries to identify the software and version running on open ports.

It helps assess whether exposed services may be vulnerable.

### Q34. What is Nmap NSE?

NSE means Nmap Scripting Engine. It runs scripts for discovery, vulnerability checks, brute-force checks, and service enumeration.

Use NSE carefully because some scripts are intrusive.

### Q35. What is masscan?

masscan is a very fast port scanner. It is useful for large-scale authorized scanning.

It can generate high traffic, so use it only with approval and rate limits.

### Q36. What is testssl.sh?

testssl.sh checks TLS/SSL configuration, protocols, ciphers, certificates, and vulnerabilities.

It is useful for HTTPS security reviews.

### Q37. What is SSL Labs?

SSL Labs provides online TLS testing for public websites. It grades certificate and TLS configuration.

It is useful for external public endpoints but not internal systems.

### Q38. How do you test TLS?

Check certificate validity, hostname, expiry, TLS versions, weak ciphers, HSTS, redirects, and mixed content.

Disable old protocols and weak ciphers.

## Nuclei, Nikto, And Recon

### Q39. What is Nuclei?

Nuclei is a fast template-based scanner. It uses YAML templates to detect exposures, CVEs, misconfigurations, technologies, and panels.

It is powerful but findings must be validated.

### Q40. What are Nuclei templates?

Templates define checks for specific patterns, vulnerabilities, or exposures. They can be community-provided or custom.

Review templates before using them in sensitive environments.

### Q41. What is Nikto?

Nikto scans web servers for common misconfigurations, dangerous files, outdated software, and default content.

It is useful for baseline checks but can be noisy.

### Q42. What is httpx?

httpx is a tool often used in recon workflows to probe HTTP services and collect metadata like status codes, titles, technologies, and response data.

Use only within authorized scope.

### Q43. What is subdomain enumeration?

Subdomain enumeration discovers subdomains belonging to a domain. It helps map external attack surface.

Tools include amass, subfinder, assetfinder, DNS records, and certificate transparency logs.

### Q44. What is subdomain takeover?

Subdomain takeover occurs when a DNS record points to an unclaimed external service, allowing an attacker to claim it and serve content under the organization's subdomain.

Fix by removing stale DNS records or claiming the service.

### Q45. What is directory brute forcing?

Directory brute forcing tries common paths to discover hidden files or directories. Tools include ffuf, gobuster, and dirsearch.

Use carefully and with rate limits.

### Q46. What is ffuf?

ffuf is a fast web fuzzer used for directory discovery, parameter discovery, virtual host discovery, and content fuzzing.

It is powerful and should be scoped and rate-limited.

### Q47. What is gobuster?

gobuster discovers directories, files, DNS names, and virtual hosts through wordlists.

It is useful in recon but can generate many requests.

### Q48. What is wordlist risk?

Large wordlists can create heavy traffic and trigger alerts or instability. Use approved wordlists and rate limits.

Professional testing balances coverage and safety.

## API Testing Tools

### Q49. What is Postman?

Postman is an API client used to create, send, test, and organize API requests. It supports collections, environments, authentication, and scripts.

Security testers use it to understand and test APIs manually.

### Q50. What is curl?

curl is a command-line tool for sending HTTP requests. It is useful for quick API testing, headers, tokens, and reproducing findings.

In reports, curl commands can show reproducible proof of concept.

### Q51. What is OpenAPI/Swagger?

OpenAPI is a specification for describing REST APIs. Swagger tools can render and test OpenAPI definitions.

Security use: API specs help identify endpoints, parameters, auth schemes, and expected schemas.

### Q52. How do you test API authorization?

Use different users/roles, replay requests, change object IDs, test missing tokens, test lower-privilege tokens, check method-level authorization, and verify object/property-level access.

API auth flaws are often severe.

### Q53. How do you test API rate limiting?

Send repeated requests within authorized limits and observe status codes, response headers, lockouts, or throttling. Test login, OTP, password reset, search, and expensive endpoints.

Avoid harmful load against production.

### Q54. What is GraphQL testing?

GraphQL testing checks schema exposure, introspection, authorization per resolver, query depth/complexity, batching abuse, injection, and excessive data exposure.

GraphQL needs object-level and field-level authorization.

### Q55. What is introspection in GraphQL?

Introspection allows clients to query schema details. It is useful for development but may expose API structure in production.

Disabling introspection is not enough; authorization still matters.

## Browser And Manual Analysis

### Q56. How do browser developer tools help AppSec?

Developer tools show network requests, storage, cookies, console errors, DOM, JavaScript, source maps, and local/session storage.

They help understand frontend behavior and token storage.

### Q57. What do you check in browser storage?

Check localStorage, sessionStorage, IndexedDB, cookies, and cache for tokens, secrets, personal data, or sensitive configuration.

Sensitive tokens in localStorage are more exposed to XSS than HttpOnly cookies.

### Q58. What are source maps?

Source maps map minified JavaScript back to original source. If exposed, they may reveal source code, comments, endpoints, or secrets.

Source maps in production should be reviewed for sensitivity.

### Q59. What is JavaScript endpoint discovery?

It means reviewing JavaScript files to find API endpoints, feature flags, routes, keys, or hidden functionality.

Do not assume hidden frontend endpoints are authorized server-side.

### Q60. What is localStorage token risk?

Tokens in localStorage can be read by JavaScript. If XSS occurs, attackers can steal them.

HttpOnly secure cookies can reduce theft risk, though they require CSRF protections depending on design.

## Header And Config Tools

### Q61. What is securityheaders.com?

securityheaders.com checks public websites for common browser security headers. It is useful for quick review.

Missing headers should be prioritized based on application context.

### Q62. What is CSP Evaluator?

CSP Evaluator analyzes Content Security Policy for weaknesses such as unsafe sources or bypassable patterns.

It helps improve CSP quality.

### Q63. What is Wappalyzer?

Wappalyzer identifies technologies used by websites, such as frameworks, CMS, analytics, and servers.

Technology detection helps focus testing but can be inaccurate.

### Q64. What is Retire.js?

Retire.js detects vulnerable JavaScript libraries. It can run as a browser extension, CLI, or Burp extension.

It is useful for frontend dependency checks.

### Q65. What is builtwith-style recon?

Technology recon identifies frameworks, hosting, CDNs, analytics, and third-party services. It helps understand attack surface and dependency risk.

Do not rely solely on technology fingerprints for conclusions.

## Web Testing Scenario Questions

### Q66. Burp shows admin API request hidden in frontend JS. What do you do?

Test whether the API enforces server-side authorization. Try calling it as a normal user or unauthenticated user within scope.

Hidden frontend code is not security.

### Q67. Nmap finds port 22 open on production server. What do you do?

Validate whether SSH exposure is intended. Check source restrictions, authentication method, bastion/VPN alternatives, and logs. Recommend restricting access or using managed session tools.

Public SSH increases attack surface.

### Q68. ZAP reports XSS but you cannot reproduce. What do you do?

Inspect request/response, check context, browser execution, encoding, content type, and whether payload actually runs. If not reproducible, mark false positive with evidence or keep as needs review.

Validation matters.

### Q69. Nuclei reports CVE on a service. What do you do?

Confirm version, endpoint, template logic, exploitability, and whether the vulnerable feature is enabled. Then prioritize based on exposure and impact.

Template match is not always proof.

### Q70. Nikto reports many missing headers. What do you do?

Group findings, validate which headers matter, assess context, and recommend appropriate headers. Do not inflate severity for every missing header.

Headers are important defense in depth.

### Q71. Postman collection exposes production token. What do you do?

Rotate token, remove it from collection/history, restrict collection sharing, move secrets to environment variables or secret storage, and add secret scanning.

Shared API collections are common leak points.

### Q72. Browser localStorage contains JWT. What is the risk?

If XSS exists, JavaScript can steal the token. Risk depends on token scope, expiry, refresh strategy, and CSP.

Recommend secure storage strategy and XSS prevention.

### Q73. API has Swagger publicly exposed. Is it a vulnerability?

Not always. Public API docs may be intentional. Risk increases if docs expose internal endpoints, admin functions, schemas, or unauthenticated testing.

Ensure authorization is enforced regardless of documentation exposure.

### Q74. Directory brute force finds backup file. What do you do?

Validate content safely, report sensitive exposure, remove file from web root, rotate secrets if exposed, check logs for access, and add deployment controls to prevent backup files in production.

Backups often contain sensitive data.

### Q75. How do you summarize web testing tools?

Use Burp/ZAP for HTTP testing, Postman/curl for APIs, Nmap for network exposure, Nuclei/Nikto for baseline checks, browser dev tools for frontend analysis, and TLS/header tools for configuration.

Tools support methodology; they do not replace understanding scope, roles, and business logic.

