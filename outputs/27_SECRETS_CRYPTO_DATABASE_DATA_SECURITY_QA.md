# Secrets, Cryptography, Database, And Data Security Q&A

This file covers secret lifecycle, KMS/HSM concepts, cryptographic design, database controls, data classification, privacy, backups, and interview scenarios.

## Secrets Management

### Q1. What is a secret?

A secret is sensitive authentication or cryptographic material such as a password, API token, private key, certificate key, database credential, or signing key.

Its value depends on confidentiality, scope, lifetime, and the permissions it grants.

### Q2. What is secrets management?

Secrets management controls creation, storage, access, distribution, rotation, revocation, auditing, backup, and destruction of secrets.

It replaces ad hoc storage in code, files, images, and shared messages.

### Q3. What is the secret lifecycle?

The lifecycle includes generation, secure storage, authorized distribution, use, rotation, expiration, revocation, recovery, and destruction.

Every stage needs ownership and auditability.

### Q4. Static vs dynamic secret?

A static secret remains valid until rotated or revoked. A dynamic secret is generated on demand with limited scope and lifetime.

Dynamic credentials reduce exposure and simplify revocation.

### Q5. What is secret rotation?

Rotation replaces an old secret with a new one and updates consumers safely. Good rotation supports overlap or coordinated rollout to avoid outages.

After verification, revoke the old secret.

### Q6. What is secret revocation?

Revocation immediately invalidates a secret before normal expiry, commonly after compromise or access removal.

Systems must support revocation and incident playbooks.

### Q7. What is secret sprawl?

Secret sprawl occurs when credentials are copied across repositories, CI variables, laptops, documents, images, and environments.

Centralize secrets and reduce long-lived copies.

### Q8. What is a secrets manager?

A secrets manager securely stores and controls secret access, often with encryption, audit logging, versions, and rotation.

Examples include HashiCorp Vault, AWS Secrets Manager, Azure Key Vault, and GCP Secret Manager.

### Q9. What is HashiCorp Vault?

Vault provides centralized secrets, dynamic credentials, leases, revocation, encryption services, and authentication integrations.

It requires secure initialization, unsealing, policy, availability, and audit configuration.

### Q10. How should applications access secrets?

Use workload identity to authenticate to a secrets manager and retrieve only required values at runtime. Prefer short-lived or dynamic credentials.

Avoid embedding a master secret that unlocks every other secret.

## Secrets In CI/CD And Runtime

### Q11. How should CI/CD use secrets?

Restrict secrets to required jobs, protected branches and environments, mask logs, use short lifetimes, and prefer OIDC federation.

Untrusted pull requests should not receive release secrets.

### Q12. What is pipeline-created secret risk?

A pipeline may generate passwords, keys, certificates, or tokens and accidentally print or store them in artifacts.

Send them directly to a secret manager and limit output.

### Q13. Environment variables vs mounted secret files?

Environment variables are convenient but may leak through process inspection or diagnostics. Mounted files can have controlled permissions and rotation behavior but may remain on disk.

Choose based on threat model and platform capabilities.

### Q14. What is sidecar secret injection?

A sidecar or agent authenticates to a secret manager and writes or renews credentials for the application.

Protect the shared volume and agent identity.

### Q15. What is CSI secret integration?

Container Storage Interface drivers can mount cloud or external secrets into Kubernetes pods.

RBAC, workload identity, rotation, and node security remain important.

### Q16. What is break-glass secret?

It is an emergency credential used when normal identity systems fail. It should be strongly protected, monitored, tested, and accessed only under documented procedures.

Every use should trigger review.

### Q17. What should secret audit logs capture?

Capture identity, secret identifier, action, time, source, result, and administrative changes without recording the secret value.

Alert on unusual access and bulk retrieval.

### Q18. How do you back up secrets?

Use encrypted, access-controlled, tested backups that preserve required metadata and recovery keys.

A backup that cannot be restored securely is not useful.

### Q19. How do you respond to a leaked secret?

Revoke or rotate it, review usage, contain affected access, remove copies, identify the root cause, and improve prevention.

Assume it was copied once exposed.

### Q20. What is secret-zero problem?

It is the challenge of how an application initially authenticates to retrieve other secrets.

Use platform workload identities, instance roles, managed identities, or trusted bootstrap mechanisms.

## Cryptography Fundamentals

### Q21. What is encryption?

Encryption transforms plaintext into ciphertext using a key so unauthorized parties cannot read it.

Encryption protects confidentiality, not automatically integrity or identity unless an authenticated mode is used.

### Q22. Symmetric vs asymmetric encryption?

Symmetric encryption uses the same secret key to encrypt and decrypt. Asymmetric cryptography uses a public/private key pair.

Symmetric methods are efficient for data; asymmetric methods support key exchange and signatures.

### Q23. What is hashing?

Hashing maps input to a fixed-length digest and is designed to be one-way and collision-resistant.

Uses include integrity, password storage with specialized algorithms, and signing workflows.

### Q24. What is a digital signature?

A private key signs data, and the public key verifies authenticity and integrity.

Signatures do not encrypt the data.

### Q25. What is authenticated encryption?

Authenticated encryption protects confidentiality and integrity together. AES-GCM and ChaCha20-Poly1305 are common examples.

Prefer well-reviewed authenticated modes over composing encryption and integrity manually.

### Q26. What is a nonce?

A nonce is a value used once with a cryptographic operation. Reusing nonces with some algorithms can expose keys or plaintext.

Follow the algorithm and library's nonce requirements.

### Q27. What is an initialization vector?

An IV initializes certain encryption modes. It may need uniqueness or unpredictability depending on the algorithm.

It is usually stored alongside ciphertext and is not necessarily secret.

### Q28. What is a salt?

A salt is a unique random value used with password hashing or key derivation to prevent precomputed attacks and identical outputs.

It is stored with the resulting hash.

### Q29. What is key derivation function?

A KDF derives cryptographic keys from passwords or other key material using salt and computational work.

Examples include Argon2, scrypt, PBKDF2, and HKDF for different use cases.

### Q30. Why should developers not create custom cryptography?

Cryptographic design has subtle requirements around algorithms, modes, randomness, keys, nonces, errors, and side channels.

Use mature libraries and reviewed protocols.

## Key Management, KMS, And HSM

### Q31. What is key management?

Key management covers generation, storage, distribution, access, rotation, backup, recovery, revocation, and destruction of cryptographic keys.

Strong algorithms fail if keys are poorly managed.

### Q32. What is KMS?

A Key Management Service centrally manages cryptographic keys and operations with IAM and audit logs.

Cloud examples include AWS KMS, Azure Key Vault/Managed HSM, and Google Cloud KMS.

### Q33. What is an HSM?

A Hardware Security Module protects cryptographic keys and performs operations inside tamper-resistant hardware.

It is used for high-assurance key storage, signing, and compliance.

### Q34. KMS vs HSM?

KMS provides managed key lifecycle and APIs, often backed by HSMs. Dedicated HSM services provide greater direct control and isolation but require more management.

Choose based on risk, compliance, and operational capability.

### Q35. What is envelope encryption?

Data is encrypted with a data-encryption key, and that key is encrypted with a key-encryption key managed by KMS.

It supports efficient large-scale encryption and centralized key control.

### Q36. What is customer-managed key?

The customer controls lifecycle and policy for a key used by a cloud service, unlike a provider-managed key with limited customer control.

More control also creates responsibility for availability and policy.

### Q37. What is BYOK?

Bring Your Own Key lets an organization import or supply key material to a cloud or service provider.

It can support governance requirements but does not automatically prevent provider access to plaintext during service operation.

### Q38. What is HYOK?

Hold Your Own Key keeps key material outside the service provider's direct key-management domain.

It can provide stronger control but may reduce service features and availability.

### Q39. What is key rotation?

Rotation creates new key material for future encryption or signing. Existing data may remain under old key versions until re-encrypted.

Plan compatibility, re-encryption, and rollback.

### Q40. What is key separation?

Use different keys for different purposes, environments, tenants, or data classes. Do not reuse one key for encryption, signing, and unrelated systems.

Separation reduces blast radius.

## Cryptographic Design And TLS

### Q41. Encryption at rest vs in transit?

At-rest encryption protects stored data. In-transit encryption protects data moving between systems, usually through TLS.

Sensitive data often requires both.

### Q42. What is client-side encryption?

The client encrypts data before sending it to storage or a service, retaining more control over plaintext and keys.

Search, processing, recovery, and key availability become more complex.

### Q43. What is server-side encryption?

The receiving service encrypts data after receipt. It is easier to operate but the service handles plaintext.

Access control and service trust remain essential.

### Q44. What is TLS certificate validation?

Clients verify the certificate chain, hostname, validity, key usage, and trust. Disabling validation enables man-in-the-middle attacks.

Do not use "trust all certificates" in production.

### Q45. What is certificate pinning?

Pinning restricts accepted certificates or public keys beyond normal CA trust. It can reduce interception risk but complicates rotation and recovery.

Use only with a robust operational plan.

### Q46. What is forward secrecy?

Forward secrecy means compromise of a long-term private key does not decrypt previously recorded sessions.

Modern ephemeral key exchanges support it.

### Q47. What is cryptographic agility?

Crypto agility is the ability to replace algorithms, keys, certificates, and protocols without redesigning the entire system.

Store algorithm/version metadata and avoid hardcoded assumptions.

### Q48. What is a cryptographic failure?

It includes missing encryption, weak algorithms, nonce reuse, exposed keys, insecure TLS, poor password hashing, and incorrect key access.

Review the complete design rather than only algorithm names.

### Q49. What is data signing vs encryption?

Signing proves integrity and signer authenticity. Encryption provides confidentiality. Some use cases require both.

Do not confuse encoded or signed data with encrypted data.

### Q50. What is tokenization?

Tokenization replaces sensitive data with a non-sensitive reference while protected systems maintain the mapping.

It can reduce application and compliance exposure.

## Database Security

### Q51. What are core database security controls?

Use network restriction, strong authentication, least-privilege roles, encryption, patching, auditing, backups, data classification, query safety, and monitoring.

Applications should use dedicated accounts.

### Q52. Why should applications not use database admin accounts?

If the application is compromised or vulnerable to injection, admin privileges increase impact to schema changes, user creation, or full data loss.

Grant only required operations.

### Q53. What is database row-level security?

Row-level security applies policies that restrict which rows a user or context can access.

It can reinforce tenant isolation but must use trusted identity context.

### Q54. What is column-level security?

It restricts access to sensitive columns such as salary, national identifiers, or card data.

Applications should also minimize returned fields.

### Q55. What is database activity monitoring?

It observes queries, access patterns, privileged actions, and policy violations.

It supports detection and audit but requires careful privacy and performance design.

### Q56. What is transparent data encryption?

TDE encrypts database files and backups at rest without changing application queries.

It protects stolen media but not authorized queries or compromised application accounts.

### Q57. What is field-level encryption?

Specific sensitive fields are encrypted by the application or database using dedicated keys.

It provides granular protection but complicates search, indexing, and rotation.

### Q58. How do you secure database backups?

Encrypt them, restrict access, isolate copies, test restoration, apply retention, monitor downloads, and prevent public exposure.

Backups often contain the organization's most complete data set.

### Q59. What is a read replica security concern?

Replicas contain sensitive data and may have different network, IAM, logging, or encryption settings.

Apply the same security baseline as the primary.

### Q60. What is database connection-string risk?

Connection strings may contain credentials and internal topology. They often leak through code, config files, exception messages, or logs.

Use managed identity or secret managers and redact errors.

## Data Security And Privacy

### Q61. What is data classification?

Data classification categorizes data by sensitivity and handling needs, such as public, internal, confidential, and restricted.

Controls should follow the classification.

### Q62. What is data inventory?

A data inventory identifies what data exists, where it is stored, who owns it, how it flows, and how long it is retained.

It supports privacy, incident response, and architecture decisions.

### Q63. What is data minimization?

Collect, process, return, and retain only data required for a legitimate purpose.

Less data reduces breach and compliance impact.

### Q64. What is data masking?

Masking hides parts of sensitive values for display or lower environments, such as showing only the final digits.

Do not rely on frontend masking if APIs return the full value.

### Q65. What is anonymization?

Anonymization irreversibly removes the ability to identify individuals. True anonymization is difficult because datasets may be reidentified through combinations.

Privacy specialists should validate the approach.

### Q66. What is pseudonymization?

Pseudonymization replaces identifiers with references while a separate mapping can restore identity.

It reduces exposure but remains sensitive data.

### Q67. What is data residency?

Data residency concerns where data is stored or processed geographically.

Cloud region, backups, logs, support access, and replication all matter.

### Q68. What is retention policy?

It defines how long data is kept and when it is deleted or archived.

Retain enough for legal and operational needs but avoid indefinite accumulation.

### Q69. What is secure deletion?

Secure deletion makes data practically unrecoverable according to media and platform capabilities. In cloud systems, cryptographic erasure and lifecycle deletion are common.

Backups and replicas must also be considered.

### Q70. What is DLP?

Data Loss Prevention identifies and controls sensitive data movement through endpoints, email, cloud storage, or networks.

DLP supports policy but may produce false positives and cannot replace access control.

## Scenarios

### Q71. A database password is committed to Git. What do you do?

Rotate it immediately, review database logs, remove it from code/history where appropriate, move to a secret manager or managed identity, and add scanning.

Assess every environment where it was valid.

### Q72. A KMS key policy grants broad decrypt access. What do you do?

Identify affected data and identities, restrict the policy, review audit logs for decrypt operations, rotate or re-encrypt if compromise is suspected, and add policy checks.

Encryption is ineffective when everyone can decrypt.

### Q73. An application uses Base64 for confidential values. What is wrong?

Base64 is encoding and provides no confidentiality. Anyone can decode it.

Use appropriate encryption with managed keys.

### Q74. A backup bucket is private but shared with many accounts. Is it safe?

Private does not mean least privilege. Review cross-account access, encryption keys, logs, retention, object lock, and business need.

Backups need especially strict controls.

### Q75. The secrets manager is unavailable. What should happen?

Follow a designed availability strategy: caching only within approved lifetime, redundant service, controlled failover, and alerting. Do not fall back to hardcoded default credentials.

Security and availability must be designed together.

### Q76. A secret is rotated but the application fails. What does that show?

The rotation process lacks compatibility, reload, or overlap handling. Restore safely if needed, then improve staged rotation and health validation.

Rotation must be tested like deployment.

### Q77. A developer wants to log decrypted payloads for debugging. What do you do?

Reject production plaintext logging. Use synthetic test data, controlled debug environments, field-level redaction, or metadata-only diagnostics.

Logs often have broader access and retention.

### Q78. A database is encrypted but SQL injection exists. Is data protected?

No. The application has authorized decryption access, so injection may retrieve plaintext through normal queries.

Encryption at rest does not fix application authorization or query safety.

### Q79. A signing key is leaked. What do you do?

Revoke or distrust the key, stop releases, identify signed artifacts, publish replacement trust material, investigate use, rotate signing infrastructure, and communicate affected versions.

Signing-key incidents have broad supply-chain impact.

### Q80. How do you summarize secrets and data security?

Minimize sensitive data, classify it, restrict access, encrypt appropriately, manage keys and secrets through their full lifecycle, monitor use, protect backups, and design reliable rotation and recovery.

Cryptography is only effective when identity, authorization, and operations are secure.

