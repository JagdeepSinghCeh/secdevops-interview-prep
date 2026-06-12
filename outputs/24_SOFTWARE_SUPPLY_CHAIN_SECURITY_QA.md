# Software Supply Chain Security Deep Q&A

This file covers dependencies, build systems, SBOMs, provenance, artifact signing, SLSA, Sigstore, package registries, CI/CD compromise, and secure release practices. It references the current SLSA specification version 1.2.

## Supply Chain Fundamentals

### Q1. What is the software supply chain?

The software supply chain includes source code, contributors, dependencies, package registries, build systems, CI/CD workflows, artifacts, signing systems, deployment tools, and runtime platforms.

Attackers compromise trusted components or processes to reach many downstream systems.

### Q2. What is a software supply-chain attack?

It is an attack that compromises software through a supplier, dependency, source repository, build process, artifact, update channel, or deployment system.

The final application may contain malicious or tampered code despite its own developers not adding it intentionally.

### Q3. Why are supply-chain attacks dangerous?

Organizations trust signed packages, dependencies, build pipelines, and updates. A compromise can spread through that trust and affect many customers or environments.

Build and release systems often have privileged access.

### Q4. What are common supply-chain attack paths?

Compromised maintainer accounts, malicious packages, dependency confusion, typosquatting, build runner compromise, workflow injection, artifact replacement, stolen signing keys, and poisoned updates are common paths.

Defense requires controls across the full lifecycle.

### Q5. What is a trusted build?

A trusted build runs in a controlled, isolated, auditable environment from reviewed source and produces traceable artifacts.

It should minimize manual access and protect credentials and signing identities.

### Q6. What is reproducible build?

A reproducible build produces identical output from the same source, dependencies, and build instructions.

It helps detect tampering but can be difficult when timestamps, environment, or nondeterministic tools affect output.

### Q7. What is hermetic build?

A hermetic build uses declared inputs and does not depend on uncontrolled network, host tools, or environment state.

It improves reproducibility and limits dependency substitution.

### Q8. What is build isolation?

Build isolation separates jobs and removes state between builds. Ephemeral runners and sandboxed build environments reduce cross-job contamination.

Sensitive builds should not share runners with untrusted code.

### Q9. What is artifact promotion?

Artifact promotion moves the exact same tested artifact from development to staging and production.

Rebuilding per environment can produce different, unverified outputs.

### Q10. What is release integrity?

Release integrity ensures published software matches reviewed source and authorized build output and has not been altered.

Signing, provenance, protected workflows, and independent verification support it.

## Dependencies And Package Registries

### Q11. What is dependency confusion?

Dependency confusion occurs when a build resolves a malicious public package instead of an intended private package with the same name.

Use scoped names, private registry configuration, source restrictions, and version controls.

### Q12. What is typosquatting?

Attackers publish package names that resemble popular dependencies, hoping developers mistype or select the wrong package.

Review names and maintain approved dependency policies.

### Q13. What is a malicious package?

A malicious package intentionally steals secrets, runs commands, adds backdoors, or downloads payloads during installation, build, or runtime.

Do not rely only on known-CVE scanning to detect intentional malicious behavior.

### Q14. What is a compromised maintainer?

An attacker gains access to a legitimate maintainer or registry account and publishes a malicious release under a trusted package name.

MFA, hardware keys, protected publishing, and review reduce this risk.

### Q15. What is package-locking?

Lockfiles record exact dependency versions and integrity values. Examples include `package-lock.json`, `poetry.lock`, and `Cargo.lock`.

Commit and review lockfile changes.

### Q16. What is dependency pinning?

Pinning constrains dependencies to exact versions or hashes. It prevents unexpected updates but requires an active update process.

Stale pinned dependencies can remain vulnerable.

### Q17. What is transitive dependency risk?

Applications inherit dependencies used by direct dependencies. These transitive packages may contain vulnerabilities or malicious code.

SCA and SBOM tools should inventory the complete dependency graph.

### Q18. What is install-script risk?

Package managers may execute lifecycle scripts during installation. Malicious packages can steal CI secrets or modify build output at that stage.

Restrict scripts where possible and isolate builds.

### Q19. What is a private package registry?

A private registry stores approved internal or mirrored packages under organizational access control.

It improves governance but must itself be patched, monitored, backed up, and protected.

### Q20. What is registry immutability?

Immutability prevents published package versions or image tags from being overwritten.

It protects consumers from silent replacement of known releases.

## SBOM

### Q21. What is an SBOM?

A Software Bill of Materials lists components, versions, relationships, and identifiers inside software.

It supports vulnerability response, license review, supplier transparency, and asset inventory.

### Q22. What are common SBOM formats?

CycloneDX and SPDX are widely used machine-readable SBOM formats.

Tool and ecosystem support may influence the chosen format.

### Q23. What is CycloneDX?

CycloneDX is an OWASP-supported bill-of-materials standard designed for software supply-chain risk use cases.

It can represent components, services, dependencies, vulnerabilities, and other metadata.

### Q24. What is SPDX?

SPDX is an open standard for communicating software components, licenses, files, and package information.

It is maintained through the Linux Foundation ecosystem and standardized internationally.

### Q25. How is an SBOM generated?

It can be generated from source manifests, lockfiles, built filesystems, container images, or build tools. Tools include Syft, Trivy, CycloneDX plugins, and platform scanners.

Build-time generation generally provides strong artifact linkage.

### Q26. Is an SBOM a vulnerability report?

No. An SBOM inventories components. Vulnerability scanners correlate that inventory with advisory data.

An SBOM may include vulnerability information, but the concepts remain distinct.

### Q27. What is SBOM completeness?

Completeness reflects whether direct, transitive, vendored, generated, operating-system, and runtime components are represented.

An incomplete SBOM can create false confidence.

### Q28. What is SBOM freshness?

The SBOM should match the exact current artifact or release. An old SBOM may not represent rebuilt dependencies or base images.

Generate and store one per release artifact.

### Q29. How should SBOMs be stored?

Associate them with artifacts or releases in a controlled registry, attestation store, or vulnerability platform. Protect integrity and access if they reveal sensitive architecture.

They should be retrievable during incident response.

### Q30. How does SBOM help during a new CVE?

Teams search inventories for the affected package and version, identify deployed applications and images, assign owners, and prioritize remediation.

SBOM usefulness depends on accurate asset-to-artifact mapping.

## Provenance And Attestations

### Q31. What is build provenance?

Build provenance records how, where, and from which inputs an artifact was produced. It may include source repository, commit, builder, workflow, dependencies, and parameters.

Consumers use it to verify artifact origin.

### Q32. What is an attestation?

An attestation is a signed statement about an artifact or process, such as build provenance, test results, vulnerability scan status, or policy compliance.

The signer and verification policy determine trust.

### Q33. What is in-toto?

in-toto provides a framework and attestation formats for recording and verifying steps in a software supply chain.

It helps confirm that expected actors performed expected steps on expected materials.

### Q34. What is artifact digest?

A digest is a cryptographic hash identifying exact artifact content. Any content change produces a different digest.

Container deployments should prefer immutable digests for strong identity.

### Q35. Tag vs digest?

A tag is a human-readable mutable reference such as `v1` or `latest`. A digest identifies exact immutable content.

Use tags for usability and digests for verification.

### Q36. What is provenance verification?

The consumer validates the provenance signature, trusted builder identity, source repository, workflow, parameters, and artifact digest against policy.

Simply storing provenance without verifying it adds little protection.

### Q37. What is source-to-artifact traceability?

It means identifying the exact reviewed source commit and build that produced a deployed artifact.

This is essential for incident investigation and controlled rollback.

### Q38. What is a build attestor?

An attestor is an identity or system authorized to make signed claims about builds or artifacts.

Protect attestor identities and limit what workflows may use them.

### Q39. What is policy-based attestation?

Deployment policy requires specific attestations, such as approved source, successful scan, trusted builder, and valid signature.

Admission controllers can enforce these rules.

### Q40. What is provenance tampering risk?

If provenance is unsigned, weakly bound to artifacts, or generated by an untrusted process, attackers may forge origin information.

Sign attestations and verify artifact digests.

## SLSA

### Q41. What is SLSA?

SLSA, Supply-chain Levels for Software Artifacts, is a framework and specification for improving software supply-chain security.

The current specification is version 1.2.

### Q42. What problem does SLSA solve?

SLSA helps organizations protect source and build processes and produce trustworthy provenance. It provides requirements and maturity concepts for preventing tampering.

It is a framework, not a scanning product.

### Q43. What is the SLSA build track?

The build track focuses on provenance and build-system protections. Higher assurance requires stronger provenance, hosted builds, isolation, and resistance to tampering.

Organizations can adopt it incrementally.

### Q44. What is SLSA provenance?

SLSA provenance describes the artifact's build process and inputs in a standardized attestation.

It should be generated by the build platform rather than manually supplied by the developer.

### Q45. Does SLSA guarantee vulnerability-free software?

No. SLSA protects supply-chain integrity, not application correctness or absence of vulnerabilities.

You still need SAST, SCA, testing, secure design, and runtime controls.

### Q46. How does SLSA relate to CI/CD?

CI/CD platforms can generate provenance, isolate builds, restrict workflows, and sign outputs. SLSA provides assurance requirements for those capabilities.

Pipeline security is therefore central to SLSA adoption.

### Q47. How do you start adopting SLSA?

Inventory build systems, generate provenance, use hosted or controlled builders, protect source branches, isolate builds, sign artifacts, and verify provenance at deployment.

Start with critical services and improve iteratively.

### Q48. SLSA vs SBOM?

SLSA focuses on artifact integrity and build provenance. An SBOM inventories components inside the artifact.

They complement each other.

### Q49. SLSA vs SSDF?

SLSA focuses strongly on software supply-chain integrity. NIST Secure Software Development Framework covers broader secure-development practices.

Organizations may use both.

### Q50. What is a SLSA interview answer?

Explain that SLSA is a supply-chain security framework that improves trust in source, builders, provenance, and artifacts. Mention that it does not replace vulnerability testing.

Connect it to protected Git, isolated CI, provenance, signing, and deployment verification.

## Sigstore And Signing

### Q51. What is Sigstore?

Sigstore is an open-source ecosystem for signing, verifying, and recording software artifact identities and signatures.

It aims to make software signing easier and more transparent.

### Q52. What is Cosign?

Cosign is a Sigstore tool used to sign and verify container images, blobs, and attestations.

It supports key-based and keyless workflows.

### Q53. What is keyless signing?

Keyless signing uses an OIDC identity and short-lived signing certificate instead of a long-lived private signing key.

It reduces key-management burden but requires strong identity and workflow policy.

### Q54. What is Fulcio?

Fulcio is Sigstore's certificate authority for issuing short-lived signing certificates tied to authenticated identities.

Verification checks the certificate identity and trust chain.

### Q55. What is Rekor?

Rekor is Sigstore's transparency log for recording signed artifact metadata.

Transparency helps detect unexpected or disputed signing activity.

### Q56. What is signature verification?

Verification confirms that an artifact signature is valid, matches the artifact, and belongs to an identity trusted by policy.

A cryptographically valid but unauthorized signer should still be rejected.

### Q57. Where should images be signed?

Sign after the artifact has passed required tests and scans and before promotion. The signing identity should be available only to the trusted release workflow.

Do not expose signing credentials to untrusted pull requests.

### Q58. How can Kubernetes enforce signed images?

Admission policy can verify Cosign signatures or attestations before allowing workloads. Tools include Kyverno, Sigstore Policy Controller, Ratify, and other admission systems.

Policies should specify trusted identities and repositories.

### Q59. What if a signature is missing?

For protected environments, block deployment or require an explicit controlled exception. Investigate whether the artifact bypassed the release workflow.

Do not silently treat unsigned and signed artifacts as equivalent.

### Q60. What if a signature is valid but the image is vulnerable?

The signature proves origin and integrity, not security quality. A trusted pipeline can still build vulnerable software.

Require vulnerability and policy attestations in addition to signatures.

## CI/CD And Repository Protection

### Q61. How can a malicious pull request attack CI?

It may alter workflow files, execute scripts during builds, poison tests, access caches, exploit runners, or steal tokens if privileged workflows process untrusted code.

Use isolated unprivileged PR workflows with no production secrets.

### Q62. What is workflow injection?

Workflow injection occurs when attacker-controlled data is inserted into shell commands or workflow expressions and becomes executable code.

Pass data through safe environment variables or arguments and avoid inline interpolation.

### Q63. What is cache poisoning?

Attackers place malicious files into shared build caches that later trusted builds consume.

Separate trust levels, bind cache keys to immutable inputs, validate contents, and avoid sharing caches with untrusted jobs.

### Q64. What is runner persistence?

An attacker modifies a reusable runner so later jobs execute malicious code or expose secrets.

Prefer ephemeral runners rebuilt from trusted images.

### Q65. What is build secret exfiltration?

Malicious build code reads environment variables, credential files, metadata, or mounted secrets and sends them externally.

Do not expose release secrets to untrusted code; restrict egress and use stage-specific identities.

### Q66. Why protect workflow files with CODEOWNERS?

Workflow changes can alter permissions, leak secrets, or bypass security gates. Requiring platform/security review reduces this risk.

Branch protection should enforce the review.

### Q67. Why separate build and release pipelines?

Build jobs process potentially untrusted source, while release jobs require powerful publishing and deployment permissions. Separation limits credential exposure.

Promote verified artifacts rather than rebuilding.

### Q68. What is two-person release control?

Sensitive releases require independent approval from another authorized person or system.

It reduces insider and account-compromise risk.

### Q69. What is release rollback risk?

An old artifact may be signed but contain known critical vulnerabilities. Rollback policy must consider both availability and security.

Maintain a set of trusted viable rollback versions.

### Q70. What is a secure release checklist?

Verify protected source, successful tests/scans, SBOM, provenance, trusted builder, artifact digest, signature, approvals, release notes, and rollback plan.

Automate verification wherever possible.

## Supply Chain Scenarios

### Q71. A dependency maintainer account is compromised. What do you do?

Identify affected versions and artifacts through lockfiles and SBOMs, stop new builds, verify package integrity, replace or pin safe versions, rebuild, and investigate deployed systems.

Monitor vendor and registry advisories.

### Q72. A container tag was overwritten after approval. What do you do?

Block deployment, compare digests, inspect registry audit logs, identify who changed it, rotate compromised credentials, and rebuild through the trusted pipeline.

Enable immutability and deploy by digest.

### Q73. A CI action tag points to a new malicious commit. What control failed?

The workflow trusted a mutable tag. Pin third-party actions to reviewed commit SHAs and use automated update pull requests.

Also review token permissions and rotate exposed secrets.

### Q74. The SBOM does not include OS packages. Is it complete?

Not for a container or system artifact containing OS packages. Generate the SBOM from the final image/filesystem and include all relevant ecosystems.

Validate tool coverage.

### Q75. Provenance says the artifact came from an unknown builder. What do you do?

Reject or quarantine it under protected policy. Verify the builder identity, source, workflow, and signature before use.

Only trusted builders should produce production artifacts.

### Q76. A signed package contains malware. How is that possible?

Signing proves the package was signed by a key or identity; it does not prove the signer or build was uncompromised or the code was safe.

You still need review, scanning, provenance, and behavioral controls.

### Q77. A private package name appears publicly with a higher version. What is the risk?

This may be dependency confusion. Stop builds, verify resolver configuration, reserve or scope package names, and ensure internal registries take precedence safely.

Review recent artifacts for the public package.

### Q78. A build runner contacted an unknown domain. What do you do?

Pause the runner, preserve evidence, identify the job and process, review dependency/install scripts, rotate accessible credentials, and rebuild the runner.

Egress monitoring can reveal supply-chain compromise.

### Q79. How do you prioritize supply-chain controls?

Start with protected source, MFA, dependency management, isolated builds, least-privilege CI, SBOMs, immutable registries, signing, provenance, and deployment verification.

Prioritize systems with broad customer or production impact.

### Q80. How do you summarize software supply-chain security?

Trust must be established from source through dependency resolution, build, artifact storage, signing, promotion, and deployment. Inventory components, isolate builds, minimize credentials, generate SBOM and provenance, sign outputs, and verify policy before release.

No single scanner or signature secures the entire chain.

