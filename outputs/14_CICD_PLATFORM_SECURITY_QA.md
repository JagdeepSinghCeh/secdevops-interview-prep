# CI/CD Platform Security Deep Q&A

This file prepares you for CI/CD platform security questions across Jenkins, GitHub Actions, GitLab CI/CD, Azure DevOps, and general pipeline design. CI/CD security is central to DevSecOps because pipelines can access source code, secrets, artifacts, registries, cloud accounts, and production deployments.

## CI/CD Fundamentals

### Q1. What is CI/CD?

CI/CD means Continuous Integration and Continuous Delivery or Continuous Deployment. Continuous Integration means developers merge changes frequently and automated builds/tests run on those changes. Continuous Delivery means software is always deployable but may require approval. Continuous Deployment means successful changes can automatically deploy to production.

From a DevSecOps perspective, CI/CD is where security controls become repeatable: SAST, SCA, secret scanning, IaC scanning, container scanning, artifact signing, approvals, and deployment monitoring.

### Q2. Why is CI/CD security important?

CI/CD systems are high-value targets because they often hold source code, credentials, artifacts, deployment permissions, and access to production environments. If compromised, attackers may steal secrets, modify code, poison builds, publish malicious artifacts, or deploy backdoors.

Securing CI/CD protects the software supply chain.

### Q3. What are common CI/CD security risks?

Common risks include leaked secrets, over-privileged tokens, untrusted third-party actions, weak branch protection, insecure runners, public artifacts, dependency confusion, command injection in workflow scripts, missing approvals, and broad cloud deployment roles.

Good security requires least privilege, isolation, logging, review, and automated scanning.

### Q4. What are typical secure pipeline stages?

A secure pipeline usually includes checkout, dependency install, unit tests, SAST, SCA, secret scanning, IaC scanning, build, container scan, SBOM generation, artifact signing, staging deployment, DAST/API tests, approval, production deployment, and runtime monitoring.

Fast checks should run early; deeper checks can run in staging or scheduled jobs.

### Q5. What is a pipeline security gate?

A security gate is a policy decision point that blocks, warns, or requires approval based on security results. Examples include blocking leaked secrets, critical vulnerabilities, public storage resources, unsigned images, or high-risk IaC changes.

Gates should be risk-based and tuned to avoid excessive false positives.

### Q6. What should block a pipeline immediately?

Leaked production secrets, confirmed critical exploitable vulnerabilities, public exposure of sensitive resources, malicious dependency indicators, untrusted artifact signatures, deployment from unprotected branches, and unauthorized production changes should usually block.

Policies vary, but critical high-confidence risk should not silently pass.

### Q7. What should create a ticket instead of blocking?

Lower severity vulnerabilities, informational findings, non-exploitable issues, duplicate findings, or medium-risk hardening gaps may create tickets instead of blocking urgent releases.

This depends on business context, environment, exposure, and remediation SLA.

### Q8. What is least privilege in CI/CD?

Least privilege means pipeline jobs, tokens, runners, and service accounts get only the permissions needed for their task. Build jobs should not automatically have production deployment permissions.

Separate permissions by repository, branch, environment, and stage.

### Q9. What are pipeline secrets?

Pipeline secrets are sensitive values used by CI/CD, such as cloud credentials, registry tokens, SSH keys, API keys, signing keys, and deployment credentials.

They should be stored in secure secret stores, masked in logs, restricted to protected branches/environments, rotated, and replaced with short-lived credentials where possible.

### Q10. What is OIDC federation in CI/CD?

OIDC federation lets a CI/CD platform obtain short-lived cloud credentials from a cloud provider using trusted identity claims instead of storing long-lived static keys.

Examples include GitHub Actions to AWS IAM role, Azure federated credentials, and GCP Workload Identity Federation. This is a strong DevSecOps practice.

## Jenkins Security

### Q11. What is Jenkins?

Jenkins is an automation server widely used for CI/CD. It supports jobs, pipelines, plugins, credentials, agents, and integrations with source control and deployment systems.

Security focus: plugin management, RBAC, credentials, agent isolation, script approval, CSRF protection, audit logs, and least-privilege deployment access.

### Q12. What are common Jenkins security risks?

Common risks include outdated vulnerable plugins, admin access sprawl, secrets in console logs, unsafe pipeline scripts, over-privileged agents, exposed Jenkins UI, weak authentication, and credentials accessible to too many jobs.

Jenkins requires active hardening because it is highly extensible and often deeply connected to production.

### Q13. How do you secure Jenkins access?

Use SSO or strong authentication, RBAC, least privilege, restrict admin users, enable CSRF protection, enforce HTTPS, restrict network access, and monitor audit logs.

Jenkins should not be publicly exposed without strong controls.

### Q14. How do you secure Jenkins plugins?

Install only required plugins, keep them updated, remove unused plugins, monitor plugin advisories, and avoid abandoned plugins.

Plugins run inside Jenkins and can become a major attack path.

### Q15. What is Jenkins Credentials plugin?

Jenkins Credentials plugin stores secrets such as usernames/passwords, tokens, SSH keys, and certificates. Jobs can use credentials through controlled bindings.

Security concern: credentials must be scoped properly and not exposed to jobs or users unnecessarily.

### Q16. How do you prevent Jenkins secrets in logs?

Use credential binding, masking, avoid echoing variables, avoid shell debug modes like `set -x` with secrets, review scripts, and restrict console log access.

If a secret appears in logs, rotate it.

### Q17. What is Jenkins agent security?

Agents run build jobs. If an attacker controls a job on an agent, they may access workspace files, credentials, network resources, or other builds.

Use ephemeral agents, isolate trusted/untrusted jobs, restrict network access, and avoid sharing sensitive workspaces.

### Q18. What is Jenkins pipeline as code?

Jenkins pipeline as code uses a `Jenkinsfile` stored in source control to define pipeline stages. It improves review and version control.

Security review should include shell commands, credential usage, artifact handling, and deployment permissions.

### Q19. What is Jenkins script approval?

Script approval controls execution of certain Groovy scripts or methods in Jenkins pipelines. It helps prevent unsafe script execution.

Admins should review approvals carefully because approved scripts may access Jenkins internals or secrets.

### Q20. How would you add DevSecOps to Jenkins?

Add stages for secret scanning, SAST, SCA, IaC scanning, Docker build, image scanning, SBOM generation, artifact signing, staging DAST, and production approval.

Also harden Jenkins itself with RBAC, plugin patching, credential scoping, and isolated agents.

## GitHub Actions Security

### Q21. What is GitHub Actions?

GitHub Actions is GitHub's CI/CD automation platform. Workflows are defined in YAML and triggered by events such as push, pull request, schedule, release, or manual dispatch.

Security focus: workflow permissions, third-party actions, secrets, environments, branch protection, OIDC, and pull request events.

### Q22. What is `GITHUB_TOKEN`?

`GITHUB_TOKEN` is an automatically generated token available to workflows. It can access repository resources depending on configured permissions.

Best practice: set explicit least-privilege permissions in workflow or repository settings, such as read-only contents unless write access is needed.

### Q23. Why are GitHub Actions permissions important?

If a workflow token has broad write permissions, compromised workflow code or dependency can modify code, releases, packages, or pull requests.

Set `permissions:` explicitly and grant only needed scopes.

### Q24. What is the risk of third-party GitHub Actions?

Third-party actions execute code in your pipeline. If compromised or malicious, they can access workflow environment, repository token, and sometimes secrets.

Pin actions to full commit SHA, use trusted publishers, review source, and restrict token/secrets.

### Q25. Why should actions be pinned?

Pinning prevents unexpected changes. Tags can move or be compromised. A commit SHA gives stronger immutability.

Dependabot can help update pinned actions through review.

### Q26. What is `pull_request_target` risk?

`pull_request_target` runs in the context of the base repository and may access secrets or write tokens. If used unsafely with untrusted pull request code, attackers can exfiltrate secrets or modify repo content.

Avoid checking out and running untrusted PR code with privileged context.

### Q27. How do GitHub environments improve security?

Environments can enforce deployment approvals, wait timers, protected branches, environment secrets, and deployment history.

Use environments for staging and production gates.

### Q28. How do you use OIDC from GitHub Actions to AWS?

Create an AWS IAM OIDC provider and role trust policy that allows GitHub claims for specific repository, branch, or environment. In workflow, request an OIDC token and assume the AWS role.

This avoids storing long-lived AWS access keys in GitHub secrets.

### Q29. How do you protect GitHub Actions secrets?

Store secrets at appropriate scope, restrict environments, avoid exposing secrets to forked PRs, mask logs, rotate secrets, and prefer OIDC for cloud credentials.

If a workflow prints a secret, rotate it immediately.

### Q30. How do you secure GitHub branch protection?

Require pull request reviews, status checks, code owner review, signed commits if needed, no force pushes, no direct pushes to main, and restrict who can dismiss reviews.

Branch protection ensures security checks cannot be skipped easily.

## GitLab CI/CD Security

### Q31. What is GitLab CI/CD?

GitLab CI/CD is GitLab's automation system using `.gitlab-ci.yml` to define stages and jobs. It supports runners, variables, environments, artifacts, and security scanning.

Security focus: protected branches/tags, protected variables, runner isolation, job tokens, artifacts, and deployment approvals.

### Q32. What are GitLab runners?

Runners execute GitLab CI jobs. They can be shared, group-level, project-level, shell, Docker, Kubernetes, or other executor types.

Security concern: untrusted jobs on shared runners may expose workspace or environment if runners are not isolated properly.

### Q33. What are protected variables in GitLab?

Protected variables are only available to pipelines running on protected branches or tags.

Use them for production secrets so untrusted branches cannot access them.

### Q34. What are masked variables?

Masked variables are hidden in job logs if printed. They help reduce accidental secret exposure.

Masking is helpful but not perfect. Avoid printing secrets and rotate if exposed.

### Q35. What is GitLab CI_JOB_TOKEN?

`CI_JOB_TOKEN` is a token available to CI jobs for interacting with GitLab APIs and resources. Its access depends on project settings and features.

Restrict token access and be careful with cross-project permissions.

### Q36. What is GitLab environment protection?

Protected environments restrict who can deploy to specific environments such as production.

Use approvals and protected environments for production deployment control.

### Q37. What are GitLab artifacts security risks?

Artifacts may contain build outputs, logs, test reports, secrets, or plan files. If accessible too broadly, sensitive data can leak.

Set expiration, restrict access, avoid storing secrets, and scan artifacts when needed.

### Q38. How do you add DevSecOps checks in GitLab?

Add SAST, DAST, dependency scanning, container scanning, secret detection, IaC scanning, license scanning, and deployment approvals. GitLab has built-in templates for many security scans depending on tier.

Also tune policies and triage findings.

### Q39. How do you secure GitLab deployments to cloud?

Use short-lived credentials or cloud federation where available, protected variables, protected environments, least-privilege service accounts, and audit logs.

Avoid storing broad cloud keys as unprotected variables.

### Q40. What is a dangerous GitLab CI pattern?

Running untrusted branch code with production secrets or deployment permissions is dangerous. Another risky pattern is using privileged Docker runners broadly.

Separate build/test jobs from deployment jobs and protect secrets.

## Azure DevOps Security

### Q41. What is Azure DevOps?

Azure DevOps provides Repos, Pipelines, Artifacts, Boards, and Test Plans. It is commonly used for enterprise CI/CD, especially with Azure.

Security focus: service connections, pipeline permissions, variable groups, secret variables, environments, approvals, branch policies, and Key Vault integration.

### Q42. What is Azure DevOps service connection?

A service connection lets pipelines authenticate to external systems like Azure subscriptions, Kubernetes clusters, Docker registries, or cloud services.

It is sensitive because it may grant deployment access. Use least privilege and restrict which pipelines can use it.

### Q43. How do you secure Azure service connections?

Use workload identity federation or managed identity where possible, least-privilege roles, restricted pipeline access, approvals, and regular review.

Avoid broad Contributor at subscription scope unless justified.

### Q44. What are Azure DevOps variable groups?

Variable groups store variables shared across pipelines. They can contain secrets and can integrate with Azure Key Vault.

Restrict access and approvals because variable groups can expose sensitive values to pipelines.

### Q45. What are Azure Pipeline environments?

Environments represent deployment targets and can enforce approvals, checks, and traceability.

Use environments to protect staging and production deployments.

### Q46. What are branch policies in Azure Repos?

Branch policies enforce PR reviews, build validation, required reviewers, linked work items, and comment resolution.

They support secure code review and prevent direct unreviewed changes to protected branches.

### Q47. How do you protect secrets in Azure Pipelines?

Use secret variables, Key Vault integration, variable group permissions, avoid printing secrets, restrict pipeline access, and use federated credentials or managed identity.

Rotate secrets if exposed in logs or artifacts.

### Q48. What is a risky Azure Pipeline permission?

A pipeline with broad service connection access to a production subscription can deploy or modify many resources. If the pipeline is compromised, production is at risk.

Limit permissions by environment, resource group, and task.

### Q49. How do you integrate Key Vault with Azure Pipelines?

Create a service connection or identity with permission to read required secrets from Key Vault, link the variable group to Key Vault, and restrict pipeline access.

Key Vault access should be logged and least privilege.

### Q50. How do you add security gates in Azure DevOps?

Use branch policies, build validations, pipeline tasks for SAST/SCA/secrets/IaC/container scanning, environment approvals, Defender for Cloud integration, and manual checks for production.

Security gates should be documented and measurable.

## Secure Pipeline Design

### Q51. How do you design a pipeline for a Dockerized web app?

Run secret scan, SAST, SCA, unit tests, Dockerfile lint, build image, scan image, generate SBOM, sign image, push to private registry, deploy to staging, run DAST/API tests, require approval, then deploy to production.

Also monitor runtime logs and cloud security findings.

### Q52. How do you design a pipeline for Terraform?

Run `fmt`, `validate`, IaC scan, policy checks, generate plan, review plan, require approval for production, apply with least-privilege short-lived credentials, and monitor drift.

Protect state and plan artifacts.

### Q53. How do you design a pipeline for Kubernetes?

Scan manifests/Helm charts, scan images, verify signatures, enforce approved registries, validate RBAC and NetworkPolicies, deploy to staging, run tests, then deploy with admission control and approvals.

Use GitOps if appropriate.

### Q54. What is separation of duties in CI/CD?

Separation of duties means no single person or process can make high-risk changes without review or approval. Developers may write code, reviewers approve, pipelines build, and deployment to production may require environment approval.

This reduces insider risk and accidental changes.

### Q55. What is build artifact security?

Build artifact security protects packages, binaries, images, and release files from tampering. Controls include private artifact repositories, checksums, signing, provenance, access control, and retention.

Artifacts should be traceable to source commit and build pipeline.

### Q56. What is a deployment approval?

A deployment approval requires authorized users or checks before deploying to an environment. It is common for production.

Approvals should not be rubber stamps; they should verify change risk, scan results, and rollback readiness.

### Q57. What is rollback security?

Rollback returns to a previous known-good version. Security relevance: rollback images/artifacts should also be trusted, signed, and free from known critical vulnerabilities.

Do not roll back to a vulnerable version unless risk is accepted temporarily.

### Q58. What is pipeline audit logging?

Pipeline audit logging records workflow runs, approvals, secret access, deployments, permission changes, and configuration updates.

Logs support incident response and compliance.

### Q59. How do you prevent secrets from reaching CI logs?

Use secret masking, avoid echoing commands with secrets, avoid verbose debug output, review scripts, use secure credential bindings, and restrict log access.

If logs contain secrets, rotate them.

### Q60. How do you handle untrusted pull requests?

Run untrusted PRs with read-only tokens, no secrets, isolated runners, and limited permissions. Do not deploy from untrusted PRs. Be careful with workflow events that run in privileged contexts.

This is critical for open-source repositories.

## Supply Chain Security

### Q61. What is software supply-chain security?

Software supply-chain security protects everything used to build and deliver software: source code, dependencies, build systems, CI/CD, artifacts, registries, deployment credentials, and runtime environments.

Supply-chain attacks target trust relationships rather than only application bugs.

### Q62. What is dependency confusion?

Dependency confusion is when build tools download malicious public packages instead of intended private packages with the same name.

Controls include private registries, scoped packages, dependency pinning, lockfiles, and package source restrictions.

### Q63. What is typosquatting?

Typosquatting is publishing packages with names similar to popular packages to trick developers into installing them.

Controls include package review, lockfiles, approved registries, and SCA.

### Q64. What is artifact signing?

Artifact signing cryptographically signs build outputs so consumers can verify authenticity and integrity.

Tools include Cosign, Sigstore, GPG signing, and platform-native signing.

### Q65. What is SLSA?

SLSA is a framework for improving software supply-chain integrity. It focuses on source, build, provenance, and tamper resistance.

It helps organizations mature from basic build hygiene to verifiable secure builds.

### Q66. What is in-toto?

in-toto is a framework for securing software supply chains by recording and verifying steps in the build and release process.

It helps ensure expected steps were performed by authorized systems.

### Q67. What is provenance?

Provenance is metadata describing where an artifact came from, including source repo, commit, builder, workflow, and build parameters.

It helps verify that production artifacts came from trusted pipelines.

### Q68. What is SBOM in CI/CD?

SBOM is generated during build to list software components and versions. It helps vulnerability response when new CVEs are announced.

Store SBOMs with artifacts and connect them to vulnerability management.

### Q69. What is a trusted builder?

A trusted builder is a controlled build environment with restricted access, auditable configuration, isolated execution, and verified outputs.

Trusted builders reduce risk of artifact tampering.

### Q70. How do you secure release tags?

Protect release branches and tags, require reviews, restrict who can create tags, sign tags if needed, and ensure release workflows use trusted commits.

Release tags often trigger production builds or deployments.

## CI/CD Scenario Questions

### Q71. A pipeline printed AWS keys in logs. What do you do?

Rotate the keys immediately, restrict log access, check CloudTrail for key usage, remove the logging cause, and add secret scanning/masking. Treat exposed keys as compromised.

Do not only delete the log and move on.

### Q72. A GitHub Action from a third party was compromised. What do you do?

Identify workflows using it, stop affected workflows, rotate potentially exposed secrets, review logs and artifacts, pin or replace the action, and assess whether malicious code ran.

Long-term controls include pinning actions, least-privilege tokens, and trusted action allowlists.

### Q73. Jenkins admin account is compromised. What do you do?

Disable the account, rotate Jenkins credentials, review job changes, audit plugin/config changes, inspect build logs, check deployments, and verify whether artifacts were tampered with.

Jenkins compromise can become production compromise.

### Q74. A GitLab runner is shared by trusted and untrusted projects. What is the risk?

Untrusted jobs may attempt to access runner workspace, cached data, network resources, or exploit runner configuration. Shared runners need strong isolation.

Use separate runners for sensitive projects and protected jobs.

### Q75. A production deployment happened without approval. What do you check?

Review pipeline logs, environment settings, branch protection, who triggered deployment, service connection permissions, and whether approval checks were bypassed or missing.

Fix process controls and investigate the deployed change.

### Q76. A CI/CD service account has cloud admin. What do you do?

Reduce permissions to least privilege, split roles by environment, use short-lived credentials, add approvals, and monitor usage.

Admin access in CI is high blast radius.

### Q77. A pipeline artifact contains `.env`. What do you do?

Treat secrets as exposed if real. Rotate them, remove artifact, restrict access, fix packaging rules, add `.dockerignore`/artifact ignore patterns, and add secret scanning.

Artifacts are often overlooked secret leak sources.

### Q78. A scanner blocks every build and developers complain. What do you do?

Review false positives, tune thresholds, group duplicates, start with high-confidence critical gates, provide remediation guidance, and create SLA-based tickets for lower risks.

A security gate must be trusted to be effective.

### Q79. A forked PR tries to change workflow YAML. What is the risk?

Workflow changes can alter pipeline behavior, exfiltrate tokens, or bypass checks. Review workflow changes carefully and avoid giving forked PRs secrets or write tokens.

CODEOWNERS can require platform/security review for workflow files.

### Q80. How do you summarize CI/CD security?

CI/CD security protects source, workflows, runners, secrets, artifacts, deployments, and permissions. Use branch protection, least privilege, secret management, scanning, artifact signing, approvals, audit logs, and runtime monitoring.

The best answer connects pipeline controls to supply-chain risk.

