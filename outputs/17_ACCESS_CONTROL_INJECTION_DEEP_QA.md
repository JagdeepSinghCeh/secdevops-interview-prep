# Access Control And Injection Deep Q&A

This file provides interview drills for broken access control, IDOR/BOLA, SQL injection, command injection, NoSQL injection, path traversal, template injection, and related issues. Testing guidance assumes explicit authorization and test accounts.

## Broken Access Control

### Q1. What is broken access control?

Broken access control occurs when an application fails to enforce what an authenticated or unauthenticated user is allowed to access or perform. It can expose another user's data, administrative functions, sensitive files, or unauthorized business actions.

The most important point is that authorization must be enforced server-side for every request. Hiding buttons or routes in the UI is not access control.

### Q2. Why is broken access control dangerous?

It can lead directly to data breaches, account takeover, privilege escalation, fraud, unauthorized changes, and regulatory impact. It is often easy to exploit because an attacker may only need to change an identifier, URL, HTTP method, or role value.

The business impact is frequently greater than the technical complexity.

### Q3. What is horizontal privilege escalation?

Horizontal privilege escalation occurs when a user accesses another user's resources at the same privilege level. For example, customer A accesses customer B's invoice by changing an object identifier.

Testing requires two authorized test accounts with the same role. The server should verify ownership or permitted relationship for every object.

### Q4. What is vertical privilege escalation?

Vertical privilege escalation occurs when a lower-privileged user gains access to higher-privileged functions. For example, a normal user invokes an administrative API.

The fix is centralized server-side authorization based on roles or permissions, with deny-by-default behavior.

### Q5. What is IDOR?

IDOR means Insecure Direct Object Reference. It occurs when an application exposes a reference to an object and does not verify whether the current user may access it.

The object reference may be an integer, UUID, filename, account number, database key, or encoded value. The identifier format does not replace authorization.

### Q6. What is BOLA?

BOLA means Broken Object Level Authorization. It is the API-security term for failures where a caller can access or manipulate objects they do not own or lack permission to access.

It is closely related to IDOR and is common in REST, GraphQL, mobile backends, and microservices.

### Q7. How do you test IDOR safely?

Create two test users. Capture a request where user A accesses an object belonging to user A. Replace the object reference with an object belonging to user B while keeping user A's session. Verify whether the server denies access.

Check URL paths, query parameters, JSON fields, headers, GraphQL variables, filenames, and nested object identifiers.

### Q8. Are UUIDs a fix for IDOR?

No. UUIDs make identifiers harder to guess, but they can leak through logs, links, notifications, APIs, browser history, or related objects. If a UUID becomes known, missing authorization remains exploitable.

Use UUIDs only as defense in depth alongside server-side authorization.

### Q9. What is forced browsing?

Forced browsing means directly requesting hidden or unlinked paths such as administrative routes, backups, debug endpoints, or old API versions.

Applications must enforce authorization regardless of whether a route appears in the UI.

### Q10. What is function-level authorization?

Function-level authorization controls whether a user may execute a particular operation, such as creating users, approving payments, or viewing reports.

Broken function-level authorization occurs when a low-privileged identity can invoke high-privileged operations directly.

### Q11. What is property-level authorization?

Property-level authorization controls which fields a user may read or modify. A user may be allowed to update their display name but not fields like role, account status, price, or credit limit.

This is particularly important in APIs that automatically bind JSON properties to backend objects.

### Q12. What is mass assignment?

Mass assignment happens when user-controlled input is automatically mapped to an object without allowlisting editable fields. An attacker may add fields such as `role`, `isAdmin`, `approved`, or `balance`.

Fix by defining explicit request schemas and allowlisting fields that each role may modify.

### Q13. How do you test mass assignment?

Review API responses, documentation, JavaScript, and related endpoints for sensitive property names. Add a harmless test modification to a restricted field using a test account and check whether the server accepts it.

Do not modify real financial, privileged, or customer records during testing.

### Q14. What is method-based access control bypass?

An endpoint may protect `POST` but forget to protect `PUT`, `PATCH`, or `DELETE`, or a proxy and application may interpret methods differently.

Authorization must apply consistently to every supported method and route.

### Q15. What is role manipulation?

Role manipulation occurs when a user can change a role value in a token, request, cookie, profile update, or registration flow and the server trusts it.

Roles must come from trusted server-side identity and authorization data, not arbitrary client input.

### Q16. What is tenant isolation?

Tenant isolation ensures one organization or customer cannot access another tenant's data or resources in a multi-tenant application.

Every data query and object access should include trusted tenant context. Tenant IDs supplied by clients must be verified against the authenticated identity.

### Q17. How do you test tenant isolation?

Use authorized accounts from two test tenants. Attempt cross-tenant object access, search, exports, file downloads, API calls, and administrative functions.

Also test indirect references such as report IDs, attachment IDs, and integration configurations.

### Q18. What is authorization caching risk?

Applications may cache permissions or resources incorrectly, causing stale or cross-user access. A role removed from a user may remain effective because of cached tokens or sessions.

Permission changes should invalidate or refresh relevant authorization state.

### Q19. Why must authorization occur after authentication?

Authentication establishes identity. Authorization uses that trusted identity to decide access. Without reliable authentication, authorization decisions cannot be trusted.

However, authentication alone never implies permission to every resource.

### Q20. How do you remediate broken access control?

Use centralized authorization, deny by default, object ownership or relationship checks, role/permission checks, trusted tenant context, field-level allowlists, and automated negative tests.

Log denied attempts and review high-risk authorization failures.

## SQL Injection

### Q21. What is SQL injection?

SQL injection occurs when untrusted input changes the structure or logic of a SQL query. It happens when applications combine user input with SQL commands unsafely.

Impact may include authentication bypass, unauthorized data access, data modification, or broader database compromise depending on configuration and privileges.

### Q22. What is the root cause of SQL injection?

The root cause is failure to separate code from data, commonly through string concatenation when building SQL statements.

The primary remediation is parameterized queries or prepared statements.

### Q23. What is error-based SQL injection?

Error-based SQL injection uses database error messages to learn query structure, database type, or data. Verbose errors make exploitation easier.

Fix the injection root cause and return generic errors to users while logging details securely.

### Q24. What is union-based SQL injection?

Union-based SQL injection attempts to combine attacker-controlled query results with the original query using SQL set operations.

Parameterized queries prevent user input from becoming query syntax.

### Q25. What is boolean-based blind SQL injection?

Boolean-based blind SQL injection infers information by causing true and false conditions that produce different responses.

Even when no database error or data is directly displayed, unsafe queries can still be vulnerable.

### Q26. What is time-based blind SQL injection?

Time-based blind SQL injection infers conditions through controlled response delays. It is used when visible response content does not change.

During authorized testing, use minimal safe verification and avoid creating load or long delays.

### Q27. What is second-order SQL injection?

Second-order SQL injection occurs when malicious input is stored safely at first but later inserted unsafely into another SQL query.

All database queries need parameterization, including those using previously stored data.

### Q28. Is escaping enough to prevent SQL injection?

Escaping is fragile and database-specific. Parameterized queries are the preferred defense because they separate SQL instructions from data.

Input validation and least privilege add defense in depth.

### Q29. Do ORMs prevent all SQL injection?

No. ORMs reduce risk when used through parameterized APIs, but raw queries, dynamic filters, unsafe ordering, and query fragments can reintroduce injection.

Review ORM escape hatches and custom query construction.

### Q30. How do you test SQL injection safely?

Identify parameters that influence database queries and use non-destructive test inputs to observe errors, response differences, or safe timing behavior in an authorized environment. Review source code when available.

Do not extract real data or modify records merely to prove the issue.

### Q31. What tools are used for SQL injection testing?

Burp Suite, OWASP ZAP, code review, SAST tools, database logs, and authorized specialized testing tools can assist. Automated output must be manually validated.

The interview focus should remain on methodology and remediation, not only tool names.

### Q32. What is database least privilege?

The application database account should have only required permissions. A read-only service should not be able to create users, alter schemas, or write data.

Least privilege reduces SQL injection impact but does not fix SQL injection.

### Q33. What is a stored procedure and can it prevent SQL injection?

Stored procedures can reduce injection if parameters are handled safely. They can still be vulnerable if they construct dynamic SQL through string concatenation.

Safe implementation matters more than the use of stored procedures alone.

### Q34. How does a WAF help with SQL injection?

A WAF can detect and block common SQL injection patterns and provide temporary virtual patching. It also provides attack visibility.

It cannot reliably understand all queries or business context and must not replace parameterized code.

### Q35. How do you rate SQL injection severity?

Consider whether the endpoint is reachable, authentication required, data sensitivity, database privileges, read/write capability, exploit reliability, and network architecture.

Internet-facing SQL injection against sensitive data is usually critical or high.

## NoSQL And Other Injection

### Q36. What is NoSQL injection?

NoSQL injection occurs when user input changes a query sent to a NoSQL database. It may abuse query operators, JavaScript evaluation, or unsafe object construction.

Use strict schemas, reject unexpected operators, and use safe query APIs.

### Q37. How can JSON input cause NoSQL injection?

An API expecting a string may accept an object containing database operators. If the application passes that object directly into a query, authentication or filtering logic may change.

Validate types and schemas before querying.

### Q38. What is LDAP injection?

LDAP injection occurs when untrusted input modifies an LDAP directory query. It may allow authentication bypass, directory enumeration, or unauthorized access.

Use safe LDAP APIs, escaping, input validation, and least-privilege directory accounts.

### Q39. What is XPath injection?

XPath injection occurs when input changes an XPath query used to access XML data. It is conceptually similar to SQL injection.

Use parameterized or safe query methods and strict validation.

### Q40. What is header injection?

Header injection occurs when untrusted input adds or modifies HTTP headers, often through newline characters. It may lead to response splitting, cache poisoning, or cookie manipulation.

Reject control characters and use safe framework APIs for headers.

### Q41. What is email header injection?

Email header injection occurs when input such as a recipient or subject inserts additional email headers. It may enable spam or message manipulation.

Use email libraries, validate addresses, and reject newline characters.

### Q42. What is log injection?

Log injection occurs when untrusted input inserts fake log entries or terminal control characters. It can confuse investigations or exploit log viewers.

Use structured logging, encode control characters, and keep trusted metadata separate.

### Q43. What is CRLF injection?

CRLF injection uses carriage-return and line-feed characters to alter line-based protocols such as HTTP headers or logs.

Framework-safe APIs and rejecting control characters are standard defenses.

### Q44. What is XML injection?

XML injection changes the structure or meaning of XML documents through untrusted input.

Use XML libraries, schema validation, and safe encoding instead of string concatenation.

### Q45. What is expression language injection?

Expression language injection occurs when attacker-controlled input is evaluated by a server-side expression engine.

Do not evaluate untrusted expressions. Restrict context and use safe templating APIs.

## Command Injection

### Q46. What is OS command injection?

OS command injection occurs when untrusted input becomes part of an operating system command. It may allow execution of arbitrary commands under the application's identity.

The safest remediation is to avoid shell commands and use language/library APIs.

### Q47. What is the root cause of command injection?

The root cause is combining untrusted input with shell commands or command interpreters without safe argument handling and validation.

Shell metacharacter filtering alone is usually fragile.

### Q48. Command injection vs code injection?

Command injection targets an operating-system command interpreter. Code injection causes input to be interpreted as code by a language or runtime.

Both result from treating untrusted data as instructions.

### Q49. How do you test command injection safely?

In an authorized environment, identify features that call system utilities, then use harmless inputs that demonstrate altered behavior without changing files or accessing sensitive data. Source review is especially valuable.

Avoid destructive commands or persistent changes.

### Q50. How do you fix command injection?

Replace shell calls with safe APIs, pass arguments as separate parameters without a shell, allowlist expected values, run under least privilege, and isolate the process.

Logging and runtime monitoring provide additional detection.

### Q51. Is input sanitization enough for command injection?

No. Shell syntax is complex and platform-specific. The preferred approach is avoiding the shell and using safe APIs.

Allowlisting constrained values is stronger than attempting to remove dangerous characters.

### Q52. What application features commonly create command injection?

File conversion, network diagnostics, image processing, archive operations, backup tools, report generation, and administrative utilities may invoke system commands.

Review any code that builds command strings from request data.

### Q53. How does least privilege reduce command injection impact?

If the application runs as a dedicated low-privilege user, injected commands have fewer permissions. They may be unable to modify system files or access unrelated secrets.

Least privilege is a mitigation, not the root fix.

### Q54. What runtime controls help detect command injection?

EDR, container runtime tools, Falco, process monitoring, command audit logs, and SIEM alerts can detect unusual shell execution or child processes.

Detection is important because prevention may fail.

### Q55. How can command injection become cloud compromise?

A compromised cloud workload may access instance credentials, service account tokens, environment secrets, metadata, or internal services. Over-privileged workload IAM increases the blast radius.

Use least-privilege roles, metadata protection, network controls, and secret management.

## Path Traversal And File Access

### Q56. What is path traversal?

Path traversal occurs when user-controlled file paths escape an intended directory and access other files. It is often associated with parent-directory sequences or path normalization differences.

Impact includes reading secrets, source code, configuration, keys, or logs.

### Q57. What is local file inclusion?

Local file inclusion occurs when an application includes or processes a local file based on unsafe user input. Depending on technology, it may expose files or lead to code execution.

Use fixed templates and server-side mappings instead of direct paths.

### Q58. What is remote file inclusion?

Remote file inclusion occurs when an application includes remote content as executable or interpretable code. It is technology-dependent and can lead to code execution.

Disable remote includes and never treat user-provided locations as code.

### Q59. Why is checking only for `..` insufficient?

Path traversal can use encoded characters, alternate separators, normalization differences, symlinks, or platform-specific paths. Simple substring filtering is fragile.

Use allowlisted file identifiers and canonical path validation.

### Q60. What is canonicalization?

Canonicalization converts a path to a normalized absolute representation. The application can then verify it remains inside the intended base directory.

Validation must occur after decoding and normalization.

### Q61. How do you test path traversal safely?

Use a controlled test file outside the intended directory and verify whether the application can access it. Avoid reading real secrets or operating-system credential files.

Test path parameters, filenames, archive extraction, and download endpoints.

### Q62. How do you prevent path traversal?

Use server-generated file IDs, map IDs to approved paths, canonicalize paths, verify base-directory containment, restrict filesystem permissions, and avoid direct user-controlled paths.

Store sensitive files outside web-accessible directories.

### Q63. What is arbitrary file read?

Arbitrary file read allows an attacker to read files they should not access. Path traversal, local file inclusion, or unsafe download APIs can cause it.

Severity depends on accessible files and application privileges.

### Q64. What is arbitrary file write?

Arbitrary file write allows writing attacker-controlled content to unintended locations. It can lead to configuration changes, web shells, persistence, or code execution.

Restrict destinations, filenames, permissions, and file types.

### Q65. What is Zip Slip?

Zip Slip is path traversal during archive extraction. Malicious archive entries contain paths that escape the extraction directory.

Validate each extracted path after normalization and use safe archive libraries.

## Server-Side Template Injection

### Q66. What is server-side template injection?

SSTI occurs when untrusted input is interpreted as a server-side template expression. Depending on the engine, attackers may access objects, secrets, files, or execute code.

The root cause is using user input as template source instead of template data.

### Q67. SSTI vs XSS?

SSTI executes or evaluates on the server. XSS executes in the victim's browser. SSTI may lead to server compromise, while XSS affects browser sessions and data.

Some template output can also create XSS if encoded incorrectly.

### Q68. Where does SSTI occur?

It can occur in email templates, report generation, CMS features, notification systems, document generation, and custom user templates.

Template engines should process trusted templates with untrusted values safely encoded as data.

### Q69. How do you test SSTI safely?

Use harmless mathematical or string expressions specific to the suspected template context and observe whether the server evaluates them. Do not attempt system command execution for basic validation.

Source review can confirm unsafe template compilation.

### Q70. How do you prevent SSTI?

Never treat user input as template code. Use predefined templates, pass user data as variables, sandbox template engines where user templates are required, and restrict accessible objects/functions.

Run the service with least privilege.

## Injection Scenarios

### Q71. SQL injection exists on an authenticated report endpoint. Is it critical?

It may be critical or high depending on data sensitivity, user role required, database privileges, and exploitability. Authentication reduces exposure but does not remove impact.

Assess whether a low-privileged user can access broad data or modify records.

### Q72. SQL injection is blocked by WAF. Can the finding be closed?

No. WAF is a compensating control and may be bypassed or disabled. The application query must be fixed with parameterization.

Document WAF as temporary mitigation.

### Q73. IDOR exposes only test data. Is the vulnerability real?

If production uses the same authorization design, the weakness is real even if demonstrated with test data. Rate severity based on likely production impact and affected resources.

Use test data for safe proof of concept.

### Q74. Admin endpoint returns 404 to users. Is that secure?

Only if the server actually enforces authorization. A 404 may intentionally hide existence, but testers should verify direct requests, alternate methods, and API routes.

Response code alone is not proof of authorization.

### Q75. A file download uses random filenames. Does that prevent access-control issues?

No. Random names make guessing harder but do not enforce permission. Links may leak, and users may still share or discover references.

The server must verify access for every download.

### Q76. SAST reports command injection but input is allowlisted. What do you do?

Review the allowlist, command construction, and whether shell execution is used. A strict fixed-value allowlist may make exploitation impossible, but replacing shell execution with a safe API is still preferable.

Document evidence if marked false positive.

### Q77. An API lets managers access all employees in their department. How do you test IDOR?

Test users from different departments and verify the manager can access only permitted employees. Include direct IDs, search, exports, attachments, and related endpoints.

Authorization may be relationship-based rather than simple ownership.

### Q78. Database account is read-only. Does that solve SQL injection?

No. It reduces modification impact but may still allow sensitive data disclosure. The query must be parameterized.

Least privilege is defense in depth.

### Q79. Path traversal reads application config containing cloud keys. What is the chain?

Path traversal causes file disclosure, leaked cloud keys enable cloud API access, and over-privileged IAM increases impact. Response includes fixing path handling, rotating keys, reviewing cloud logs, and moving to workload identities.

This is a strong AppSec-to-CloudSec interview chain.

### Q80. How do you summarize access-control and injection testing?

For access control, test every role, object, property, method, and tenant relationship using server-side requests. For injection, identify interpreters and verify whether untrusted data becomes instructions.

Always validate safely, explain business impact, fix root cause, and add automated regression tests.

