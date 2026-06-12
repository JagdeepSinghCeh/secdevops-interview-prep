# Microservices And Serverless Security Q&A

This file covers service-to-service identity, zero trust, gateways, service meshes, distributed authorization, serverless functions, events, observability, and cloud-native scenarios.

## Microservices Fundamentals

### Q1. What is microservices architecture?

Microservices split an application into independently deployed services aligned to business capabilities.

Security becomes distributed across many identities, APIs, deployments, data stores, and network paths.

### Q2. What security benefits can microservices provide?

Services can have narrow permissions, isolated data, independent patching, and smaller failure domains.

These benefits appear only when boundaries and identities are designed correctly.

### Q3. What security challenges do microservices create?

More APIs, secrets, certificates, dependencies, logs, network paths, authorization decisions, and deployments increase complexity.

Consistency and observability become major challenges.

### Q4. What is service-to-service authentication?

It verifies the identity of one service calling another. Methods include mTLS, workload identity, signed tokens, and cloud-native identities.

Shared static passwords are difficult to rotate and attribute.

### Q5. What is service authorization?

After authenticating the caller, the receiving service checks whether that workload may perform the requested action on the resource.

Network location alone should not grant broad trust.

### Q6. What is zero trust for microservices?

Every request is authenticated, authorized, and protected regardless of network location. Access is least privilege and continuously observable.

Zero trust is a design approach, not one product.

### Q7. What is an API gateway in microservices?

It provides an external entry point for routing, authentication, rate limiting, and policy.

Internal services still need identity and authorization because gateways can be bypassed or misconfigured.

### Q8. What is backend-for-frontend?

A BFF is a backend tailored to a specific client type, such as web or mobile.

It can reduce client complexity but must not become an over-privileged proxy.

### Q9. What is service discovery?

Service discovery maps logical service names to current instances. Kubernetes Services and service registries provide it.

Secure discovery and DNS because traffic may be redirected to malicious destinations if compromised.

### Q10. What is a trust boundary in microservices?

Every service, tenant, identity domain, network zone, and data store can form a trust boundary.

Document data and identity crossing each boundary.

## Service Mesh And mTLS

### Q11. What is a service mesh?

A service mesh provides traffic management, service identity, mTLS, policy, and observability through proxies or node components.

Examples include Istio, Linkerd, and Consul service mesh.

### Q12. What does mTLS provide?

Mutual TLS encrypts traffic and authenticates both participating services.

It does not decide whether a caller is authorized for a business operation.

### Q13. What is workload identity?

Workload identity assigns a verifiable identity to a service, pod, VM, or function without long-lived static credentials.

Cloud and mesh systems can issue short-lived credentials.

### Q14. What is certificate rotation in a mesh?

The mesh automatically renews short-lived workload certificates and updates proxies.

Monitor failures because expired certificates can cause outages.

### Q15. What is sidecar proxy risk?

Sidecars handle sensitive traffic and identity material. Vulnerabilities or misconfiguration may expose data or bypass policy.

Patch proxies and restrict administrative interfaces.

### Q16. What is mesh authorization policy?

It allows or denies service communication based on workload identity, namespace, method, path, or other attributes.

Use deny by default and test policies.

### Q17. Can a service mesh replace NetworkPolicy?

No. Mesh policy controls participating application traffic, while NetworkPolicy controls network reachability at a lower layer.

Use both where risk justifies layered controls.

### Q18. What is east-west traffic?

East-west traffic flows between internal services or workloads. It may contain sensitive data and should not be assumed trusted.

Use identity, encryption, segmentation, and monitoring.

### Q19. What is north-south traffic?

North-south traffic enters or leaves the application environment, commonly through gateways and load balancers.

Apply edge controls and protect origins.

### Q20. What is mesh telemetry risk?

Telemetry can contain URLs, identities, headers, and internal topology. High volume also creates cost and privacy issues.

Redact sensitive fields and control access.

## Distributed Authorization And Data

### Q21. Centralized vs local authorization?

Centralized policy improves consistency, while local enforcement understands service-specific objects and business context.

A common pattern centralizes policy decisions but enforces them in each service.

### Q22. What is token propagation risk?

Forwarding the original user token through many services exposes it broadly and may grant excessive downstream access.

Use token exchange, audience restriction, or delegated service identities.

### Q23. What is confused deputy problem?

A privileged service is tricked into using its authority on behalf of an unauthorized caller.

Bind actions to the original identity, resource, audience, and authorization context.

### Q24. What is tenant context propagation?

Services need a trustworthy tenant identity for data isolation. A client-supplied tenant header alone is unsafe.

Derive tenant context from verified identity and protect it across calls.

### Q25. What is database-per-service?

Each service owns its data store rather than sharing one database schema.

It strengthens boundaries but complicates transactions, reporting, and governance.

### Q26. What is shared-database risk?

Services may bypass APIs, access unrelated tables, or become tightly coupled. One compromised credential can expose broad data.

Use separate schemas/accounts and least privilege when a shared platform is necessary.

### Q27. What is eventual consistency security risk?

Authorization or revocation changes may take time to propagate, temporarily allowing stale access or duplicate actions.

Design expiry, invalidation, and compensating checks.

### Q28. What is distributed transaction risk?

Partial completion can create inconsistent financial or authorization state.

Use idempotency, sagas, compensating actions, and auditable state transitions.

### Q29. What is a saga?

A saga coordinates a sequence of local transactions with compensating actions when later steps fail.

Compensations require the same authorization and integrity controls as primary actions.

### Q30. What is message authorization?

Consumers verify that the producer is allowed to publish the event and that the event is valid for the target tenant or resource.

Internal queues should not be trusted blindly.

## Messaging And Event Security

### Q31. What security controls apply to message queues?

Use producer/consumer identity, topic permissions, TLS, encryption, schema validation, retention, dead-letter handling, audit logs, and quotas.

Separate environments and tenants.

### Q32. What is a poison message?

A poison message repeatedly fails processing and may block or exhaust consumers.

Use retry limits, dead-letter queues, validation, and monitoring.

### Q33. What is a dead-letter queue?

It stores messages that cannot be processed after configured retries.

Protect it because messages may contain sensitive data and failures reveal application internals.

### Q34. What is event replay?

Reprocessing old valid events may duplicate transactions or restore revoked state.

Use event IDs, timestamps, sequence checks, and idempotent consumers.

### Q35. What is schema registry security?

A schema registry manages message formats and compatibility. Unauthorized schema changes can break consumers or bypass validation.

Protect publishing access and review changes.

### Q36. What is event injection?

An attacker publishes forged or manipulated events into a trusted bus.

Authenticate producers, authorize topics, validate schema and business context, and sign high-risk events where appropriate.

### Q37. What is excessive retry risk?

Unlimited retries amplify cost, load, and downstream side effects.

Use exponential backoff, retry caps, idempotency, and dead-letter queues.

### Q38. What is queue data-retention risk?

Long retention may preserve sensitive payloads beyond need. Short retention may harm investigation or recovery.

Apply classification-based retention and encryption.

### Q39. How do you secure Kafka?

Use TLS, client authentication, ACLs, topic isolation, broker hardening, schema controls, quotas, audit logs, and protected ZooKeeper/KRaft management.

Avoid public broker exposure.

### Q40. How do you secure cloud event buses?

Use least-privilege publish/subscribe IAM, event-source policies, encryption, logging, schema validation, and account/project boundaries.

Review cross-account subscriptions carefully.

## Serverless Fundamentals

### Q41. What is serverless?

Serverless lets developers run functions or managed services without managing underlying servers directly.

The provider manages more infrastructure, while customers still secure code, identity, events, data, dependencies, and configuration.

### Q42. What is FaaS?

Function as a Service executes functions in response to events. Examples include AWS Lambda, Azure Functions, and Google Cloud Functions.

Functions should be small, least privileged, observable, and resource limited.

### Q43. What is a serverless trigger?

A trigger invokes a function, such as an HTTP request, queue message, object upload, schedule, or database event.

Validate the trigger identity, payload, and replay behavior.

### Q44. What is serverless attack surface?

It includes event sources, function URLs, dependencies, IAM roles, environment variables, secrets, storage, deployment packages, and connected services.

Functions can become powerful cloud deputies.

### Q45. Is serverless automatically secure?

No. It reduces operating-system management but can still suffer from injection, authorization flaws, vulnerable dependencies, over-privileged IAM, leaked secrets, and unsafe events.

Shared responsibility still applies.

### Q46. What is function execution role?

It is the workload identity used by a function to access cloud services.

Grant permissions per function or narrow capability, not one shared administrator role.

### Q47. What is function URL risk?

A function may be exposed directly to the internet with weak or missing authentication.

Review intended exposure, gateway controls, CORS, authorization, and rate limits.

### Q48. What is cold start?

A cold start initializes a new function environment. It affects performance and may load secrets, dependencies, and configuration.

Do not log sensitive initialization data.

### Q49. What is warm execution context risk?

Providers may reuse execution environments. Sensitive data left in global memory or temporary storage might persist between invocations.

Avoid retaining user-specific secrets and clean temporary data.

### Q50. What is serverless concurrency risk?

Rapid event volume can create many parallel executions, increasing downstream load and cost.

Set concurrency, quotas, queue buffering, and backpressure.

## Serverless Security Controls

### Q51. How do you secure Lambda?

Use least-privilege roles, validated event sources, secure dependencies, Secrets Manager, restricted networking, resource limits, logging, code signing where appropriate, and IaC scanning.

Avoid public function URLs unless required.

### Q52. How do you secure Azure Functions?

Use managed identity, Key Vault, secure function authorization, private endpoints or access restrictions, dependency scanning, validated triggers, logging, and least privilege.

Protect storage accounts used by the function runtime.

### Q53. How do you secure Google Cloud Functions?

Use dedicated least-privilege service accounts, authenticated invocation, Secret Manager, ingress restrictions, dependency scanning, event validation, and audit logs.

Avoid user-managed service-account keys.

### Q54. How should serverless secrets be handled?

Retrieve secrets through workload identity from a secrets manager. Cache only for an approved lifetime and avoid logging or embedding them.

Plan rotation and function refresh.

### Q55. What is serverless dependency risk?

Deployment packages may contain vulnerable or malicious libraries. Functions are sometimes forgotten after deployment and remain unpatched.

Use SCA, inventory, SBOMs, and automated rebuilds.

### Q56. What is serverless event-data injection?

Events from queues, storage, or databases may contain attacker-controlled fields that reach SQL, templates, logs, commands, or outbound requests.

Treat all event data as untrusted.

### Q57. What is serverless SSRF?

A function may fetch URLs and access metadata, private networks, or cloud APIs using its identity.

Apply URL policy, egress controls, and least-privilege roles.

### Q58. What is function timeout security?

Timeouts limit runaway code, expensive requests, and some denial-of-service impact.

Also limit memory, payload size, retries, and downstream calls.

### Q59. What is reserved concurrency?

It caps the number of simultaneous function executions, protecting downstream services and cost.

Set values based on business capacity.

### Q60. What is serverless logging risk?

Events and environment data are easily logged wholesale, exposing tokens and personal data.

Use structured allowlisted logs and redaction.

## Microservices DevSecOps

### Q61. How do you secure microservices in CI/CD?

Apply consistent templates for authentication, authorization, logging, health, TLS, SAST, SCA, image scanning, SBOM, signing, IaC, and deployment policy.

Platform engineering can make secure defaults easy.

### Q62. What is a golden service template?

It is an approved starter repository with secure framework configuration, pipeline, container, observability, and deployment defaults.

Keep it updated and provide migration guidance.

### Q63. What is centralized dependency policy?

The organization defines approved registries, versions, licenses, and update workflows while allowing service teams to own remediation.

Avoid blocking all innovation with unmanaged exceptions.

### Q64. How do you scan serverless IaC?

Scan Terraform, CloudFormation, SAM, Serverless Framework, Bicep, or deployment manifests for public triggers, broad roles, unencrypted storage, and missing logs.

Review generated cloud resources too.

### Q65. How do you test service authorization?

Use identities for multiple services, roles, and tenants. Attempt direct calls, replay tokens with wrong audiences, and test protected operations and objects.

Do this in authorized environments.

### Q66. What is consumer-driven contract testing?

Consumers define expectations that providers verify, reducing breaking API changes.

Security requirements such as field minimization and authentication can be incorporated.

### Q67. What is chaos engineering security relevance?

Controlled failures reveal insecure fallback behavior, stale authorization, secret-manager outages, retry storms, and logging gaps.

Experiments require strict safety controls.

### Q68. What is secure degradation?

When dependencies fail, the system preserves critical security invariants. For example, authorization failure denies access rather than allowing all users.

Availability fallbacks should not bypass security.

### Q69. What is platform engineering's security role?

Platform teams provide secure reusable pipelines, identities, templates, observability, policy, and deployment paths.

This reduces duplicated insecure implementation.

### Q70. What metrics help microservices security?

Track service inventory, identity ownership, mTLS/policy coverage, vulnerability age, public endpoints, secret rotation, signed-image coverage, and denied access anomalies.

Measure outcomes, not only tool installation.

## Scenarios

### Q71. One service trusts an `X-User-Role` header. What is wrong?

If external or untrusted callers can set it, they can impersonate roles. Identity context must be created and protected by trusted authentication infrastructure.

Strip untrusted headers at the edge and verify signed claims.

### Q72. All services share one cloud role. What is the risk?

Compromise of any service grants the combined permissions of all services and prevents clear attribution.

Use dedicated workload identities and least privilege.

### Q73. A function processes public file uploads. What do you check?

File type and size, malware/parser isolation, trigger permissions, storage access, function role, timeouts, retries, logging, destination paths, and output access.

Treat metadata and filenames as untrusted.

### Q74. A queue message is retried 1,000 times. What can happen?

Cost and load increase, downstream actions may duplicate, and the queue may remain blocked.

Set retry limits, idempotency, backoff, and dead-letter handling.

### Q75. mTLS is enabled, but service A can call every method on service B. Is that secure?

mTLS proves identity and encrypts traffic but does not provide fine-grained authorization automatically.

Add method and resource policies.

### Q76. A serverless function has administrator permission. What do you do?

Identify exact resource actions, replace the role with least privilege, review past audit activity, and add IaC policy to prevent broad roles.

Function compromise otherwise becomes cloud compromise.

### Q77. An internal service has no authentication because it is private. What is the risk?

SSRF, compromised workloads, routing mistakes, or insider access can reach it. Private networking is not sufficient identity.

Add service authentication and authorization.

### Q78. A service mesh certificate expires and traffic fails. What lesson follows?

Security automation needs health monitoring, renewal testing, safe rollout, and emergency recovery.

Certificate lifecycle is an availability concern too.

### Q79. A function logs every incoming event. What do you do?

Identify secrets and personal data, stop broad logging, redact or allowlist fields, restrict log access, and address historical exposure.

Events are untrusted and often sensitive.

### Q80. How do you summarize microservices and serverless security?

Give every workload a strong least-privilege identity, authenticate and authorize every boundary, validate messages and events, protect secrets and data, limit resources, standardize secure delivery, and maintain end-to-end observability.

Managed platforms reduce infrastructure work but do not remove application and identity risk.

