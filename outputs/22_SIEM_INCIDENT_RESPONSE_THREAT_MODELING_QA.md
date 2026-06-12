# SIEM, Incident Response, And Threat Modeling Deep Q&A

This file covers security monitoring, SIEM/SOAR, incident handling, cloud investigation, threat modeling, and scenario response. These skills distinguish a candidate who understands the full DevSecOps feedback loop.

## Logging And SIEM

### Q1. What is SIEM?

SIEM means Security Information and Event Management. It collects, normalizes, correlates, searches, and alerts on logs from applications, cloud, identity, endpoints, networks, and security tools.

Examples include Splunk, Microsoft Sentinel, Google Security Operations, QRadar, and Elastic Security.

### Q2. Why is SIEM important?

Individual logs show isolated events. SIEM correlates events across systems to detect patterns such as credential compromise, privilege escalation, data exfiltration, or malware.

It also supports investigation, reporting, and compliance.

### Q3. What is log source?

A log source is a system producing events, such as CloudTrail, Entra ID, GCP Audit Logs, firewalls, WAF, EDR, Kubernetes, Linux auth logs, applications, and CI/CD.

Critical systems should have defined log requirements.

### Q4. What is log normalization?

Normalization converts different log formats into a consistent schema so events can be searched and correlated.

Fields often include timestamp, user, source IP, destination, action, result, and resource.

### Q5. What is log correlation?

Correlation connects related events across sources. Example: many failed logins, a successful login from a new country, MFA change, and privileged action.

Correlation reduces isolated-event blindness.

### Q6. What is a detection rule?

A detection rule defines suspicious conditions that generate an alert. It may use thresholds, sequences, behavior, threat intelligence, or anomaly models.

Rules require tuning and testing.

### Q7. What is alert fatigue?

Alert fatigue occurs when analysts receive too many low-quality alerts and may miss important ones.

Reduce it through tuning, deduplication, risk scoring, enrichment, and automation.

### Q8. What is a false positive alert?

A false positive alert matches a detection rule but is benign. Examples include approved admin activity or expected scanner traffic.

Tune carefully without hiding real attacks.

### Q9. What is a false negative in detection?

A false negative is malicious activity not detected. It may occur because logs are missing, rules lack coverage, attackers evade known patterns, or alert thresholds are wrong.

Threat hunting and coverage reviews help find gaps.

### Q10. What is log retention?

Log retention defines how long logs are stored. Requirements depend on investigation needs, compliance, cost, and data sensitivity.

Critical audit logs should be protected from alteration or deletion.

## SIEM Tools And Concepts

### Q11. What is Splunk?

Splunk collects, indexes, searches, and analyzes machine data. Splunk Enterprise Security adds security-focused correlation, risk, and investigation capabilities.

SPL, Search Processing Language, is used for queries.

### Q12. What is Microsoft Sentinel?

Microsoft Sentinel is a cloud-native SIEM and SOAR platform. It integrates strongly with Azure, Entra ID, Microsoft Defender, and many third-party sources.

KQL is used for log queries and analytics.

### Q13. What is Google Security Operations?

Google Security Operations, including technology historically known as Chronicle, provides security analytics, SIEM capabilities, threat intelligence, and investigation at scale.

It can ingest multicloud and enterprise telemetry.

### Q14. What is Elastic Security?

Elastic Security builds SIEM, search, and detection capabilities on the Elastic Stack.

It is used for centralized logs, endpoint telemetry, dashboards, and alerting.

### Q15. What is QRadar?

IBM QRadar is a SIEM platform for log/event collection, correlation, offenses, and investigation.

The specific tool matters less than understanding detection and response workflow.

### Q16. What is SOAR?

SOAR means Security Orchestration, Automation, and Response. It automates workflows such as enrichment, ticket creation, notification, account disabling, or IP blocking.

High-impact actions should include safety checks and approvals.

### Q17. What is a playbook?

A playbook is a documented or automated sequence of investigation and response actions for an alert or incident.

Examples include leaked credential, public bucket, malware, and suspicious login playbooks.

### Q18. What is enrichment?

Enrichment adds context to an alert, such as asset owner, IP reputation, geolocation, user role, vulnerability status, or cloud resource tags.

Context helps analysts prioritize.

### Q19. What is UEBA?

User and Entity Behavior Analytics identifies unusual behavior compared with normal baselines, such as abnormal login times, data downloads, or service-account actions.

Anomalies require validation because unusual does not always mean malicious.

### Q20. What is threat intelligence?

Threat intelligence provides information about malicious IPs, domains, hashes, campaigns, vulnerabilities, and attacker techniques.

It can enrich alerts but should be evaluated for freshness and confidence.

## Detection Engineering

### Q21. What is detection engineering?

Detection engineering designs, tests, deploys, and maintains detection rules based on threats and available telemetry.

It includes use-case definition, log validation, tuning, documentation, and coverage mapping.

### Q22. What is MITRE ATT&CK?

MITRE ATT&CK is a knowledge base of adversary tactics and techniques. Teams use it to map detections, incidents, and control coverage.

It helps identify detection gaps across an attack lifecycle.

### Q23. What is a tactic?

A tactic represents an attacker's goal, such as initial access, persistence, privilege escalation, credential access, discovery, or exfiltration.

Techniques describe how the goal is achieved.

### Q24. What is detection coverage?

Detection coverage measures which threats, assets, and ATT&CK techniques are observable and alerted on.

Coverage requires both logs and effective rules.

### Q25. What is a detection hypothesis?

A detection hypothesis describes suspicious behavior you expect to observe. Example: "An attacker using stolen AWS keys may create new access keys or assume unusual roles."

It guides log selection and rule design.

### Q26. What is rule tuning?

Rule tuning adjusts logic, thresholds, allowlists, and risk scores to reduce noise while preserving attack detection.

Tuning should be evidence-based and documented.

### Q27. What is threshold detection?

Threshold detection alerts when an event count exceeds a limit, such as 20 failed logins in five minutes.

It is simple but attackers may stay below thresholds.

### Q28. What is sequence detection?

Sequence detection identifies a chain of events in order, such as login, privilege change, and secret access.

It often produces higher-confidence alerts than single events.

### Q29. What is anomaly detection?

Anomaly detection identifies activity that differs from historical patterns. It may detect new attack behavior but can be noisy.

Use asset and user context to triage.

### Q30. How do you test a detection?

Generate safe controlled events in a test environment, confirm logs arrive, verify rule logic and alert context, and measure expected false positives.

Detection-as-code can version and review rules.

## Incident Response Fundamentals

### Q31. What is a security incident?

A security incident is an event that compromises or threatens confidentiality, integrity, availability, policy, or operations and requires coordinated response.

Not every alert becomes an incident.

### Q32. What are incident response phases?

Common phases are preparation, detection/analysis, containment, eradication, recovery, and lessons learned.

Different frameworks use slightly different wording, but the workflow is similar.

### Q33. What is containment?

Containment limits ongoing damage. Examples include disabling credentials, isolating hosts, blocking access, making a bucket private, or pausing deployments.

Containment should preserve evidence where possible.

### Q34. What is eradication?

Eradication removes the root cause and attacker persistence, such as deleting malicious accounts, patching vulnerabilities, removing malware, and rotating secrets.

Do not recover before understanding persistence.

### Q35. What is recovery?

Recovery restores normal operations securely. It may involve rebuilding systems, restoring data, redeploying trusted artifacts, monitoring closely, and validating controls.

Recovery should avoid reintroducing the original weakness.

### Q36. What is lessons learned?

Lessons learned reviews what happened, why controls failed, what worked, and what preventive changes are needed.

It should produce accountable actions, not blame.

### Q37. What is incident severity?

Incident severity reflects impact, scope, data sensitivity, operational disruption, legal obligations, and attacker activity.

Severity determines escalation and response urgency.

### Q38. What is chain of custody?

Chain of custody documents who collected, handled, transferred, and stored evidence. It supports integrity and legal defensibility.

Use approved forensic procedures for serious incidents.

### Q39. What is an indicator of compromise?

An IOC is observable evidence associated with malicious activity, such as an IP, domain, hash, account, file, process, or registry key.

IOCs can change quickly and should be combined with behavioral detections.

### Q40. What is an indicator of attack?

An indicator of attack represents suspicious behavior or technique, such as credential dumping or unusual role assumption.

Behavioral indicators can be more durable than static IOCs.

## Cloud Incident Response

### Q41. How do you respond to leaked AWS credentials?

Disable or rotate the credentials, review CloudTrail activity, identify IAM permissions, search for new users/roles/keys and resource changes, check data access, and contain persistence.

Then migrate to roles/OIDC and add secret scanning.

### Q42. How do you respond to Azure identity compromise?

Revoke sessions, reset credentials, require MFA, review Entra sign-in/audit logs, inspect role assignments, service principals, app consent, and resource activity.

Use Conditional Access and PIM to reduce recurrence.

### Q43. How do you respond to GCP service-account key leak?

Disable/delete the key, review Cloud Audit Logs, inspect IAM and resource activity, rotate downstream secrets, and migrate to Workload Identity Federation.

Consider organization policy to block user-managed key creation.

### Q44. How do you respond to a public storage bucket?

Remove unintended public access, identify exposed data and duration, review access logs, notify owners, assess regulatory obligations, rotate exposed credentials, and add preventive guardrails.

Do not close after configuration change without impact analysis.

### Q45. How do you isolate a compromised cloud VM?

Change security groups/NSGs/firewall rules to restrict traffic, preserve disk snapshots and logs, avoid destroying evidence, review identity and network activity, and rebuild from trusted images.

Use incident-specific forensic procedures.

### Q46. How do you respond to crypto mining in Kubernetes?

Isolate or stop affected workloads, preserve evidence, identify the image/deployment source, inspect audit and runtime logs, review service-account/cloud IAM, rotate exposed secrets, and rebuild securely.

Add admission and runtime controls.

### Q47. What is cloud persistence?

Cloud persistence includes new users, access keys, roles, trust policies, service principals, OAuth apps, scheduled functions, startup scripts, and modified pipelines.

Incident response must search beyond the original credential.

### Q48. What is cloud log tampering?

Attackers may disable trails, delete logs, alter sinks, or change retention. These actions should generate high-priority alerts.

Centralize logs in protected accounts/projects and restrict deletion.

### Q49. What is blast radius?

Blast radius is the extent of resources and data affected by compromise. Least privilege, account/project separation, network segmentation, and isolated pipelines reduce it.

Incident triage should estimate blast radius quickly.

### Q50. What is break-glass access?

Break-glass access is emergency privileged access used when normal identity systems fail. It should be tightly protected, monitored, tested, and rarely used.

Every use should trigger review.

## DevSecOps Incident Scenarios

### Q51. A malicious commit reaches production. What do you do?

Pause deployments, identify commit and author/context, verify artifacts, roll back or contain, rotate exposed secrets, review pipeline permissions, and inspect repository/audit logs.

Add stronger branch protection, signing, review, and provenance.

### Q52. CI runner is compromised. What do you do?

Stop affected runners, rotate accessible credentials, inspect jobs and artifacts, identify persistence, rebuild runners from trusted images, and review network access.

Prefer ephemeral isolated runners.

### Q53. A critical dependency CVE is actively exploited. What do you do?

Identify affected assets through SCA/SBOM, prioritize internet-facing systems, patch or mitigate urgently, monitor exploitation indicators, and communicate status.

Use emergency change procedures when needed.

### Q54. A WAF blocks thousands of SQL injection attempts. Is this an incident?

It may be routine internet noise or targeted attack. Check affected endpoints, whether requests bypassed the WAF, application/database logs, source patterns, and vulnerability status.

An alert becomes an incident based on evidence and impact.

### Q55. Secret scanner finds a key in an old commit. Is it still urgent?

Check whether the key remains valid. If valid or uncertain, revoke/rotate and review usage. Old commits are widely copied.

Secret age does not guarantee safety.

### Q56. A container image signature fails verification. What do you do?

Block deployment, verify registry and signing identity, investigate whether image/tag was replaced, review CI logs and provenance, and rebuild from trusted source if needed.

Signature failure may indicate tampering or process error.

### Q57. An employee downloads unusual amounts of data. What do you do?

Validate business need, identity, device, destination, data sensitivity, timing, and access pattern. Escalate according to insider-risk and privacy procedures if suspicious.

Avoid assumptions before evidence.

### Q58. A production API token is found in logs. What do you do?

Rotate/revoke token, restrict log access, search for use, remove/redact sensitive logs according to policy, fix logging code, and add detection/tests.

Treat logs as a potential credential-exposure source.

### Q59. Cloud bill spikes unexpectedly. What security causes are possible?

Crypto mining, resource abuse, DDoS-related scaling, leaked credentials, serverless/API abuse, or misconfiguration may cause spikes.

Correlate billing, cloud audit, network, and workload telemetry.

### Q60. How does DevSecOps improve incident response?

It provides asset metadata, reproducible infrastructure, centralized logs, SBOMs, signed artifacts, automated containment, quick rebuilds, and clear ownership.

The delivery system becomes part of response and recovery.

## Threat Modeling

### Q61. What is threat modeling?

Threat modeling is a structured process to identify assets, trust boundaries, threats, attack paths, and mitigations before or during system development.

It turns security into design requirements.

### Q62. When should threat modeling occur?

During initial design, major architectural changes, new trust boundaries, sensitive features, cloud migrations, and significant integrations.

Update it when the system changes.

### Q63. What is a data-flow diagram?

A data-flow diagram shows processes, data stores, external entities, data flows, and trust boundaries.

It helps teams reason about where untrusted data enters and sensitive data moves.

### Q64. What is a trust boundary?

A trust boundary is a point where data or control crosses between different trust levels, such as internet to API, app to database, tenant to tenant, or CI to production cloud.

Every boundary needs authentication, authorization, validation, and logging.

### Q65. What is STRIDE?

STRIDE stands for Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, and Elevation of Privilege.

It provides systematic threat categories.

### Q66. What is spoofing?

Spoofing means impersonating a user, service, or system. Controls include authentication, MFA, certificates, and token validation.

Identity threats map strongly to spoofing.

### Q67. What is tampering?

Tampering means unauthorized modification of data, code, configuration, or messages. Controls include integrity checks, signatures, authorization, and audit logs.

Artifact signing addresses build tampering.

### Q68. What is repudiation?

Repudiation means denying an action without sufficient evidence to prove it. Controls include reliable audit logs, identity, timestamps, and log integrity.

Logging supports accountability.

### Q69. What is information disclosure?

Information disclosure is unauthorized exposure of data. Controls include encryption, access control, data minimization, and redaction.

Many AppSec vulnerabilities map here.

### Q70. What is denial of service?

Denial of service reduces availability through resource exhaustion, crashes, or traffic overload. Controls include limits, scaling, quotas, DDoS protection, and resilient design.

Availability is part of security.

### Q71. What is elevation of privilege?

Elevation of privilege lets an identity gain higher permissions. Controls include authorization, least privilege, segmentation, and privilege monitoring.

IAM and RBAC reviews address this threat.

### Q72. What is an abuse case?

An abuse case describes how legitimate functionality could be misused, such as OTP spam, coupon reuse, excessive data export, or malicious file processing.

It helps find business logic risks.

### Q73. What is attack tree?

An attack tree starts with an attacker goal and branches into possible methods to achieve it.

It helps teams identify multiple attack paths and layered controls.

### Q74. What is threat likelihood?

Likelihood estimates how probable a threat is based on exposure, attacker capability, controls, history, and exploitability.

Combine likelihood with impact to prioritize risks.

### Q75. What is threat-model output?

Outputs include diagrams, assets, threats, assumptions, mitigations, security requirements, owners, and accepted risks.

Threat models should result in actionable work.

## Threat Modeling Scenarios

### Q76. Threat-model a file upload feature.

Assets include uploaded content, users, storage, and processing service. Threats include malware, web shells, stored XSS, parser exploits, oversized files, public exposure, and cross-user access.

Controls include allowlists, re-encoding, scanning, isolated processing, private storage, authorization, size limits, and logging.

### Q77. Threat-model a CI/CD pipeline.

Assets include source, secrets, runners, artifacts, signing keys, cloud roles, and production. Threats include malicious commits, compromised actions, secret theft, runner escape, artifact tampering, and unauthorized deployment.

Controls include branch protection, least privilege, isolated runners, scanning, signing, provenance, approvals, and audit logs.

### Q78. Threat-model a cloud-hosted API.

Assets include data, tokens, service identity, database, and cloud resources. Threats include auth bypass, IDOR, injection, SSRF, DDoS, secret leakage, and over-privileged IAM.

Controls span application, network, IAM, encryption, logging, and monitoring.

### Q79. Threat-model a Kubernetes workload.

Assets include image, service-account token, secrets, node, cloud identity, and internal services. Threats include vulnerable image, pod compromise, lateral movement, secret theft, privileged escape, and cloud IAM abuse.

Controls include image scanning/signing, non-root, RBAC, NetworkPolicy, workload identity, admission control, and runtime detection.

### Q80. How do you summarize monitoring, incident response, and threat modeling?

Threat modeling predicts what could go wrong, DevSecOps controls prevent it, monitoring detects failures or attacks, and incident response contains and recovers. Lessons learned then improve the design and pipeline.

That closed feedback loop is a mature DevSecOps program.

