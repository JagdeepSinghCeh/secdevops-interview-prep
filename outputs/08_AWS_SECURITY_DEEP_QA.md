# AWS Security Deep Q&A

This file focuses on AWS security interview preparation for DevSecOps, CloudSec, and AppSec roles. Learn the pattern behind every service: what it does, why it matters, common misconfigurations, logs to check, and how it fits into DevSecOps.

## AWS Fundamentals

### Q1. What is AWS from a security point of view?

AWS is a cloud platform where customers create compute, storage, network, identity, database, container, serverless, and security resources. From a security point of view, AWS is powerful because infrastructure can be created quickly through console, CLI, SDK, or IaC. That speed is useful, but it also means misconfigurations can happen quickly.

AWS security focuses on IAM, logging, encryption, network exposure, data protection, workload hardening, vulnerability management, and continuous monitoring. A DevSecOps engineer should know how to prevent insecure AWS resources through IaC scanning and detect deployed risks through AWS-native services and CSPM tools.

### Q2. What is the AWS shared responsibility model?

The AWS shared responsibility model separates what AWS secures and what the customer secures. AWS is responsible for security of the cloud, such as physical data centers, hardware, and foundational infrastructure. The customer is responsible for security in the cloud, such as IAM permissions, data, application code, network rules, logging, encryption choices, and resource configuration.

The exact split depends on service type. With EC2, the customer manages OS patching and hardening. With S3, AWS manages infrastructure, but the customer manages bucket permissions and data access. With Lambda, AWS manages more runtime infrastructure, but the customer still manages code, IAM, dependencies, and secrets.

### Q3. What is an AWS account?

An AWS account is a container for AWS resources, identities, billing, and security boundaries. Organizations often use multiple accounts for production, staging, development, security, logging, and shared services.

Security benefit: account separation reduces blast radius. If a development account is compromised, proper separation helps prevent direct production impact.

### Q4. What is AWS Organizations?

AWS Organizations helps centrally manage multiple AWS accounts. It supports organizational units, consolidated billing, and Service Control Policies.

Security teams use Organizations to apply guardrails, separate environments, centralize logging, and manage accounts at scale.

### Q5. What is an SCP?

SCP means Service Control Policy. It defines maximum permissions for accounts or organizational units in AWS Organizations. SCPs do not grant permissions by themselves; they limit what IAM can allow.

Example: an SCP can deny disabling CloudTrail, deny leaving the organization, deny public S3 bucket changes, or restrict regions. SCPs are strong preventive guardrails.

### Q6. What is AWS Control Tower?

AWS Control Tower helps set up and govern a multi-account AWS environment using landing zone patterns. It provides guardrails, account vending, and baseline governance.

For interviews, mention Control Tower when discussing secure AWS account structure, centralized logging, preventive controls, and scalable governance.

### Q7. What is an AWS landing zone?

An AWS landing zone is a secure, multi-account baseline environment for workloads. It usually includes account structure, identity integration, logging, networking, guardrails, and security services.

A landing zone helps prevent teams from creating insecure cloud environments from scratch.

### Q8. What is AWS region security relevance?

AWS regions are geographic locations where resources are deployed. Security relevance includes data residency, compliance, latency, disaster recovery, and attack surface.

Organizations may restrict allowed regions using SCPs because unused regions can become blind spots for attackers.

### Q9. What is an Availability Zone?

An Availability Zone is an isolated data center or group of data centers inside a region. Deploying across multiple AZs improves availability.

Availability is part of security because systems must remain accessible. Multi-AZ design reduces downtime from failures.

### Q10. What is tagging in AWS security?

Tags are key-value metadata assigned to resources. Security teams use tags for ownership, environment, data classification, cost center, and compliance.

Tags help vulnerability management and incident response because you can identify who owns a resource and how critical it is.

## AWS IAM

### Q11. What is AWS IAM?

AWS IAM, Identity and Access Management, controls access to AWS services and resources. It includes users, groups, roles, policies, permissions, and identity federation.

IAM is one of the most important AWS security areas because compromised or over-permissive identities can control cloud resources. Good IAM follows least privilege, MFA, short-lived credentials, and regular access review.

### Q12. What is an IAM user?

An IAM user is a long-term identity inside an AWS account. It can have console password, access keys, or both.

Best practice is to avoid long-lived IAM users where possible, especially for human access. Prefer federation through IAM Identity Center or an external identity provider. If access keys are required, rotate and monitor them.

### Q13. What is an IAM role?

An IAM role is an identity with permissions that can be assumed by trusted entities such as AWS services, users, applications, or external identities. Roles provide temporary credentials.

Roles are preferred for workloads because they avoid hardcoded long-lived access keys. EC2, Lambda, ECS tasks, and EKS workloads commonly use roles.

### Q14. What is an IAM policy?

An IAM policy is a JSON document that allows or denies actions on AWS resources under certain conditions. Policies can be identity-based, resource-based, permissions boundaries, session policies, or SCPs.

Security review checks for wildcards, admin access, overly broad resources, missing conditions, and risky actions such as `iam:PassRole`.

### Q15. What is least privilege in AWS IAM?

Least privilege means granting only the actions and resources required for a job. Avoid `AdministratorAccess`, `Action: "*"`, and `Resource: "*"` unless truly justified.

IAM least privilege is difficult but essential. Use access analyzer, CloudTrail activity, managed policies carefully, and periodic review to reduce permissions.

### Q16. What is an IAM trust policy?

A trust policy defines who can assume an IAM role. It is attached to roles and controls trusted principals such as AWS services, accounts, SAML providers, OIDC providers, or specific users.

Misconfigured trust policies can allow unintended role assumption, including cross-account access.

### Q17. What is `iam:PassRole` and why is it sensitive?

`iam:PassRole` allows a principal to pass an IAM role to an AWS service. It is sensitive because if a user can pass a powerful role to EC2, Lambda, or Glue, they may indirectly gain that role's permissions.

Restrict `iam:PassRole` to specific roles and services using conditions.

### Q18. What is an IAM permissions boundary?

A permissions boundary defines the maximum permissions an IAM user or role can have. It does not grant permissions by itself; it limits effective permissions.

Permissions boundaries are useful when delegating IAM creation to teams while preventing them from creating overly privileged roles.

### Q19. What is IAM Access Analyzer?

IAM Access Analyzer helps identify resources shared with external entities and can also help generate policies based on CloudTrail access activity. It is useful for finding unintended public or cross-account access.

Interview use case: review S3 buckets, KMS keys, IAM roles, Lambda functions, SQS queues, and other resource policies for external access.

### Q20. What is IAM credential report?

An IAM credential report lists IAM users and credential details such as password enabled, access key age, MFA status, and last use.

Security teams use it to find old access keys, users without MFA, unused users, and stale credentials.

### Q21. What is AWS IAM Identity Center?

AWS IAM Identity Center, formerly AWS SSO, provides centralized workforce access to AWS accounts and applications. It can integrate with external identity providers.

Security benefit: centralized access, temporary credentials, MFA integration, permission sets, and easier access review.

### Q22. How do you secure AWS root user?

Enable MFA, use a strong password, do not create root access keys, restrict root use to rare account-level tasks, monitor root login, and store credentials securely.

The root user has full account power and should not be used for daily operations.

### Q23. How do you review an IAM policy?

Check allowed actions, resources, conditions, principals, wildcards, admin permissions, privilege escalation paths, `iam:PassRole`, cross-account trust, and whether permissions match business need.

Also check CloudTrail usage to determine whether permissions are actually used.

### Q24. What are common AWS IAM misconfigurations?

Common issues include no MFA, long-lived access keys, unused users, wildcard permissions, public resource policies, broad trust policies, excessive `iam:PassRole`, admin roles for CI/CD, and lack of access review.

Prevent with IAM Identity Center, least privilege, Access Analyzer, credential reports, SCPs, and monitoring.

### Q25. A CI/CD pipeline needs AWS access. What is the secure way?

Use a dedicated deployment role with least privilege and short-lived credentials. Prefer OIDC federation from GitHub Actions, GitLab, or other CI systems instead of storing long-lived AWS keys.

Restrict role assumption by repository, branch, environment, and audience claims where supported. Add CloudTrail monitoring and deployment approvals.

## AWS Logging And Detection

### Q26. What is AWS CloudTrail?

CloudTrail records AWS API calls and account activity. It helps identify who did what, when, from where, and against which resource.

Best practices include enabling multi-region trails, protecting log buckets, enabling log file validation, sending logs to a central security account, and monitoring high-risk events.

### Q27. What are CloudTrail management events?

Management events record control-plane actions such as creating users, changing IAM policies, launching instances, creating buckets, or modifying security groups.

These events are essential for detecting account compromise, privilege escalation, and suspicious configuration changes.

### Q28. What are CloudTrail data events?

Data events record data-plane actions on supported resources, such as S3 object-level access or Lambda function invocation. They are high volume and may cost more, so they are often enabled selectively.

For sensitive S3 buckets, data events are valuable for investigating data access.

### Q29. What is CloudWatch?

CloudWatch collects metrics, logs, alarms, and events from AWS resources and applications. It helps monitor performance and operational health.

Security use cases include alarming on suspicious metrics, collecting application logs, monitoring failed login patterns, and triggering response workflows.

### Q30. What are VPC Flow Logs?

VPC Flow Logs capture metadata about network traffic to and from network interfaces in a VPC. They include source, destination, ports, protocol, action, and bytes, but not packet contents.

Security teams use them to investigate network exposure, suspicious connections, scanning, and data transfer patterns.

### Q31. What is Amazon GuardDuty?

Amazon GuardDuty is AWS threat detection service. It analyzes AWS data sources and logs to identify malicious activity and unauthorized behavior. It can detect account compromise, suspicious API calls, reconnaissance, instance compromise indicators, DNS-based threats, and container-related suspicious activity depending on enabled protections.

GuardDuty findings should feed incident response workflows through EventBridge, Security Hub, SIEM, or SOAR.

### Q32. What data sources does GuardDuty use?

GuardDuty uses sources such as CloudTrail events, VPC Flow Logs, DNS logs, EKS audit logs, runtime monitoring signals, S3 data events, and other protection-plan-specific sources depending on configuration.

The key interview point: GuardDuty is detective control, not a preventive IAM or network control.

### Q33. What is AWS Security Hub?

AWS Security Hub centralizes security findings from AWS services and partner tools. It also checks AWS accounts against security standards and best-practice controls.

It helps with visibility and prioritization, but findings still need ownership, triage, and remediation.

### Q34. What is AWS Config?

AWS Config records resource configuration history and evaluates resources against rules. It can detect drift and non-compliance, such as public S3 buckets, unencrypted volumes, or unrestricted security groups.

Config is useful for compliance and continuous monitoring.

### Q35. What is Amazon Inspector?

Amazon Inspector scans AWS workloads for software vulnerabilities and unintended network exposure. It supports services like EC2, container images in ECR, and Lambda depending on current AWS capabilities.

Inspector helps vulnerability management by continuously identifying affected resources and CVEs.

### Q36. What is Amazon Macie?

Amazon Macie helps discover and protect sensitive data in S3 using managed data identifiers and machine learning. It can identify data like personal information or credentials in buckets.

Use Macie when discussing sensitive data discovery, public bucket exposure, and data classification.

### Q37. What is Detective?

Amazon Detective helps investigate security findings by analyzing and visualizing activity from sources such as CloudTrail, VPC Flow Logs, and GuardDuty.

It supports incident investigation by showing relationships between accounts, users, IPs, and resources.

### Q38. What is EventBridge in security response?

Amazon EventBridge routes events from AWS services to targets like Lambda, SNS, SQS, Step Functions, or SIEM integrations. Security teams use it to automate response to GuardDuty, Security Hub, or Config findings.

Example: when GuardDuty reports a compromised instance, EventBridge can trigger a Lambda function to isolate the security group.

### Q39. What are high-risk CloudTrail events to monitor?

Monitor root login, console login failures, access key creation, policy changes, role assumption anomalies, CloudTrail deletion or stop logging, security group changes, public bucket policy changes, KMS key policy changes, and disabling GuardDuty or Security Hub.

These events may indicate compromise or defense evasion.

### Q40. What do you do if CloudTrail is disabled?

Treat it as serious because attackers may disable logs to hide activity. Re-enable CloudTrail, check who disabled it, review previous events, check for other defense evasion actions, and ensure SCPs or permissions prevent unauthorized disabling.

Centralized logging and SCP deny rules can reduce this risk.

## AWS Network Security

### Q41. What is a VPC?

A VPC is a logically isolated virtual network in AWS. It contains subnets, route tables, internet gateways, NAT gateways, security groups, NACLs, and network interfaces.

VPC design controls network segmentation, internet exposure, private access, and traffic flow.

### Q42. What is a public subnet?

A public subnet has a route to an internet gateway and can host resources with public IPs. It is typically used for load balancers, bastion hosts, or public-facing services.

Security point: databases and internal services should usually not be placed directly in public subnets.

### Q43. What is a private subnet?

A private subnet does not directly route inbound traffic from the internet. Resources may still access the internet through NAT for updates or outbound calls.

Security point: application servers, databases, and internal workloads are often placed in private subnets.

### Q44. What is an Internet Gateway?

An Internet Gateway allows communication between a VPC and the internet. Resources need proper routes and public IPs to use it.

Security review checks whether only intended public resources have internet reachability.

### Q45. What is a NAT Gateway?

A NAT Gateway allows resources in private subnets to initiate outbound internet connections without accepting inbound internet connections.

Security point: NAT reduces inbound exposure but does not fully control outbound destinations. Egress filtering may still be needed.

### Q46. What is a Security Group?

A Security Group is a stateful virtual firewall attached to resources. It controls inbound and outbound traffic.

Common misconfiguration: allowing `0.0.0.0/0` to SSH, RDP, databases, Elasticsearch/OpenSearch, Redis, or internal admin ports.

### Q47. What is a NACL?

A Network ACL is a stateless firewall at subnet level. It controls inbound and outbound traffic using ordered allow/deny rules.

Security Groups are usually primary workload firewalls; NACLs are useful for subnet-level guardrails.

### Q48. Difference between Security Group and NACL?

Security Groups are stateful and attached to resources. NACLs are stateless and attached to subnets. Security Groups support allow rules only, while NACLs support allow and deny rules.

In interviews, say Security Groups are commonly used for instance-level access control, while NACLs provide subnet-level filtering.

### Q49. How do you secure SSH access in AWS?

Avoid public SSH where possible. Use AWS Systems Manager Session Manager, VPN, bastion host, or restricted IP ranges. Disable password login, use key-based authentication, restrict security groups, monitor login attempts, and rotate keys.

For EC2, SSM Session Manager often reduces the need for inbound SSH entirely.

### Q50. What is AWS Systems Manager Session Manager?

Session Manager allows secure shell-like access to instances without opening inbound SSH ports. It uses IAM permissions and SSM agent.

Security benefit: no public SSH exposure, centralized access control, and session logging.

### Q51. What is VPC Endpoint?

A VPC Endpoint allows private connectivity from a VPC to supported AWS services without using the public internet. There are interface endpoints and gateway endpoints.

Security benefit: private access to services like S3, DynamoDB, Secrets Manager, or ECR, with endpoint policies and reduced internet exposure.

### Q52. What is AWS PrivateLink?

AWS PrivateLink provides private connectivity to services using interface endpoints, keeping traffic inside AWS network paths instead of public internet.

Use it for private service access, SaaS integrations, and reducing public exposure.

### Q53. What is AWS WAF?

AWS WAF is a web application firewall that helps filter HTTP/S traffic based on rules. It can block common web attacks, bad bots, malicious IPs, SQLi/XSS patterns, and custom conditions.

WAF is defense in depth. It can provide temporary mitigation but does not replace fixing vulnerable code.

### Q54. What is AWS Shield?

AWS Shield provides DDoS protection. Shield Standard is automatically included for AWS customers. Shield Advanced provides enhanced protections, visibility, and response support.

DDoS protection is part of availability security.

### Q55. What is CloudFront security relevance?

CloudFront is AWS CDN. It can terminate TLS, cache content, integrate with AWS WAF, hide origins, and improve availability.

Security checks include origin access control, cache behavior, signed URLs/cookies for private content, TLS policy, and avoiding sensitive response caching.

## AWS Storage And Data Security

### Q56. What is S3?

S3 is AWS object storage. It stores objects in buckets. It is widely used for files, logs, backups, static websites, data lakes, and application assets.

Security focus: public access, bucket policies, encryption, logging, versioning, lifecycle, object ownership, and sensitive data classification.

### Q57. How do you secure S3 buckets?

Enable Block Public Access unless public access is required. Use least-privilege bucket policies, disable or avoid ACLs, enable encryption, enable versioning for important data, enable logging or CloudTrail data events for sensitive buckets, and monitor with Access Analyzer, Config, Security Hub, and Macie.

Also avoid mixing public and private data in the same bucket.

### Q58. What is S3 Block Public Access?

S3 Block Public Access is a set of controls that blocks public access through ACLs or bucket policies at account or bucket level.

It is one of the most important protections against accidental public bucket exposure.

### Q59. What is an S3 bucket policy?

An S3 bucket policy is a resource-based policy that controls access to a bucket and objects. It can grant access to AWS principals, accounts, services, or public principals.

Security review checks for public principals, broad actions, missing conditions, and cross-account access.

### Q60. What are S3 ACLs?

ACLs are older S3 access control mechanisms. AWS generally recommends using bucket policies and IAM instead of ACLs where possible.

Modern S3 security often disables ACLs through Object Ownership settings.

### Q61. What is S3 pre-signed URL?

A pre-signed URL grants temporary access to an S3 object using the permissions of the signer. It is useful for controlled upload or download without making a bucket public.

Security checks include short expiry, limited permissions, HTTPS, object-level authorization before generation, and logging.

### Q62. What is S3 versioning?

S3 versioning keeps multiple versions of objects. It helps recover from accidental deletion, overwrite, or some ransomware scenarios.

For sensitive buckets, combine versioning with MFA delete or retention controls where appropriate.

### Q63. What is S3 Object Lock?

S3 Object Lock can enforce write-once-read-many retention for objects. It helps protect backups and logs from deletion or tampering.

Useful for compliance, ransomware resilience, and audit log protection.

### Q64. What is EBS encryption?

EBS encryption protects EC2 block volumes at rest. It uses AWS KMS keys.

Security checks include encryption enabled by default, key policies, snapshot sharing, and access to KMS keys.

### Q65. What is RDS encryption?

RDS encryption protects database storage, backups, snapshots, and replicas using KMS keys.

Security checks include encryption enabled, public accessibility disabled, strong auth, network restrictions, backups protected, and logging enabled.

### Q66. What is AWS KMS?

AWS KMS manages cryptographic keys used by AWS services and applications. It supports key policies, IAM integration, auditing through CloudTrail, rotation for supported keys, and envelope encryption.

Security review should focus on who can administer keys, who can use keys, and whether key policies allow external or overly broad access.

### Q67. What is envelope encryption?

Envelope encryption encrypts data with a data key, then encrypts the data key with a master key or KMS key. This is efficient and scalable.

AWS services commonly use envelope encryption behind the scenes.

### Q68. What is AWS Secrets Manager?

AWS Secrets Manager stores and manages secrets such as database credentials, API keys, and tokens. It supports rotation, access control, encryption, and audit logging.

Use it instead of storing secrets in code, environment files, container images, or CI logs.

### Q69. What is AWS Parameter Store?

Systems Manager Parameter Store stores configuration values and secrets. SecureString parameters can use KMS encryption.

Secrets Manager has richer secret rotation features, while Parameter Store is often used for configuration and simpler secret needs.

### Q70. What is AWS Certificate Manager?

AWS Certificate Manager provisions and manages TLS certificates for AWS services like load balancers and CloudFront.

Security benefit: easier TLS certificate lifecycle management and reduced certificate expiry risk.

## AWS Compute And Workload Security

### Q71. What is EC2 security baseline?

EC2 security baseline includes least-privilege IAM role, patched OS, hardened SSH access, restricted security groups, encrypted EBS, SSM agent, logging, EDR/runtime monitoring, vulnerability scanning, and no secrets stored on disk unnecessarily.

Use golden AMIs or image pipelines to standardize hardening.

### Q72. What is AMI security?

AMI means Amazon Machine Image. It defines the OS and software used to launch EC2 instances. Insecure AMIs can contain vulnerabilities, secrets, malware, or outdated packages.

Use trusted AMIs, patch regularly, scan images, and avoid sharing AMIs publicly unless intended.

### Q73. What is Lambda security?

Lambda security includes least-privilege execution role, secure environment variables, dependency scanning, input validation, logging, timeout limits, VPC configuration where needed, and avoiding secrets in code.

Serverless reduces server management but does not remove application and IAM responsibility.

### Q74. What are Lambda environment variable risks?

Environment variables can contain secrets. They may be visible to users with Lambda read permissions and can leak through logs or errors.

Use Secrets Manager or Parameter Store for sensitive values and restrict Lambda configuration access.

### Q75. What is ECS security?

ECS security includes task roles, image scanning, secrets management, network mode, security groups, least-privilege execution role, logging, and runtime monitoring.

Avoid giving tasks broad IAM permissions. Use separate task roles per service.

### Q76. What is EKS security?

EKS security includes Kubernetes RBAC, AWS IAM integration, IRSA or EKS Pod Identity, network policies, secrets encryption, image scanning, admission control, audit logs, cluster upgrades, and node hardening.

EKS requires both Kubernetes security and AWS security knowledge.

### Q77. What is IRSA?

IRSA means IAM Roles for Service Accounts. It allows Kubernetes service accounts in EKS to assume IAM roles using OIDC, giving pods scoped AWS permissions.

Security benefit: avoid giving broad permissions to node roles and assign least privilege per workload.

### Q78. What is EKS Pod Identity?

EKS Pod Identity is an AWS feature for assigning IAM permissions to Kubernetes pods more simply than older patterns. It helps workloads access AWS services with scoped credentials.

Interview point: the principle is workload-level IAM least privilege, whether using IRSA or newer pod identity mechanisms.

### Q79. What is ECR security?

ECR is Amazon Elastic Container Registry. Security checks include image scanning, repository permissions, lifecycle policies, encryption, private repositories, tag immutability, and pull access control.

Use image signing and admission controls for stronger supply-chain security.

### Q80. What is AWS Fargate security?

Fargate runs containers without managing servers. Security still includes task roles, image security, secrets, network rules, logging, and runtime configuration.

Fargate reduces host management but does not remove container or IAM risk.

## AWS DevSecOps And IaC

### Q81. How do you scan Terraform for AWS misconfigurations?

Use tools like Checkov, tfsec, Terrascan, KICS, or Snyk IaC in pull requests and CI pipelines. Scan for public S3, open security groups, disabled encryption, weak IAM, missing logging, and insecure EKS settings.

IaC scanning prevents cloud misconfigurations before deployment.

### Q82. How do you secure Terraform state in AWS?

Store state in a private encrypted S3 bucket with versioning, strict IAM, bucket policy, and DynamoDB state locking. Do not expose state publicly because Terraform state can contain sensitive values.

Also restrict who can read state and avoid putting secrets directly in Terraform variables.

### Q83. What is CloudFormation security?

CloudFormation is AWS IaC. Security concerns include overly permissive IAM, public resources, insecure parameters, secrets in templates, and stack drift.

Use cfn-nag, Checkov, CloudFormation Guard, and review changesets before deployment.

### Q84. What is cfn-nag?

cfn-nag scans CloudFormation templates for insecure patterns such as open security groups, permissive IAM policies, and missing encryption.

It is useful in CI/CD for AWS-native IaC security.

### Q85. What is CloudFormation Guard?

CloudFormation Guard is a policy-as-code tool for validating CloudFormation templates against rules.

Use it to enforce organization-specific requirements, such as mandatory encryption or restricted ports.

### Q86. What is AWS CDK security?

AWS CDK defines cloud infrastructure using programming languages. Security concerns are similar to CloudFormation because CDK synthesizes CloudFormation templates.

Scan synthesized templates, review IAM grants, avoid broad constructs, and use secure defaults.

### Q87. How do you prevent public S3 through DevSecOps?

Use secure Terraform modules, IaC scanning, code review, SCPs, S3 Block Public Access at account level, AWS Config rules, Security Hub controls, IAM Access Analyzer, and alerting.

This is layered prevention and detection.

### Q88. How do you prevent open SSH in AWS?

Use IaC scanning to block `0.0.0.0/0` on port 22, secure modules, code review, Config rules, Security Hub controls, SCPs where appropriate, and prefer SSM Session Manager.

If SSH is required, restrict source IPs and monitor access.

### Q89. How do you secure AWS deployment credentials in GitHub Actions?

Use GitHub OIDC to assume an AWS role with least privilege. Restrict trust policy to specific repo, branch, workflow, and environment claims. Avoid storing static AWS keys as GitHub secrets.

Monitor role assumptions in CloudTrail.

### Q90. What AWS services help DevSecOps automation?

CodePipeline, CodeBuild, CodeArtifact, ECR, Inspector, Security Hub, EventBridge, Lambda, Systems Manager, Secrets Manager, KMS, CloudFormation, and IAM Access Analyzer can support DevSecOps workflows.

Even if using GitHub/GitLab/Jenkins, AWS security services still monitor cloud resources.

## AWS Incident Scenarios

### Q91. An AWS access key is leaked. What do you do?

Immediately disable or rotate the access key. Identify the IAM user, review CloudTrail for actions performed with the key, check source IPs and regions, look for persistence such as new users/roles/keys, and assess data/resource impact.

Then remove the key from code/history, migrate to roles or secret manager, add secret scanning, and document the incident.

### Q92. A GuardDuty finding says credentials were used from an unusual location. What do you do?

Validate the finding, disable or rotate affected credentials, review CloudTrail activity, check IAM changes, inspect accessed resources, search for persistence, and notify stakeholders.

After containment, reduce permissions and add stronger identity controls.

### Q93. An S3 bucket is public. What do you do?

Determine whether public access is intended. If unintended, enable Block Public Access or update policy immediately. Identify exposed data, check logs if available, notify owners, assess compliance impact, and add preventive controls.

Do not just make it private and close the ticket without impact assessment.

### Q94. CloudTrail shows `CreateAccessKey` for an old IAM user. What do you do?

Check whether the action was authorized. Review who performed it, source IP, MFA status, and subsequent key usage. If suspicious, disable the key and user, investigate activity, and rotate affected secrets.

Old IAM users should usually be removed or migrated to federated access.

### Q95. Someone opened RDP to `0.0.0.0/0`. What do you do?

Restrict the security group immediately, confirm whether any instances were accessed, check logs, identify change source through CloudTrail, and open remediation or incident ticket depending on exposure time.

Prevent with IaC scanning, Config rules, Security Hub controls, and change approvals.

### Q96. A Lambda function has admin IAM permissions. What do you do?

Review what the function actually needs, reduce the execution role to least privilege, check CloudTrail for usage, and verify no secrets or dangerous environment variables exist.

Serverless functions should not have broad permissions because code execution becomes cloud control.

### Q97. An ECR image has critical vulnerabilities. What do you do?

Identify affected image tags, running workloads, exploitability, and fix versions. Rebuild with patched base images and dependencies, rescan, redeploy, and consider blocking vulnerable images through admission controls.

Also enforce image scanning in CI before push/deploy.

### Q98. An EC2 instance is suspected compromised. What do you do?

Isolate the instance by changing security groups or network controls, preserve evidence with snapshots, review GuardDuty/CloudTrail/VPC Flow Logs, identify entry point, rotate instance role credentials if needed, and rebuild from trusted image.

Avoid destroying evidence before investigation if incident response requires forensics.

### Q99. A KMS key policy allows external account access. What do you do?

Validate whether cross-account access is intended. If not, remove the external principal, review CloudTrail KMS usage, identify encrypted resources affected, and add Access Analyzer monitoring.

KMS key access can expose encrypted data if misconfigured.

### Q100. A developer stores database password in Lambda code. What do you do?

Rotate the password, remove it from code and Git history if committed, store it in Secrets Manager or Parameter Store, grant Lambda least-privilege access to read it, and add secret scanning.

Also check deployment packages and logs for exposure.

## AWS Rapid Fire

### Q101. CloudTrail or GuardDuty?

CloudTrail records AWS API activity. GuardDuty analyzes logs and signals to detect suspicious behavior. CloudTrail is raw audit evidence; GuardDuty is threat detection.

### Q102. Security Hub or Config?

Config records resource configuration and evaluates rules. Security Hub centralizes findings and standards checks from multiple sources. They often work together.

### Q103. KMS or Secrets Manager?

KMS manages encryption keys. Secrets Manager stores and rotates secrets, using KMS for encryption. They solve related but different problems.

### Q104. Security Group or WAF?

Security Group controls network ports and sources. WAF filters HTTP/S application traffic. Use both for different layers.

### Q105. IAM role or access key?

Prefer IAM roles with temporary credentials for AWS workloads and federation. Access keys are long-lived and higher risk.

### Q106. Public subnet means public access?

Not always. A subnet is public if it routes to an internet gateway, but a resource also needs public IP and security rules to be reachable. Always verify actual reachability.

### Q107. S3 bucket private means data safe?

Private access reduces exposure, but you still need IAM least privilege, encryption, logging, data classification, and application-level authorization.

### Q108. Can AWS WAF stop all attacks?

No. WAF helps block common patterns but cannot understand all business logic or authorization flaws. Secure code and testing are still required.

### Q109. Is encryption enough for compliance?

No. Compliance also needs access control, logging, monitoring, retention, incident response, policies, evidence, and governance.

### Q110. What is the best AWS security mindset?

Use least privilege, secure defaults, centralized logging, encryption, network segmentation, automated guardrails, continuous monitoring, and incident-ready processes.

