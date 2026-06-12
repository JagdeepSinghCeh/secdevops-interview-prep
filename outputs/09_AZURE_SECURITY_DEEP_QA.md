# Azure Security Deep Q&A

This file focuses on Microsoft Azure security interview preparation for DevSecOps, CloudSec, and AppSec roles. Azure interviewers often test identity, RBAC, storage, Key Vault, networking, Defender for Cloud, Sentinel, and Azure DevOps security.

## Azure Fundamentals

### Q1. What is Azure from a security point of view?

Azure is Microsoft's cloud platform for compute, storage, networking, databases, identity, security, DevOps, and analytics. From a security perspective, Azure is deeply identity-driven because Microsoft Entra ID and RBAC control access across subscriptions and resources.

Azure security focuses on Entra ID, MFA, conditional access, RBAC, resource locks, network security groups, private endpoints, Key Vault, Defender for Cloud, Sentinel, Azure Policy, and secure DevOps pipelines.

### Q2. What is Azure shared responsibility?

Azure shared responsibility means Microsoft secures the underlying cloud infrastructure, while customers secure their identities, data, applications, configurations, access permissions, and workloads. The responsibility split depends on whether the service is IaaS, PaaS, or SaaS.

For VMs, customers patch and harden the OS. For Azure SQL or App Service, Microsoft manages more platform layers, but customers still manage data, identity, configuration, and application security.

### Q3. What is an Azure tenant?

An Azure tenant is a dedicated instance of Microsoft Entra ID used to manage identities and access. Users, groups, applications, and service principals live in the tenant.

Security relevance: tenant configuration, conditional access, MFA, app registrations, and privileged roles are central to Azure security.

### Q4. What is an Azure subscription?

An Azure subscription is a billing and management boundary for Azure resources. It is associated with a tenant and contains resource groups and resources.

Security teams use subscriptions to separate environments, workloads, and ownership. RBAC can be assigned at subscription scope.

### Q5. What is a resource group?

A resource group is a logical container for Azure resources. RBAC, policies, locks, and lifecycle management can be applied at resource group level.

Security relevance: resource group structure affects access control and operational ownership.

### Q6. What is Azure management group?

Management groups organize subscriptions into a hierarchy. Policies and RBAC can be applied at management group level.

Security teams use management groups to enforce governance across many subscriptions.

### Q7. What is Azure Policy?

Azure Policy enforces or audits rules for Azure resources. Examples include requiring encryption, restricting regions, denying public IPs, or enforcing tags.

It is a preventive and detective governance tool, similar in spirit to cloud guardrails.

### Q8. What is an Azure Blueprint?

Azure Blueprints were used to package policies, role assignments, and templates for repeatable environments. Azure has been moving governance patterns toward Template Specs, deployment stacks, and Azure Policy-based approaches.

In interviews, focus more on Azure Policy and landing zone governance unless the interviewer specifically asks about Blueprints.

### Q9. What is Azure landing zone?

An Azure landing zone is a secure baseline architecture for Azure environments. It includes identity, management groups, subscriptions, networking, policy, logging, and security controls.

It helps organizations deploy workloads consistently and securely.

### Q10. What are resource locks?

Resource locks prevent accidental deletion or modification of Azure resources. Lock types include CanNotDelete and ReadOnly.

Security relevance: locks can protect critical resources, but they do not replace RBAC or monitoring.

## Microsoft Entra ID And RBAC

### Q11. What is Microsoft Entra ID?

Microsoft Entra ID, formerly Azure Active Directory, is Microsoft's cloud identity and access management service. It manages users, groups, applications, service principals, authentication, and conditional access.

Azure security depends heavily on Entra ID because identity compromise can lead to cloud compromise.

### Q12. What is Azure RBAC?

Azure RBAC controls access to Azure resources using role assignments. A role assignment connects a security principal, role definition, and scope.

Scopes include management group, subscription, resource group, and resource. Use least privilege and avoid broad Owner assignments.

### Q13. What are Azure built-in roles?

Azure built-in roles include Owner, Contributor, Reader, User Access Administrator, Security Reader, and many service-specific roles.

Owner is powerful because it can manage resources and access. Contributor can manage resources but not access assignments. User Access Administrator can manage access and is highly sensitive.

### Q14. What is a custom role in Azure?

A custom role defines a specific set of permissions when built-in roles are too broad or not suitable.

Security benefit: custom roles help implement least privilege, but they need maintenance and review.

### Q15. What is a service principal?

A service principal is an identity used by applications, scripts, pipelines, or automation to access Azure resources. It is similar to a non-human identity.

Security concerns include over-permission, long-lived client secrets, certificate management, and lack of monitoring.

### Q16. What is managed identity?

Managed identity provides an Azure-managed identity for resources like VMs, App Service, Functions, and AKS. It allows resources to access other Azure services without storing credentials in code.

Security benefit: it reduces long-lived secrets and supports least-privilege access through RBAC.

### Q17. System-assigned vs user-assigned managed identity?

A system-assigned managed identity is tied to a single resource and deleted with it. A user-assigned managed identity is a standalone identity that can be attached to multiple resources.

Use system-assigned for simple one-resource identity and user-assigned for shared or lifecycle-independent identities.

### Q18. What is Conditional Access?

Conditional Access applies access decisions based on signals like user, location, device, risk, application, and MFA status. It can require MFA, block risky sign-ins, or restrict access.

It is a key Azure identity security control.

### Q19. What is Privileged Identity Management?

Privileged Identity Management, or PIM, provides just-in-time privileged access, approval workflows, time-bound role activation, and access reviews.

It reduces standing admin privileges and improves auditability.

### Q20. What are risky Azure identity misconfigurations?

Common issues include no MFA for admins, too many Owners, stale guest users, unused service principals, long-lived client secrets, broad app permissions, weak conditional access, and lack of PIM.

Identity review is one of the first Azure security tasks.

## Azure Logging And Monitoring

### Q21. What are Azure Activity Logs?

Azure Activity Logs record subscription-level management events, such as resource creation, deletion, and configuration changes.

They help answer who changed what in Azure resources.

### Q22. What are Microsoft Entra ID logs?

Entra ID logs include sign-in logs, audit logs, provisioning logs, and risky user/sign-in information depending on licensing.

They are essential for investigating identity attacks, MFA prompts, suspicious logins, and application consent abuse.

### Q23. What is Azure Monitor?

Azure Monitor collects metrics, logs, and telemetry from Azure resources and applications. It supports alerts, dashboards, and analysis.

Security teams use Azure Monitor with Log Analytics and Sentinel for detection and investigation.

### Q24. What is Log Analytics workspace?

A Log Analytics workspace stores and queries logs using Kusto Query Language. It is commonly used with Azure Monitor and Microsoft Sentinel.

Security relevance: centralize logs and control access because logs may contain sensitive information.

### Q25. What is Microsoft Sentinel?

Microsoft Sentinel is Microsoft's cloud-native SIEM and SOAR platform. It collects security logs, detects threats, correlates alerts, and supports automated response through playbooks.

Sentinel is used for SOC monitoring and incident response.

### Q26. What is Microsoft Defender for Cloud?

Microsoft Defender for Cloud is a cloud-native application protection platform that helps improve security posture and protect workloads across Azure, hybrid, and multicloud environments.

It provides recommendations, secure score, workload protections, regulatory compliance views, and security alerts depending on enabled plans.

### Q27. What is Azure secure score?

Secure score measures security posture based on Defender for Cloud recommendations. It helps prioritize improvements.

Secure score is useful, but teams should still prioritize based on business risk and asset criticality.

### Q28. What are diagnostic settings?

Diagnostic settings route Azure resource logs and metrics to destinations like Log Analytics, Storage Account, or Event Hub.

Security best practice: enable diagnostic logs for critical resources such as Key Vault, Storage, SQL, App Gateway, and AKS.

### Q29. What Azure events should be monitored?

Monitor privileged role assignment, service principal credential creation, failed and risky sign-ins, Key Vault secret access, public storage changes, NSG changes, policy exemptions, resource deletion, and Defender alerts.

These can indicate compromise or misconfiguration.

### Q30. What is KQL?

KQL means Kusto Query Language. It is used to query logs in Log Analytics, Sentinel, and other Microsoft security platforms.

For Azure security roles, basic KQL knowledge is valuable for investigation.

## Azure Network Security

### Q31. What is Azure VNet?

Azure Virtual Network is an isolated network in Azure. It contains subnets and resources, and can connect to other networks through peering, VPN, or ExpressRoute.

Security design includes segmentation, private endpoints, NSGs, route tables, and firewall controls.

### Q32. What is NSG?

Network Security Group controls inbound and outbound traffic for Azure subnets or network interfaces using rules.

Common risk: allowing RDP, SSH, databases, or admin services from the internet.

### Q33. What is Application Security Group?

Application Security Groups let you group VMs logically and use those groups in NSG rules instead of individual IPs.

They simplify network security management for application tiers.

### Q34. What is Azure Firewall?

Azure Firewall is a managed network security service for filtering network, application, and outbound traffic. It supports rules, threat intelligence, and centralized control.

Use it for network-level segmentation, egress filtering, and controlled internet access.

### Q35. What is Azure Web Application Firewall?

Azure WAF protects web applications from common HTTP attacks. It can run with Application Gateway, Front Door, or CDN depending on architecture.

WAF is defense in depth and should not replace secure coding.

### Q36. What is Azure Front Door?

Azure Front Door is a global edge service for routing, acceleration, TLS termination, and WAF protection for web applications.

Security checks include WAF policy, custom domains, TLS configuration, origin protection, and caching behavior.

### Q37. What is Private Endpoint?

Private Endpoint provides private IP access to Azure PaaS services through a VNet. It reduces public exposure for services like Storage, Key Vault, SQL, and Container Registry.

Security benefit: sensitive services can be accessed privately instead of through public endpoints.

### Q38. What is Service Endpoint?

Service Endpoint extends VNet identity to Azure services and allows access restrictions to selected VNets. Private Endpoint is often preferred for stronger private connectivity because it provides private IP access.

Know both because interviewers may ask the difference.

### Q39. What is DDoS Protection?

Azure DDoS Protection helps protect public resources against distributed denial-of-service attacks. Basic platform protection exists, and paid plans provide enhanced features.

DDoS protection supports availability.

### Q40. How do you secure RDP/SSH in Azure?

Avoid exposing RDP/SSH publicly. Use Azure Bastion, VPN, Just-in-Time VM access through Defender for Cloud, restricted NSG rules, strong authentication, and monitoring.

Public RDP is one of the most common cloud risks.

## Azure Storage And Secrets

### Q41. What is Azure Storage Account?

Azure Storage Account provides storage services such as Blob, File, Queue, and Table storage. Blob Storage is commonly used for object storage.

Security focus: public access, access keys, SAS tokens, network restrictions, encryption, logging, private endpoints, and RBAC.

### Q42. How do you secure Azure Blob Storage?

Disable anonymous access unless required, use Azure RBAC or managed identities, restrict network access, use private endpoints, avoid broad SAS tokens, enable logging, enable encryption, and review Defender for Cloud recommendations.

For sensitive data, block public access and monitor access patterns.

### Q43. What is anonymous blob access?

Anonymous blob access allows public read access to containers or blobs. It is risky if enabled unintentionally.

Disable it at account level unless the storage account is intentionally public.

### Q44. What is Shared Access Signature?

SAS is a token that grants limited access to Azure Storage resources for a defined time and permissions. It can be useful but risky if too broad or long-lived.

Best practices: short expiry, least permissions, HTTPS only, stored access policies, and avoid sharing account-level SAS unnecessarily.

### Q45. What are Azure Storage account keys?

Storage account keys provide broad access to a storage account. They are powerful and should be protected carefully.

Prefer Azure AD/RBAC and managed identities where possible. Rotate keys and monitor usage.

### Q46. What is Azure Key Vault?

Azure Key Vault stores and manages secrets, keys, and certificates. It integrates with Azure RBAC, managed identities, logging, soft delete, and purge protection.

Use Key Vault instead of storing secrets in code, app settings, container images, or pipelines.

### Q47. What is Key Vault soft delete?

Soft delete allows recovery of deleted vaults or objects during a retention period. It helps prevent accidental or malicious deletion.

Purge protection prevents permanent deletion before retention expires and is important for critical secrets and keys.

### Q48. What is Key Vault access model?

Key Vault can use Azure RBAC or vault access policies depending on configuration and design. Azure RBAC provides centralized IAM-style access management.

Security review checks who can read secrets, manage keys, delete vaults, or change access.

### Q49. What is Azure Disk Encryption?

Azure Disk Encryption protects VM disks using encryption. Azure managed disks also support server-side encryption by default, with options for customer-managed keys.

Security focus includes encryption settings, key access, and VM access control.

### Q50. What is Azure SQL security baseline?

Azure SQL security includes firewall/private endpoints, Microsoft Entra authentication where appropriate, least-privilege database roles, encryption, auditing, threat detection, vulnerability assessment, and secure connection strings.

Avoid public database exposure and hardcoded database credentials.

## Azure Compute, Containers, And DevOps

### Q51. What is Azure VM security baseline?

Baseline includes patched OS, Defender for Servers, disk encryption, restricted RDP/SSH, managed identity, least-privilege RBAC, logging, vulnerability management, endpoint protection, and no secrets in custom scripts.

Use images and policies to standardize hardening.

### Q52. What is Azure App Service security?

App Service security includes managed identity, Key Vault references, HTTPS-only, access restrictions, private endpoints, authentication integration, secure app settings, logging, and dependency scanning.

Do not store secrets directly in source code. Use Key Vault references where possible.

### Q53. What are Azure Functions security concerns?

Azure Functions need secure triggers, input validation, managed identity, Key Vault secrets, least-privilege permissions, dependency scanning, logging, and timeout limits.

Serverless still needs secure code and IAM controls.

### Q54. What is AKS security?

AKS security includes Entra ID integration, Kubernetes RBAC, Azure RBAC if used, managed identities, network policies, private clusters, image scanning, admission control, secrets management, logging, and cluster upgrades.

AKS combines Kubernetes security with Azure identity and networking.

### Q55. What is Azure Container Registry security?

ACR security includes private registry access, RBAC, managed identities, image scanning through Defender for Cloud, content trust/signing options, private endpoints, and network restrictions.

Avoid public or overly broad pull/push permissions.

### Q56. What is Azure DevOps?

Azure DevOps provides repos, pipelines, artifacts, boards, and test plans. It is commonly used for CI/CD.

Security focus: service connections, variable groups, secret variables, environment approvals, branch policies, pipeline permissions, and integration with Key Vault.

### Q57. What is Azure DevOps service connection?

A service connection lets Azure Pipelines connect to external services such as Azure subscriptions, Kubernetes clusters, container registries, or cloud providers.

It is sensitive because it may grant deployment access. Use least privilege, approvals, and workload identity/federated credentials where possible.

### Q58. How do you protect secrets in Azure Pipelines?

Use secret variables, variable groups with restricted access, Key Vault integration, avoid echoing secrets, limit pipeline permissions, use environments and approvals, and rotate secrets.

Prefer managed identity or federated credentials over long-lived service principal secrets.

### Q59. What are branch policies in Azure Repos?

Branch policies enforce PR reviews, build validation, status checks, linked work items, and comment resolution before merge.

Security benefit: prevents unreviewed changes from reaching protected branches.

### Q60. How do you add DevSecOps checks in Azure Pipelines?

Add SAST, SCA, secret scanning, IaC scanning, container scanning, artifact publishing, approvals, and deployment gates. Integrate with Defender for Cloud, Key Vault, and Azure Policy where applicable.

Use fast checks on PRs and deeper scans in staging or scheduled workflows.

## Azure Incident Scenarios

### Q61. A service principal secret is leaked. What do you do?

Disable or rotate the secret immediately. Review sign-in logs and activity logs for service principal usage, check role assignments, investigate resource changes, and assess impact.

Then migrate to managed identity or federated credentials where possible and add secret scanning.

### Q62. A Storage Account is public. What do you do?

Confirm whether public access is intended. If not, disable anonymous access, restrict network access, review exposed data, check logs, assess compliance impact, and enable Defender/Policy controls to prevent recurrence.

If SAS tokens were exposed, revoke or rotate affected keys/tokens.

### Q63. An admin account has no MFA. What do you do?

Enable MFA through Conditional Access or security defaults, review privileged role assignments, use PIM for just-in-time access, and monitor risky sign-ins.

Admin accounts without MFA are high risk.

### Q64. Defender for Cloud reports public RDP. What do you do?

Restrict NSG rules, enable Just-in-Time access or Azure Bastion, check login logs for suspicious access, identify who made the change, and enforce Azure Policy.

Public RDP should generally be avoided.

### Q65. Key Vault secret was accessed by unexpected identity. What do you do?

Review Key Vault diagnostic logs, identify the identity and source, check RBAC/access policies, rotate affected secret if suspicious, and investigate downstream usage.

Key Vault access should be tightly controlled and logged.

### Q66. Sentinel alerts on impossible travel. What do you do?

Validate user activity, check sign-in logs, device, MFA, IP reputation, and actions after login. If suspicious, revoke sessions, reset password, require MFA, and investigate related activity.

Identity alerts often require quick containment.

### Q67. Azure Policy is in audit mode only. Is that enough?

Audit mode gives visibility but does not prevent risky resources. For mature DevSecOps, high-risk policies should use deny or deploy-if-not-exists where appropriate.

Start with audit to measure impact, then move critical guardrails to enforcement.

### Q68. Azure App Service has FTP enabled. Is that risky?

FTP can expose credentials and deployment surface. Prefer disabling FTP or using FTPS only if needed, and use secure deployment methods through pipelines.

Also review publishing credentials and access restrictions.

### Q69. A pipeline has Contributor on subscription. What do you do?

Reduce permissions to the specific resource group or resources needed. Use least-privilege custom role if needed, environment approvals, and federated credentials.

Contributor at subscription scope gives broad ability to modify resources.

### Q70. AKS cluster exposes Kubernetes API publicly. What do you check?

Check authorized IP ranges, private cluster settings, Entra/RBAC integration, audit logs, admin credentials, network exposure, and whether any suspicious API activity occurred.

Public API endpoints are not always forbidden, but access must be tightly restricted.

## Azure Rapid Fire

### Q71. Entra ID or Azure RBAC?

Entra ID manages identities. Azure RBAC grants those identities permissions to Azure resources.

### Q72. Managed identity or service principal secret?

Prefer managed identity for Azure resources because it avoids storing credentials. Use service principals carefully for external automation.

### Q73. Defender for Cloud or Sentinel?

Defender for Cloud provides posture management and workload protection. Sentinel is SIEM/SOAR for log correlation and incident response. They integrate well.

### Q74. NSG or Azure Firewall?

NSG filters traffic at subnet/NIC level. Azure Firewall provides centralized managed firewall capabilities and egress control.

### Q75. Key Vault key or secret?

Keys are cryptographic keys used for encryption/signing. Secrets are sensitive values like passwords and tokens. Certificates manage certificate material.

### Q76. Private Endpoint or public endpoint with firewall?

Private Endpoint is stronger for sensitive services because traffic uses private IP in a VNet. Public endpoint with firewall can be acceptable for lower-risk or specific use cases.

### Q77. Owner or Contributor?

Owner can manage access and resources. Contributor can manage resources but not assign access. Owner is more sensitive.

### Q78. PIM or permanent admin?

PIM is preferred because it provides just-in-time, time-limited privileged access with approval and audit.

### Q79. Azure Policy or manual review?

Use both. Azure Policy enforces or audits at scale. Manual review handles design and exceptions.

### Q80. Azure security mindset?

Secure identity first, enforce least privilege, use policy guardrails, restrict public exposure, protect secrets, centralize logs, monitor with Defender/Sentinel, and automate checks in CI/CD.

