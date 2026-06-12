# Advanced API Security Q&A

This file expands REST, GraphQL, gRPC, webhook, authentication, authorization, inventory, testing, gateway, and OWASP API Security Top 10 interview preparation. Testing guidance assumes explicit authorization and test data.

## API Foundations

### Q1. What is an API?

An Application Programming Interface defines how software components communicate. Web APIs commonly receive requests over HTTP and return JSON, XML, binary data, or streaming responses.

API security protects identities, objects, functions, data, business flows, integrations, and infrastructure behind those interfaces.

### Q2. What is a REST API?

REST is an architectural style that models resources through URLs and standard HTTP methods. For example, `GET /orders/10` retrieves an order and `PATCH /orders/10` updates it.

REST does not provide security automatically; every endpoint still needs authentication, authorization, validation, rate limiting, and logging.

### Q3. What is an API endpoint?

An endpoint is a specific API route and method used to perform an operation. `GET /users` and `POST /users` are different endpoints even though they share a path.

Security inventories should track method, path, version, owner, authentication, exposure, and data classification.

### Q4. What is an API resource?

A resource is the business object represented by an API, such as a user, account, order, invoice, file, or deployment.

Object-level authorization must protect every resource instance.

### Q5. What is an API schema?

An API schema defines expected paths, methods, parameters, request bodies, responses, and data types. OpenAPI is commonly used for REST APIs.

Schemas support documentation, validation, testing, code generation, and security inventory.

### Q6. What is OpenAPI?

OpenAPI is a machine-readable specification for HTTP APIs. It can describe endpoints, authentication schemes, parameters, models, and responses.

Security teams use it to generate tests, discover undocumented differences, and configure DAST tools.

### Q7. What is API-first development?

API-first development designs and reviews the API contract before implementation. It helps teams agree on data models, security requirements, errors, and versioning.

Threat modeling and authorization design should be included during the contract stage.

### Q8. What is an API gateway?

An API gateway is a managed entry point that routes, authenticates, limits, observes, and sometimes transforms API traffic. Examples include Amazon API Gateway, Azure API Management, Apigee, Kong, and NGINX.

Gateways provide centralized controls but do not replace authorization and validation inside services.

### Q9. What is an API management platform?

API management combines gateway functions with developer portals, lifecycle management, analytics, policies, subscriptions, and governance.

It helps organizations inventory and consistently secure large API estates.

### Q10. What is an API contract test?

A contract test verifies that an implementation follows the agreed API schema and behavior. It can detect unexpected fields, response changes, or incompatible versions.

Contract tests complement security tests but do not prove authorization or business-logic safety.

## OWASP API Security Top 10

### Q11. What is OWASP API Security Top 10?

It is an OWASP awareness document describing major API security risk categories. The current published edition is 2023.

It is a starting point rather than a complete verification standard.

### Q12. What is API1: Broken Object Level Authorization?

BOLA occurs when an API does not verify whether the caller may access a particular object. Changing an order, account, file, or user identifier may expose another user's resource.

Test with multiple authorized accounts and enforce ownership or relationship checks server-side.

### Q13. What is API2: Broken Authentication?

Broken Authentication covers weaknesses that allow attackers to compromise authentication tokens or assume identities. Examples include weak token validation, credential stuffing, insecure recovery, and missing rate limits.

Protect the complete authentication lifecycle.

### Q14. What is API3: Broken Object Property Level Authorization?

This category covers unauthorized reading or modification of individual object fields. It combines concerns historically described as excessive data exposure and mass assignment.

Define explicit request and response schemas for each role.

### Q15. What is API4: Unrestricted Resource Consumption?

This risk occurs when API requests consume excessive CPU, memory, storage, network, third-party cost, or other limited resources.

Use quotas, rate limits, size limits, pagination, timeouts, cost controls, and asynchronous processing.

### Q16. What is API5: Broken Function Level Authorization?

This occurs when users invoke functions outside their role, such as normal users calling administrative endpoints.

Authorization must be enforced on every method and operation, not only hidden in the UI.

### Q17. What is API6: Unrestricted Access to Sensitive Business Flows?

Attackers automate valuable legitimate workflows such as purchasing, reservations, posting, referrals, or account creation at abusive scale.

Defenses include business-aware limits, bot detection, identity checks, fraud controls, and workflow monitoring.

### Q18. What is API7: Server-Side Request Forgery?

API features that fetch URLs can be abused to make server-side requests to internal services, metadata endpoints, or attacker-controlled destinations.

Use destination allowlists, egress control, redirect validation, and least-privilege workload identities.

### Q19. What is API8: Security Misconfiguration?

It includes unsafe defaults, verbose errors, unnecessary methods, missing TLS, broad CORS, public management endpoints, or inconsistent security across services.

Automated baselines, gateway policy, IaC scanning, and continuous posture monitoring reduce this risk.

### Q20. What is API9: Improper Inventory Management?

Organizations may lose track of hosts, versions, environments, endpoints, data flows, or deprecated APIs. Forgotten APIs often miss patches and controls.

Maintain a continuously updated inventory and retirement process.

### Q21. What is API10: Unsafe Consumption of APIs?

Applications may trust third-party API data too much and apply weaker validation than they use for user input. A compromised dependency or integration can then attack the consumer.

Validate responses, enforce timeouts and schemas, authenticate providers, and isolate integrations.

## API Authentication And Authorization

### Q22. API key vs access token?

An API key commonly identifies a calling application or subscription. An access token usually represents authorized access for a user or workload and carries scope or claims.

Neither should be treated as public, and both require rotation, restriction, and monitoring.

### Q23. Where should an API key be sent?

Prefer an authorization header or provider-defined secure header over query strings. Query parameters frequently appear in URLs, logs, browser history, and analytics.

Always use TLS.

### Q24. What is bearer-token risk?

Anyone possessing a bearer token can generally use it until expiry or revocation. Theft through logs, XSS, repositories, or insecure transport can therefore become account compromise.

Use short lifetimes, narrow scopes, secure storage, and token revocation.

### Q25. What is token scope?

Scope limits what a token may access, such as read-only profile access or deployment to one environment.

APIs should verify required scope and still perform object and function authorization.

### Q26. What is machine-to-machine authentication?

It authenticates one service or workload to another without a human. Common approaches include OAuth client credentials, mTLS, cloud workload identity, and signed service tokens.

Avoid shared static secrets across many services.

### Q27. What is mTLS for APIs?

Mutual TLS requires both client and server to present trusted certificates. It provides strong transport authentication for services.

Certificate lifecycle, authorization, and application-level identity remain necessary.

### Q28. What is an authorization policy decision point?

A policy decision point evaluates whether access should be allowed based on identity, action, resource, and context. The application or gateway then enforces the decision.

Central policy engines can improve consistency, but service context must be accurate.

### Q29. RBAC vs ABAC for APIs?

RBAC grants permissions based on roles. ABAC uses attributes such as department, tenant, data sensitivity, resource owner, location, or time.

Complex APIs often combine both models.

### Q30. What is deny by default?

If no explicit authorization rule allows an operation, the API denies it. This is safer than granting access unless a denial rule matches.

New endpoints and methods should begin protected.

## API Input And Output Security

### Q31. What is schema validation?

Schema validation checks request structure, required fields, types, lengths, formats, and allowed values before business processing.

It reduces injection, type confusion, mass assignment, and unexpected-state bugs.

### Q32. Allowlist vs blocklist validation?

An allowlist defines permitted values or patterns. A blocklist attempts to reject known bad input.

Allowlists are generally stronger for constrained fields because bad input has countless forms.

### Q33. What is content-type validation?

The API verifies that the declared content type is supported and that the body actually follows the expected format.

Reject ambiguous or unsupported parsers to reduce request confusion and parser attacks.

### Q34. What is mass assignment in APIs?

The framework binds all client-supplied properties to a backend object, including sensitive fields such as role, price, approval, or tenant ID.

Use dedicated request models and field allowlists.

### Q35. What is excessive data exposure?

The API returns more fields than the caller needs and relies on the client to hide them. Hidden fields remain available to attackers inspecting the response.

Build role-specific server-side response models.

### Q36. What is data minimization?

Data minimization means processing and returning only the information required for the operation.

It reduces breach impact, privacy risk, and accidental exposure.

### Q37. What is API error-handling risk?

Verbose errors may reveal stack traces, SQL statements, internal hostnames, tokens, library versions, or sensitive records.

Return stable generic errors externally and preserve diagnostic detail in protected logs.

### Q38. What is API response caching risk?

Shared caches may store personalized or sensitive responses and serve them to other users if cache keys or headers are wrong.

Set appropriate cache controls and include authorization context in cache design.

### Q39. What is pagination security?

Pagination limits response size and resource consumption. APIs should cap page size and validate cursors or offsets.

Unlimited exports and pagination can enable scraping or denial of service.

### Q40. What is idempotency-key security?

Idempotency keys prevent duplicate processing of requests such as payments. Keys must be bound to the authenticated caller and request semantics.

Predictable or cross-user keys may create information disclosure or replay issues.

## REST And HTTP Testing

### Q41. How do you test a REST API?

Inventory endpoints, methods, authentication, roles, object IDs, schemas, and business flows. Then test negative authorization, input types, rate limits, error handling, data exposure, and state transitions.

Use Burp Suite, Postman, curl, and automated scanners as supporting tools.

### Q42. Why test every HTTP method?

Authorization or validation may differ between GET, POST, PUT, PATCH, DELETE, and custom actions. An endpoint may secure one method but expose another.

Method enumeration should remain within scope.

### Q43. What is HTTP method override?

Some frameworks allow a header or parameter to override the HTTP method. If intermediaries and applications interpret it differently, controls may be bypassed.

Disable unused override functionality and enforce authorization after final method resolution.

### Q44. What is API versioning risk?

Older API versions may remain accessible with weaker authentication, missing fixes, or outdated schemas.

Inventory, monitor, deprecate, and retire versions through a defined lifecycle.

### Q45. What is undocumented API risk?

Undocumented endpoints may receive less testing and monitoring but remain reachable. Mobile apps and JavaScript often reveal them.

Discovery should feed the official inventory and ownership process.

### Q46. What is shadow API?

A shadow API is deployed outside approved inventory or governance. It may be created by a team, test environment, or forgotten service.

Use gateway telemetry, DNS/cloud inventory, and external attack-surface monitoring to discover it.

### Q47. What is zombie API?

A zombie API is an obsolete or deprecated API that remains active. It may use vulnerable code or weak controls.

Retire it, remove routes and infrastructure, and monitor attempted use.

### Q48. What is an API replay attack?

An attacker captures a valid request and sends it again to repeat an action. Payments, transfers, password resets, and webhook events are common targets.

Use nonces, timestamps, idempotency, short-lived signatures, and state validation.

### Q49. What is API request signing?

The client signs request elements using a secret or private key so the server can verify integrity, identity, and sometimes freshness.

Canonicalization, key rotation, clock skew, and replay protection must be designed carefully.

### Q50. What is HMAC?

HMAC uses a shared secret and hash function to authenticate message integrity. It is common for webhook and API request signatures.

Compare signatures safely and protect the shared secret.

## GraphQL Security

### Q51. What is GraphQL?

GraphQL lets clients request selected fields through a typed schema, usually through a single HTTP endpoint.

Security must operate at resolver, object, and field level rather than relying only on endpoint-level checks.

### Q52. What is GraphQL introspection?

Introspection lets clients query the schema. It helps development and tooling but may reveal types and operations.

Disabling it can reduce information exposure but never replaces authorization.

### Q53. What is a GraphQL resolver?

A resolver fetches or changes data for a field or operation. Every resolver must enforce appropriate authorization and tenant context.

Parent-object access does not automatically authorize every nested field.

### Q54. What is GraphQL query-depth abuse?

Deeply nested queries may cause expensive recursive processing and database calls.

Set depth, complexity, timeout, and resource limits.

### Q55. What is GraphQL batching abuse?

Clients may submit many operations in one request, bypassing request-count rate limits or amplifying resource use.

Limit batch size and apply operation-aware quotas.

### Q56. What is GraphQL field-level authorization?

Some fields contain sensitive information even when the parent object is visible. The resolver must check whether the caller may access each protected field.

Response filtering alone after data retrieval may still leak through errors or side channels.

### Q57. What is GraphQL alias abuse?

Aliases can request the same operation many times under different names in a single query. This may amplify brute force or resource consumption.

Apply complexity and per-operation limits.

### Q58. How do you test GraphQL safely?

Review the schema, roles, queries, mutations, field permissions, depth, batching, aliases, errors, and object identifiers using test accounts.

Avoid high-cost queries against production.

## gRPC, Webhooks, And Event APIs

### Q59. What is gRPC?

gRPC is a remote procedure call framework commonly using Protocol Buffers and HTTP/2. It supports unary and streaming communication.

Security still requires TLS, service identity, method authorization, validation, and observability.

### Q60. What are Protocol Buffers?

Protocol Buffers define strongly typed message schemas and serialize data efficiently.

Strong types help validation, but applications must still enforce ranges, authorization, and business rules.

### Q61. What is gRPC reflection?

Reflection lets clients discover services and methods, similar to API introspection. It is useful for development but should be evaluated for production exposure.

Disabling discovery does not replace authorization.

### Q62. What is a webhook?

A webhook sends an HTTP callback when an event occurs, such as payment completion or repository activity.

Receivers must authenticate the sender, validate payloads, prevent replay, and process events idempotently.

### Q63. How do you secure incoming webhooks?

Verify HMAC or asymmetric signatures, validate timestamp and event ID, use TLS, enforce schema, allowlist providers where practical, and make processing idempotent.

Do not trust source IP alone because provider ranges change and proxies exist.

### Q64. How do you secure outgoing webhooks?

Validate configured destinations to prevent SSRF, restrict schemes and ports, protect signing secrets, apply egress controls, and log delivery.

Tenant users should not be able to reach internal network destinations.

### Q65. What is webhook replay?

An attacker resends a previously valid event to repeat an action. A valid signature alone may still verify.

Check timestamp windows and store processed event IDs.

### Q66. What is event-driven API security?

Message queues and event buses require producer/consumer authentication, topic authorization, schema validation, encryption, retention, replay handling, and poison-message controls.

Do not assume internal messages are trustworthy.

## API Gateways, Rate Limits, And Observability

### Q67. What security belongs at an API gateway?

TLS termination, authentication integration, coarse authorization, rate limiting, schema checks, WAF rules, request-size limits, routing, and centralized logging can belong at the gateway.

Object-level and business authorization must remain in the application.

### Q68. Why is rate limiting by IP alone weak?

Attackers can distribute traffic across many addresses, while many legitimate users may share one address.

Combine IP, account, token, tenant, device, endpoint, and business-action signals.

### Q69. What is quota?

A quota limits resource consumption over a longer period, such as requests per day, storage per tenant, or monthly API spend.

Quotas protect cost and service fairness.

### Q70. What is API observability?

API observability combines logs, metrics, traces, and context to understand requests and failures.

Security-relevant fields include identity, endpoint, method, status, latency, resource, tenant, and denied action without logging secrets.

### Q71. What should API security logs include?

Log authentication failures, authorization denials, sensitive actions, admin changes, rate-limit events, token anomalies, schema failures, and data exports.

Redact tokens, credentials, and sensitive body fields.

### Q72. What is distributed tracing security risk?

Trace data may contain headers, tokens, query values, personal data, and internal topology.

Apply redaction, access control, encryption, and retention policies.

## API DevSecOps

### Q73. How do you add API security to CI/CD?

Lint and review OpenAPI schemas, run SAST/SCA/secrets, execute authorization and contract tests, deploy to staging, run authenticated DAST and API tests, then enforce gateway and runtime monitoring.

Generate test accounts for distinct roles and tenants.

### Q74. What is API fuzzing?

API fuzzing submits unexpected but controlled values, types, sizes, sequences, or structures to find validation and reliability weaknesses.

Use schema-guided fuzzing in authorized non-production environments.

### Q75. What is negative API testing?

Negative tests verify that invalid, unauthorized, expired, oversized, or out-of-order requests are rejected safely.

Strong security test suites contain more than successful happy-path tests.

### Q76. How do you prevent authorization regression?

Add automated tests for every role, tenant, object relationship, protected field, and sensitive operation. Run them on pull requests and deployment pipelines.

Central authorization libraries also reduce inconsistent checks.

### Q77. How do you scan APIs with DAST?

Provide an OpenAPI, Postman, SOAP, or GraphQL definition where supported, configure authentication, use test data, and scan a staging environment.

Manually validate results and business logic.

### Q78. An API gateway authenticates users. Does the backend need checks?

Yes. Backends must validate trusted identity context and perform resource/function authorization. Direct access, routing mistakes, or compromised internal services can bypass gateway assumptions.

Use defense in depth and restrict backend network exposure.

### Q79. A mobile app contains an API key. Is it secret?

Anything distributed to client devices can be extracted. A mobile API key may identify the application but must not grant powerful confidential access.

Use user authentication, attestation as defense in depth, backend authorization, and key restrictions.

### Q80. How do you summarize API security?

Inventory every API, strongly authenticate callers, authorize every object/function/property, validate schemas, limit resources and business flows, protect integrations, manage versions, and monitor runtime behavior.

API security is primarily identity, data, and business-logic security enforced consistently throughout the lifecycle.

