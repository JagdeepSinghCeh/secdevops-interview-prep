# Server-Side And Business Logic Vulnerabilities Deep Q&A

This file covers SSRF, file upload, XXE, insecure deserialization, open redirect, request smuggling concepts, race conditions, rate-limit abuse, and business logic flaws. Testing guidance is for authorized labs and test environments only.

## SSRF

### Q1. What is SSRF?

SSRF means Server-Side Request Forgery. It occurs when an attacker can influence a server to send requests to unintended destinations.

The server may reach internal services, cloud metadata, private APIs, or trusted networks that the external user cannot access directly.

### Q2. What features commonly introduce SSRF?

URL-based image upload, link previews, webhook testing, PDF generation, import-by-URL, document conversion, proxy functions, integrations, feed readers, and server-side API callbacks commonly fetch remote resources.

Any user-controlled destination should be reviewed.

### Q3. What is blind SSRF?

Blind SSRF occurs when the application makes the request but does not return the response to the attacker. It may be detected through controlled DNS or HTTP callbacks, timing, logs, or side effects.

Use approved OAST infrastructure for authorized testing.

### Q4. What is cloud metadata SSRF?

Cloud workloads may access metadata services that provide instance information or temporary credentials. SSRF can sometimes make the workload query metadata.

Impact depends on metadata protections and workload IAM permissions.

### Q5. How can SSRF affect AWS?

If SSRF reaches EC2 metadata and retrieves temporary role credentials, those credentials may access AWS APIs. AWS IMDSv2 makes simple attacks harder, while least-privilege IAM limits impact.

Application SSRF defenses and egress controls remain necessary.

### Q6. How can SSRF affect Azure?

Azure workloads use a metadata service for instance information and managed identity tokens. A server-side request weakness may expose tokens if the application can satisfy required headers and reach the endpoint.

Use managed identity least privilege, network controls, and application-level destination restrictions.

### Q7. How can SSRF affect GCP?

GCP metadata services provide workload identity information and tokens with required request headers. SSRF may expose cloud credentials depending on request control and environment.

Use least-privilege service accounts, metadata protections, and egress restrictions.

### Q8. Why is blocking `127.0.0.1` not enough?

Attackers may use IPv6, alternate IP representations, internal DNS names, redirects, private ranges, or DNS behavior to reach internal destinations.

Use strict destination allowlists and validate resolved addresses at each request.

### Q9. What is DNS rebinding in SSRF context?

DNS rebinding changes a hostname's resolved address over time, potentially passing validation with a public IP and later resolving to a private IP.

Defenses should validate resolved IPs safely and prevent changes between validation and connection.

### Q10. What is redirect-based SSRF bypass?

An allowed URL may redirect the server to a private or otherwise forbidden destination. If validation only checks the initial URL, the protection fails.

Disable redirects or validate every redirect destination.

### Q11. What protocols matter in SSRF?

Applications should normally allow only required schemes, usually HTTPS and perhaps HTTP. General URL libraries may support file, FTP, or other schemes that create additional risk.

Use an explicit scheme allowlist.

### Q12. How do you test SSRF safely?

Use a controlled callback URL and an authorized test environment. Confirm whether the server makes a request, then verify whether intended controls reject private IPs, localhost, metadata, redirects, and unapproved domains.

Do not target unrelated systems.

### Q13. How do you prevent SSRF?

Use strict domain or destination allowlists, allow only required schemes and ports, resolve and block private/link-local addresses, validate redirects, restrict outbound network access, and use least-privilege workload identities.

Avoid building general-purpose server-side proxies.

### Q14. What is egress filtering?

Egress filtering restricts outbound connections from servers or workloads. It limits SSRF, malware callbacks, and data exfiltration.

Use firewalls, proxies, service mesh policy, Kubernetes NetworkPolicy, or cloud network controls.

### Q15. How do you rate SSRF?

Consider reachable destinations, whether responses are returned, metadata access, credential exposure, internal services, allowed methods/headers, authentication required, and workload privileges.

SSRF reaching privileged metadata or internal admin services can be critical.

## File Upload

### Q16. Why is file upload dangerous?

File upload accepts attacker-controlled content and may store, process, render, scan, or serve it. Risks include malware, web shells, stored XSS, parser exploits, oversized files, path traversal, and public data exposure.

Secure upload requires multiple controls.

### Q17. Is file-extension validation enough?

No. Attackers can rename files, use multiple extensions, mixed case, or files whose content does not match the extension.

Use extension allowlists, server-side content validation, safe storage, and non-executable locations.

### Q18. What is MIME type validation?

MIME type describes content type, but client-provided `Content-Type` can be changed. Server-side detection can inspect file signatures or parse expected formats.

MIME validation is one layer, not a complete control.

### Q19. What are magic bytes?

Magic bytes are identifying byte sequences at the start of many file formats. They help determine actual content type.

Attackers can create polyglot files, so magic-byte checks should be combined with safe parsing and allowlists.

### Q20. What is a polyglot file?

A polyglot file is valid in more than one format, such as an image that also contains interpretable script content.

Re-encoding files and serving them with safe content types can reduce risk.

### Q21. What is a web shell?

A web shell is an uploaded server-executable script that provides remote control through HTTP requests.

Prevent execution in upload directories, use non-executable object storage, rename files, and allowlist formats.

### Q22. What is stored XSS through file upload?

Uploaded HTML, SVG, or other active content may execute script when viewed under the application's origin.

Serve untrusted files from a separate origin, set safe content types and download headers, sanitize or disallow active formats.

### Q23. What is image-processing risk?

Image libraries may have vulnerabilities, metadata issues, or resource-exhaustion weaknesses. Malicious files may exploit server-side processing.

Patch libraries, limit resources, re-encode images, and isolate processing.

### Q24. What is archive upload risk?

Archives can contain path traversal entries, huge decompression ratios, nested archives, symlinks, or malware.

Validate extraction paths, set size/count limits, scan content, and process in an isolated environment.

### Q25. What is a zip bomb?

A zip bomb is a highly compressed archive that expands to consume large storage, memory, or CPU resources.

Limit compressed and expanded size, nesting depth, file count, and processing time.

### Q26. Why rename uploaded files?

Server-generated random names prevent dangerous filenames, collisions, predictable object references, path manipulation, and special-character issues.

Store the sanitized original name only as metadata if needed.

### Q27. Where should uploads be stored?

Prefer non-executable object storage or directories outside web root. Use private access by default and controlled download endpoints or scoped signed URLs.

Separate untrusted content from application code.

### Q28. What is malware scanning for uploads?

Malware scanning uses antivirus or file-analysis services to identify known malicious content. It is useful but cannot detect everything.

Combine it with strict formats, isolation, access control, and safe processing.

### Q29. How do you test upload security safely?

Use harmless test files with mismatched extensions, oversized-but-safe samples within agreed limits, active-content test files, and authorized antivirus test signatures where permitted.

Do not upload real malware or persistent server-side code.

### Q30. How do you secure cloud file uploads?

Use private S3/Blob/GCS storage, short-lived signed URLs, object-level authorization, content validation, malware scanning, encryption, logging, and lifecycle policies.

Do not make the entire bucket public for convenience.

## XXE And XML

### Q31. What is XXE?

XXE means XML External Entity injection. It occurs when an XML parser processes external entities from untrusted XML.

Impact may include local file disclosure, SSRF, internal network access, or denial of service.

### Q32. What causes XXE?

XXE is caused by insecure XML parser configuration that allows DTDs and external entity resolution.

Disable external entities and DTD processing unless explicitly required.

### Q33. What is blind XXE?

Blind XXE does not return entity data directly but may trigger out-of-band DNS/HTTP requests or timing behavior.

Use approved controlled callbacks for authorized validation.

### Q34. XXE vs XML injection?

XXE abuses XML external entity processing. XML injection changes XML structure or data through unsafe string construction.

Both require secure XML libraries and configuration.

### Q35. What features may parse XML?

SOAP APIs, SAML, XML uploads, SVG processing, office documents, RSS feeds, configuration imports, and document conversion may parse XML.

XML may exist indirectly inside archive-based document formats.

### Q36. How do you test XXE safely?

Use a controlled XML document that attempts a harmless external callback or controlled local test resource in an authorized environment. Avoid reading sensitive files.

Source review of parser configuration is often safer and clearer.

### Q37. How do you prevent XXE?

Disable DTDs and external entities, use secure parser defaults, keep libraries updated, validate schemas, and restrict outbound network access.

Prefer simpler formats like JSON when XML features are unnecessary.

### Q38. What is XML bomb or Billion Laughs?

It is an entity-expansion denial-of-service attack that causes a parser to consume excessive resources.

Disable DTD/entity expansion and apply resource limits.

### Q39. Can WAF prevent XXE?

A WAF may detect known XML patterns, but encodings and parser behavior vary. Secure parser configuration is the real fix.

Use WAF only as additional protection.

### Q40. How can XXE become cloud compromise?

XXE can act like SSRF and access internal metadata or services. If workload IAM is broad, exposed tokens can lead to cloud API access.

Use parser hardening, egress restrictions, and least privilege.

## Insecure Deserialization

### Q41. What is serialization?

Serialization converts an object or data structure into a format for storage or transmission. Deserialization reconstructs it.

Formats include JSON, XML, language-native object formats, and binary protocols.

### Q42. What is insecure deserialization?

Insecure deserialization occurs when untrusted serialized data is converted into objects with unsafe behavior. It may trigger code execution, object manipulation, privilege changes, or denial of service.

Language-native object serialization is particularly risky with untrusted input.

### Q43. What is a gadget chain?

A gadget chain is a sequence of existing classes or methods that performs dangerous actions during deserialization.

Attackers abuse available application/library code rather than uploading new code.

### Q44. What application features use serialization?

Session state, message queues, caches, RPC, cookies, signed objects, job processing, and internal service communication may use serialization.

Review boundaries where serialized data can be influenced by untrusted users.

### Q45. Does signing serialized data solve the problem?

Signing prevents tampering if keys are protected and verification is correct, but it does not make unsafe deserialization inherently safe. Trusted producers could still create dangerous objects, and key compromise breaks the control.

Prefer safe data formats and explicit schemas.

### Q46. Why is JSON generally safer?

JSON represents data rather than arbitrary language objects. It reduces gadget-chain risk when parsed into simple structures with strict schemas.

Unsafe polymorphic type handling can still introduce risk.

### Q47. How do you prevent insecure deserialization?

Avoid native object deserialization from untrusted sources, use safe formats with strict schemas, allowlist types, sign data when integrity is required, patch libraries, and isolate processing.

Do not deserialize arbitrary classes.

### Q48. How do you test deserialization safely?

Identify serialized formats and input sources, review code/library settings, and use harmless modifications to determine whether client data controls object properties or types.

Avoid executing destructive gadget chains for basic validation.

### Q49. What is type confusion in deserialization?

Type confusion occurs when input selects unexpected object types or polymorphic classes. This can bypass validation or instantiate dangerous classes.

Disable unsafe type metadata and allowlist expected types.

### Q50. How do you rate insecure deserialization?

Severity depends on whether untrusted users control serialized data, available gadget chains, integrity protection, affected privileges, and whether code execution or authorization bypass is possible.

Confirmed remote code execution is critical.

## Open Redirect And URL Handling

### Q51. What is open redirect?

Open redirect occurs when an application redirects users to an attacker-controlled destination based on unvalidated input.

It can support phishing, OAuth token theft, or trust abuse.

### Q52. Where do open redirects occur?

Login return URLs, logout redirects, marketing links, OAuth callbacks, invitation flows, and navigation parameters commonly use redirect destinations.

Review parameters like return, next, redirect, callback, and continue.

### Q53. How do you prevent open redirect?

Use relative paths, server-side destination mappings, or strict allowlists. Normalize and validate URLs and avoid user-controlled full destinations.

Do not rely only on checking whether a trusted domain name appears in the string.

### Q54. How do you rate open redirect?

Open redirect alone is often low or medium, but severity increases when chained with OAuth, password reset, trusted domains, or authentication flows.

Always evaluate chaining potential.

### Q55. How can open redirect help SSRF?

An SSRF filter may allow a trusted domain that redirects to an internal destination. If redirects are followed without validation, the filter can be bypassed.

Validate every redirect hop.

## Race Conditions And Business Logic

### Q56. What is a business logic vulnerability?

A business logic vulnerability allows misuse of legitimate functionality in a way that violates business rules. Examples include applying a coupon repeatedly, bypassing payment, altering workflow order, or refunding more than paid.

Scanners often miss these because they require understanding intended behavior.

### Q57. What is a race condition?

A race condition occurs when multiple concurrent operations interact with shared state incorrectly. Attackers may perform an action multiple times before the system updates state.

Examples include duplicate withdrawals, repeated coupon redemption, inventory overselling, or multiple password reset uses.

### Q58. How do you test race conditions safely?

Use test accounts and reversible test data. Send a small number of concurrent requests and observe whether a one-time action executes more than once.

Avoid financial or production-impacting operations.

### Q59. How do you prevent race conditions?

Use database transactions, atomic operations, unique constraints, idempotency keys, locking, and server-side state validation.

Client-side button disabling is not sufficient.

### Q60. What is idempotency?

An idempotent operation produces the same result when repeated. Payment and order APIs often use idempotency keys to avoid duplicate processing.

The server must store and validate keys safely.

### Q61. What is workflow bypass?

Workflow bypass occurs when a user skips required steps or changes their order, such as accessing confirmation before payment or approval before verification.

The server must enforce valid state transitions.

### Q62. What is parameter tampering?

Parameter tampering changes values such as price, quantity, discount, user ID, plan, role, or shipping cost.

The server should calculate trusted values and validate business rules.

### Q63. What is negative quantity or numeric edge-case abuse?

Unexpected negative, zero, very large, decimal, or overflow values may break business rules or calculations.

Use strict types, ranges, and server-side validation.

### Q64. What is coupon abuse?

Coupon abuse includes reuse, stacking, changing ownership, bypassing expiry, or applying a coupon to excluded products.

Enforce limits server-side with atomic redemption records.

### Q65. What is refund abuse?

Refund abuse may involve repeated refunds, refunding more than paid, refunding after chargeback, or accessing another user's transaction.

Use authorization, state machines, reconciliation, and audit logs.

### Q66. What is unrestricted sensitive business flow?

An application may expose valuable flows such as ticket purchasing, reservation, signup, posting, or messaging without anti-automation controls.

Rate limiting, quotas, bot detection, identity checks, and fraud monitoring may be needed.

### Q67. What is rate-limit bypass?

Attackers may distribute requests across IPs, accounts, tokens, headers, or endpoints to avoid simple limits.

Rate limits should combine identity, IP, device, behavior, and business context.

### Q68. What is OTP abuse?

Attackers may repeatedly request OTPs to spam users, incur costs, enumerate accounts, or brute-force codes.

Use request limits, attempt limits, short expiry, binding to session/action, and monitoring.

### Q69. What is account registration abuse?

Automated registration can create fake accounts for spam, fraud, promotion abuse, or resource consumption.

Use verification, rate limiting, risk signals, and post-registration controls.

### Q70. How do you find business logic flaws?

Understand business goals, roles, state transitions, limits, trust assumptions, and financial or privilege decisions. Test actions out of order, repeated, concurrently, with altered values, and across users.

Threat modeling and abuse cases are especially valuable.

## Advanced Server-Side Scenarios

### Q71. SSRF returns no response but DNS callback occurs. Is it valid?

Yes, it demonstrates blind SSRF. Severity depends on reachable destinations, request control, headers/methods, and workload privileges.

Document controlled callback evidence and avoid probing unrelated internal systems.

### Q72. Upload accepts SVG. Is that automatically vulnerable?

No. Risk depends on how SVG is served and rendered. If served inline under the trusted origin, active content may create XSS. If forced as download from a separate origin, risk is lower.

Validate content handling.

### Q73. XML parser is internal only. Does XXE matter?

It may still matter if external users can influence XML indirectly through uploads, messages, documents, or upstream integrations. Internal trust should be verified, not assumed.

Assess data flow and parser configuration.

### Q74. Serialized cookie is signed. Can it be trusted?

A strong signature prevents unauthorized modification if the key remains secret and verification is correct. However, unsafe deserialization and key compromise remain concerns.

Prefer simple session identifiers or safe structured tokens.

### Q75. Open redirect is on login page. Why can that matter?

Users trust the legitimate login domain, and the redirect can send them to phishing content after authentication. It may also interact with OAuth flows.

Validate redirect destinations strictly.

### Q76. Two simultaneous coupon requests both succeed. What is the root issue?

The check and update are not atomic. Both requests see the coupon as unused before either marks it used.

Use transactions, locking, or unique constraints.

### Q77. A rate limit is per IP only. What is the weakness?

Attackers can use distributed IPs, cloud proxies, or botnets. Add account, token, device, and behavior-based controls.

Rate limits should protect the business action, not only the network address.

### Q78. SSRF accesses an internal admin API but not cloud metadata. Is it serious?

Yes. Internal services may trust network location and expose administrative actions or sensitive data. Metadata access is only one SSRF impact.

Fix destination validation and internal service authentication.

### Q79. File upload malware scanner passes a dangerous file. What does that show?

Antivirus is not a complete file-security control. Unknown malware, parser exploits, or active content may bypass signatures.

Use allowlists, safe storage, re-encoding, isolation, and access control.

### Q80. How do you summarize server-side and logic testing?

Identify where the server fetches, parses, executes, stores, or trusts user-controlled data. Test business rules, state transitions, concurrency, limits, and integrations using safe authorized scenarios.

Fix root causes through strict schemas, safe libraries, least privilege, network controls, atomic operations, and abuse-resistant design.

