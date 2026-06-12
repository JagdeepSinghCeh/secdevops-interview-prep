# Secure Code Review And Language-Specific Q&A

This file prepares you to review security-sensitive code across Java, JavaScript/Node.js, Python, .NET, Go, PHP, and frontend applications. The goal is to recognize dangerous data flows and framework misuse, not memorize every library.

## Secure Code Review Fundamentals

### Q1. What is secure code review?

Secure code review examines source code to identify vulnerabilities, unsafe design assumptions, insecure framework usage, and missing controls.

It combines automated SAST with human understanding of data flow, authorization, business logic, and architecture.

### Q2. What should you understand before reviewing code?

Understand the application purpose, architecture, entry points, trust boundaries, identities, roles, sensitive data, dependencies, deployment, and high-risk workflows.

Review without context creates noise and misses business risk.

### Q3. What are sources and sinks?

A source introduces untrusted data, such as HTTP parameters, headers, files, messages, or database values. A sink performs a sensitive operation, such as a SQL query, shell command, HTML output, file access, or redirect.

Review whether data reaches a sink and what validation or encoding occurs.

### Q4. What is taint analysis?

Taint analysis tracks untrusted data from sources through transformations to sensitive sinks. SAST tools use it to detect injection and data exposure.

Human review validates whether sanitization and execution context make the path exploitable.

### Q5. What are security-sensitive code areas?

Authentication, authorization, session handling, cryptography, secrets, deserialization, file handling, database queries, outbound requests, redirects, logging, payments, and administrative workflows deserve extra review.

CODEOWNERS can require specialist review for these paths.

### Q6. What is a dangerous function?

A dangerous function performs a powerful operation that becomes vulnerable with attacker-controlled input. Examples include shell execution, dynamic code evaluation, raw SQL, unsafe deserialization, and HTML rendering.

The function is not always vulnerable; input and context determine risk.

### Q7. What is secure-by-construction API?

It is an API designed so normal use is safe, such as parameterized database methods or auto-escaping templates.

Prefer safe abstractions over repeatedly asking developers to sanitize strings manually.

### Q8. What is a security invariant?

A security invariant is a condition that must always hold, such as "users may access only objects in their tenant" or "only approved artifacts deploy."

Code review should verify invariants across every relevant path.

### Q9. Why review negative paths?

Error, cancellation, retry, timeout, and partial-failure paths may skip authorization, cleanup, validation, or logging.

Attackers deliberately trigger unusual states.

### Q10. How do you review a pull request securely?

Read the purpose and threat context, inspect changed code and related callers, review tests and dependencies, check sensitive data flows, run automated checks, and request specific fixes.

Do not review only the changed lines when security depends on surrounding behavior.

## Input, Output, And Authorization Review

### Q11. How do you review input validation?

Verify expected type, format, length, range, encoding, allowed values, and business constraints. Confirm validation occurs server-side before sensitive processing.

Prefer schema validation and allowlists.

### Q12. How do you review output encoding?

Identify the output context: HTML body, attribute, JavaScript, CSS, URL, SQL, shell, or logs. Confirm the correct context-aware encoding or safe API is used.

One generic encoder does not secure every context.

### Q13. How do you review SQL access?

Look for string concatenation, raw queries, dynamic identifiers, unsafe ORM escape hatches, excessive database privileges, and sensitive data in errors.

Require parameterized values and allowlisted dynamic identifiers.

### Q14. How do you review authorization?

Identify the trusted identity and verify role, tenant, ownership, relationship, and protected properties at the server-side operation.

Check read and write paths, bulk operations, exports, background jobs, and alternate endpoints.

### Q15. How do you review file handling?

Trace filenames, paths, upload content, archive extraction, storage permissions, download authorization, and parser usage.

Prefer generated identifiers and non-executable storage.

### Q16. How do you review outbound HTTP requests?

Check whether users control scheme, host, port, path, redirects, headers, or DNS resolution. Review egress controls and timeout/resource limits.

This is essential for SSRF prevention.

### Q17. How do you review logging code?

Ensure security events are logged with identity and context while passwords, tokens, keys, sensitive bodies, and regulated data are redacted.

Prevent log injection through structured logging.

### Q18. How do you review error handling?

External errors should not expose stacks, queries, secrets, or infrastructure. Internal logs should preserve enough context for investigation.

Authorization and transaction failures should fail securely.

### Q19. How do you review random-number use?

Security tokens, reset links, session identifiers, and cryptographic nonces require a cryptographically secure random generator.

General-purpose pseudo-random functions are unsuitable.

### Q20. How do you review time and expiry logic?

Check time zones, clock skew, expiration comparison, replay windows, integer units, token refresh, and server-side enforcement.

Clients must not decide whether authorization has expired.

## Java Security Review

### Q21. What Java APIs commonly require security review?

JDBC queries, `Runtime.exec`, `ProcessBuilder`, object deserialization, XML parsers, expression languages, reflection, file APIs, URL connections, and template engines are high-risk.

Review framework-specific safe alternatives.

### Q22. How do you prevent SQL injection in Java?

Use `PreparedStatement` with bound parameters or safe ORM parameter binding. Do not concatenate request values into SQL or JPQL.

Dynamic table or column names require strict allowlists.

### Q23. What is Java deserialization risk?

`ObjectInputStream` and compatible native deserialization can instantiate attacker-influenced object graphs and gadget chains.

Avoid native deserialization for untrusted data and use simple schema-validated formats.

### Q24. How do you secure Java XML parsing?

Disable DTDs, external entities, and external schema access using parser-specific secure features. Keep parser libraries updated.

Test configuration because defaults vary.

### Q25. What is Spring Security authorization risk?

Routes may be authenticated but lack method or object authorization. Misordered matchers or broad permit rules may expose endpoints.

Use deny-by-default configuration and method-level checks for sensitive operations.

### Q26. What is mass assignment in Spring?

Automatic request binding may populate sensitive object fields. Using persistence entities directly as request models increases risk.

Use dedicated DTOs and explicit field mappings.

### Q27. What is Java path traversal review?

Check `File`, `Path`, upload, extraction, and download code. Normalize paths and verify the final path remains below an approved base.

Prefer mapping opaque IDs to server-owned paths.

### Q28. What is unsafe Java command execution?

Passing a concatenated string through a shell can turn user input into commands. Even `ProcessBuilder` requires validated separate arguments.

Prefer Java APIs over external commands.

### Q29. How do you review Java logging?

Use parameterized structured logging, avoid string concatenation with control characters, and redact credentials and personal data.

Review logging configurations and vulnerable logging dependencies too.

### Q30. What tools help Java code review?

CodeQL, Semgrep, SonarQube, SpotBugs with security plugins, dependency scanners, IDE analysis, and manual review help.

Tool findings must be traced through actual framework behavior.

## JavaScript And Node.js Security Review

### Q31. What Node.js sinks deserve attention?

`eval`, `Function`, `child_process`, dynamic `require`, filesystem paths, template rendering, redirects, database queries, and outbound HTTP clients are high-risk.

Trace request data into these operations.

### Q32. What is prototype pollution?

Prototype pollution modifies properties on shared JavaScript object prototypes through unsafe merges or property assignment.

It can alter authorization, configuration, or code flow. Reject dangerous keys and update libraries.

### Q33. How do you prevent command injection in Node.js?

Avoid shell execution. Use library APIs or `spawn`/`execFile` with separate validated arguments and shell disabled.

Strictly allowlist constrained values.

### Q34. What is Express security review?

Review middleware order, authentication, authorization, proxy trust, body-size limits, CORS, cookies, error handling, rate limiting, and security headers.

An authorization middleware placed after a route provides no protection.

### Q35. What is `trust proxy` risk in Express?

Incorrect proxy trust may let clients spoof source IP or protocol headers, affecting rate limits, secure cookies, redirects, and audit logs.

Configure only known proxy hops.

### Q36. How do you review npm dependencies?

Check lockfile changes, new maintainers/packages, lifecycle scripts, known vulnerabilities, package age/reputation, and whether the dependency is necessary.

Use SCA, but also consider malicious-package risk.

### Q37. What is unsafe Node.js template rendering?

Rendering attacker-controlled template source can cause server-side template injection. Disabling automatic escaping may cause XSS.

Use fixed templates and pass input only as data.

### Q38. What is ReDoS?

Regular Expression Denial of Service occurs when a regex has catastrophic backtracking on attacker-controlled input.

Use safe patterns, length limits, timeouts, or non-backtracking engines.

### Q39. How do you review Node.js async errors?

Unhandled promise rejections, partial writes, retry behavior, and lost transaction context may create availability or authorization problems.

Ensure failures clean up securely and return controlled errors.

### Q40. What tools help Node.js review?

Semgrep, CodeQL, ESLint security plugins, npm audit, OSV-Scanner, Snyk, secret scanners, and manual review help.

Review package scripts and lockfiles, not only application source.

## Python Security Review

### Q41. What Python functions require attention?

`eval`, `exec`, `pickle`, `yaml.load` with unsafe loaders, `subprocess` with shell, filesystem paths, template rendering, dynamic imports, and HTTP clients require review.

Prefer safe alternatives.

### Q42. Why is pickle unsafe?

Pickle can execute arbitrary behavior during deserialization. Unpickling untrusted data may lead to code execution.

Use JSON or another data-only format with schema validation.

### Q43. How do you load YAML safely?

Use a safe loader that does not construct arbitrary Python objects. Validate the resulting schema and types.

Do not load attacker-controlled YAML through unsafe constructors.

### Q44. How do you prevent Python command injection?

Use `subprocess` with argument arrays and `shell=False`, validate each argument, and prefer native libraries.

Avoid building command strings.

### Q45. What is Django security review?

Review authentication decorators/middleware, object authorization, ORM raw queries, CSRF exemptions, allowed hosts, debug mode, file uploads, templates, and settings.

Django defaults help, but unsafe overrides can bypass them.

### Q46. What is Flask security review?

Review secret key management, session cookies, proxy configuration, CSRF, route authorization, debug mode, templates, uploads, and error handling.

Flask provides flexibility, so secure controls may require explicit configuration.

### Q47. What is Jinja2 security risk?

Auto-escaping may be disabled or bypassed, causing XSS. Rendering attacker-controlled template source can create SSTI.

Use fixed templates and safe variables.

### Q48. How do you review Python SQL?

Look for f-strings, `%` formatting, concatenation, and unsafe raw ORM queries. Use driver placeholders and parameter binding.

Do not quote placeholders manually.

### Q49. What is Python dependency risk?

PyPI typosquatting, dependency confusion, compromised maintainers, install scripts, and vulnerable packages are concerns.

Use lockfiles, hashes, private indexes, SCA, and controlled builds.

### Q50. What tools help Python review?

Bandit, Semgrep, CodeQL, SonarQube, pip-audit, OSV-Scanner, secret scanners, and manual framework review help.

Automated tools may not understand object authorization.

## .NET Security Review

### Q51. What .NET areas require attention?

SQL access, process execution, XML parsing, deserialization, Razor output, file paths, authorization attributes/policies, Data Protection keys, and configuration secrets are important.

Review both C# code and ASP.NET Core configuration.

### Q52. How do you prevent SQL injection in .NET?

Use parameterized `SqlCommand`, Entity Framework LINQ, or correctly bound stored procedure parameters.

Raw SQL and dynamic identifiers require careful review.

### Q53. What is .NET deserialization risk?

Legacy serializers and unsafe type handling can instantiate dangerous types. Avoid deserializing untrusted native object graphs.

Use `System.Text.Json` with constrained models and safe polymorphism.

### Q54. What is Razor output encoding?

Razor normally HTML-encodes expressions. Using raw HTML helpers or constructing scripts/attributes incorrectly can reintroduce XSS.

Sanitize intentional rich HTML.

### Q55. How do you review ASP.NET authorization?

Check global/default policies, `[Authorize]` and `[AllowAnonymous]`, role/policy definitions, object authorization, endpoint routing, and administrative APIs.

An authenticated controller can still expose another user's objects.

### Q56. What is ASP.NET Data Protection?

Data Protection protects cookies, tokens, and application data with managed keys. Key storage and sharing matter in scaled deployments.

Protect key rings and configure rotation correctly.

### Q57. What is .NET configuration secret risk?

Secrets in `appsettings.json`, source control, environment dumps, or publish artifacts may leak.

Use secret stores, managed identity, and protected production configuration.

### Q58. What tools help .NET review?

CodeQL, Semgrep, SonarQube, Roslyn analyzers, Microsoft security analyzers, NuGet SCA, and manual ASP.NET review help.

Keep framework and package versions supported.

## Go And PHP Review

### Q59. What Go APIs require attention?

`os/exec`, `text/template` versus `html/template`, SQL queries, filesystem paths, HTTP clients, TLS configuration, JSON decoding, and concurrency controls are important.

Go's explicit error handling requires reviewing every failure path.

### Q60. Why use `html/template` in Go?

It provides contextual auto-escaping for HTML output. `text/template` does not provide HTML-specific protection.

Avoid marking attacker-controlled content as trusted.

### Q61. How do you prevent Go SQL injection?

Use database-driver placeholders and bound parameters. Do not construct SQL with `fmt.Sprintf` using untrusted values.

Allowlist dynamic identifiers.

### Q62. What is Go HTTP client SSRF risk?

Redirects, DNS resolution, proxy environment variables, private IPs, and unlimited response bodies require review.

Use destination policy, timeouts, redirect checks, and response-size limits.

### Q63. What is Go concurrency security risk?

Race conditions can corrupt authorization state, duplicate transactions, or expose data. Use synchronization, atomic operations, transactions, and the race detector.

Business operations still need database-level atomicity.

### Q64. What PHP functions require attention?

`eval`, `include` with user input, shell functions, unserialize, dynamic SQL, file paths, upload handling, and raw output require review.

Legacy PHP applications often need configuration and dependency review.

### Q65. How do you prevent PHP SQL injection?

Use PDO or MySQLi prepared statements with bound parameters. Do not concatenate request variables into queries.

Database least privilege reduces impact.

### Q66. What is PHP object injection?

Unsafe `unserialize` on attacker-controlled data can invoke magic methods and gadget chains.

Use JSON or safe structured formats and avoid native object deserialization.

### Q67. What is PHP file inclusion risk?

User-controlled input in `include` or `require` may load unintended local or remote files.

Map fixed identifiers to trusted templates and disable remote inclusion.

### Q68. What tools help Go and PHP review?

gosec, staticcheck, govulncheck, Semgrep, CodeQL, SonarQube, Composer audit, SCA tools, and manual review help.

Tool support varies by language and framework.

## Frontend And Mobile-Client Review

### Q69. What should frontend code never be trusted to enforce?

Authorization, prices, roles, transaction rules, data filtering, or secret protection must not rely solely on frontend code.

Clients are controlled by users and can be modified.

### Q70. What is DOM XSS review?

Trace URL, message, storage, and API data into DOM sinks such as `innerHTML`, dynamic script creation, and unsafe URL navigation.

Use safe DOM APIs and sanitization for intentional HTML.

### Q71. What is `postMessage` risk?

Cross-window messages may be accepted from untrusted origins or contain unsafe data.

Validate exact origin, source window, message schema, and resulting action.

### Q72. What is client-side secret risk?

JavaScript bundles and mobile apps can be inspected. Embedded API keys, private credentials, or signing secrets cannot remain confidential.

Restrict public client identifiers and keep privileged operations server-side.

### Q73. What is source-map risk?

Production source maps may expose original code, comments, internal routes, or sensitive configuration.

Control access or omit them from public deployments when unnecessary.

### Q74. What is third-party JavaScript risk?

Third-party scripts execute with the page's privileges and may access DOM data and tokens.

Minimize providers, use CSP and integrity controls where applicable, and monitor changes.

## Code Review Scenarios

### Q75. A PR adds a raw SQL query. What do you ask?

Ask whether any value is attacker-controlled, whether parameters are bound, why ORM-safe methods are insufficient, what database privileges exist, and what tests cover injection.

Require a safe query or documented constrained identifier allowlist.

### Q76. A developer sanitizes input before shell execution. Is that enough?

Usually no. Replace the shell command with a library API or execute a fixed binary with separate allowlisted arguments.

Shell syntax and encoding are difficult to sanitize reliably.

### Q77. A controller has authentication but no object check. What is the risk?

Any authenticated user may access another user's resource, causing IDOR/BOLA.

Add ownership, tenant, or relationship authorization and negative tests.

### Q78. A logger records an entire request object. What do you do?

Review whether headers, cookies, tokens, passwords, files, and personal data are included. Replace broad logging with explicit allowlisted fields and redaction.

Rotate exposed credentials if logs already contain them.

### Q79. SAST marks a safe path as vulnerable. How do you review it?

Trace the complete data flow, confirm validation and sink behavior, test the framework API, and document why exploitation is impossible.

Suppress narrowly and keep the rationale.

### Q80. How do you summarize secure code review?

Understand the system, trace untrusted data, inspect sensitive operations and authorization, review failure paths and dependencies, validate tool findings, and convert recurring issues into safe libraries and automated tests.

The best review protects security invariants rather than only matching dangerous strings.

