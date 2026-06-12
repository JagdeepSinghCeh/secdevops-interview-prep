# Linux And Network Security Deep Q&A

This file covers the Linux, networking, DNS, HTTP/TLS, firewall, proxy, and troubleshooting knowledge expected from a two-year DevSecOps, AppSec, or CloudSec candidate.

## Linux Fundamentals

### Q1. Why is Linux important for DevSecOps?

Linux runs a large portion of cloud servers, containers, CI/CD runners, Kubernetes nodes, security tools, and network services. DevSecOps engineers regularly inspect permissions, processes, logs, services, packages, and network connections.

You do not need to be a full Linux administrator, but you should diagnose security and deployment problems confidently.

### Q2. What is the Linux kernel?

The kernel is the core of the operating system. It manages processes, memory, devices, networking, filesystems, and security boundaries.

Containers share the host kernel, which is why kernel vulnerabilities and runtime restrictions matter to container security.

### Q3. What is a Linux distribution?

A Linux distribution combines the kernel with packages, tools, configuration, and a package manager. Examples include Ubuntu, Debian, Red Hat Enterprise Linux, Amazon Linux, Alpine, and Rocky Linux.

Security patch commands and package names vary by distribution.

### Q4. What is a shell?

A shell interprets commands and scripts. Bash, sh, zsh, and PowerShell are examples.

DevSecOps uses shell scripts for builds, deployments, tests, and automation. Unsafe handling of variables can cause command injection or secret exposure.

### Q5. What is a process?

A process is a running program with a process ID, owner, memory, open files, and environment. Processes inherit permissions from their user and capabilities.

Security teams inspect processes to find suspicious programs, shells, miners, or unexpected child processes.

### Q6. What is a daemon?

A daemon is a background service, such as SSH, nginx, Docker, or a monitoring agent. Modern systems often manage daemons through systemd.

Unnecessary daemons increase attack surface and should be disabled.

### Q7. What is systemd?

systemd manages services, startup, logs, and system state on many Linux distributions. Commands such as `systemctl` inspect and manage services, while `journalctl` reads journal logs.

Security reviews check service users, environment files, restart behavior, and executable paths.

### Q8. What is root?

Root is the superuser with unrestricted system privileges. Compromise of root generally means full host compromise.

Applications and CI jobs should not run as root unless required.

### Q9. What is sudo?

`sudo` allows authorized users to execute commands with elevated privileges. Rules are defined in sudoers configuration.

Review users and commands allowed through sudo. Broad passwordless sudo may create privilege-escalation risk.

### Q10. What is a service account in Linux?

A service account is a dedicated non-human user used to run an application or daemon. It should have no interactive login unless required and only necessary filesystem permissions.

Dedicated accounts reduce the impact of application compromise.

## Linux Permissions

### Q11. Explain Linux file permissions.

Linux permissions define read, write, and execute access for owner, group, and others. A mode such as `rwxr-x---` gives full access to owner, read/execute to group, and none to others.

Correct permissions protect secrets, binaries, configuration, and logs.

### Q12. What do numeric permissions mean?

Read is 4, write is 2, and execute is 1. The values are added for owner, group, and others. For example, `640` means owner read/write, group read, and others no access.

Avoid overly broad modes such as `777`.

### Q13. What does chmod do?

`chmod` changes permissions. Examples include restricting a private key to the owner or making a script executable.

Changes should follow least privilege.

### Q14. What does chown do?

`chown` changes file owner and group. Correct ownership prevents unauthorized modification or reading.

Application files should normally be owned by controlled deployment or service identities.

### Q15. What is the setuid bit?

Setuid makes an executable run with the file owner's privileges, often root. It is sometimes required by system utilities but can enable privilege escalation if the binary is vulnerable.

Review unexpected setuid binaries and minimize their use.

### Q16. What is the setgid bit?

Setgid on an executable runs it with the file group's privileges. On a directory, new files may inherit the directory group.

It can support shared directories but needs careful permission design.

### Q17. What is the sticky bit?

The sticky bit on a shared directory allows users to delete only their own files, even if the directory is writable by many users. `/tmp` commonly uses it.

Without it, users may interfere with each other's files.

### Q18. What is umask?

Umask defines default permissions removed when new files and directories are created.

A secure umask prevents new files from becoming world-readable or writable.

### Q19. Why is `777` dangerous?

It grants read, write, and execute permissions to everyone. Attackers or unrelated users may modify files, replace scripts, or access data.

Fix the ownership and exact required permissions instead of using `777` to solve errors.

### Q20. How do you protect private keys on Linux?

Store them outside shared directories, restrict owner permissions, avoid committing them to Git, use a secrets manager where possible, and rotate if exposed.

SSH commonly rejects private keys with overly broad permissions.

## Users, Authentication, And SSH

### Q21. Where are Linux users stored?

Basic account information is stored in `/etc/passwd`; password hashes and aging details are generally stored in `/etc/shadow`, readable only by privileged users.

The password hash file must be protected because offline cracking is possible if stolen.

### Q22. What is PAM?

PAM, Pluggable Authentication Modules, provides a framework for Linux authentication, account rules, password policy, and session controls.

PAM configuration errors can weaken or break authentication.

### Q23. How do you harden SSH?

Use key-based authentication, disable direct root login, restrict allowed users/groups, limit source networks, disable obsolete algorithms, use MFA or bastion access where appropriate, and monitor login logs.

Cloud environments can use SSM Session Manager, Azure Bastion, or GCP IAP to reduce direct SSH exposure.

### Q24. Password authentication or SSH keys?

SSH keys are generally stronger when private keys are protected and rotated. Passwords are more exposed to brute force and reuse.

Hardware-backed keys and short-lived SSH certificates can improve security further.

### Q25. What is authorized_keys?

`authorized_keys` lists public SSH keys allowed to log into an account. Attackers may add keys for persistence after compromise.

Monitor changes and manage keys centrally.

### Q26. What is an SSH bastion host?

A bastion host is a hardened entry point used to access private systems. It reduces the number of publicly exposed administration endpoints.

It should use MFA, strict logging, patching, and source restrictions.

### Q27. What are failed login logs?

Authentication logs record successful and failed logins, sudo use, and account events. Locations vary by distribution, such as `/var/log/auth.log` or `/var/log/secure`.

Repeated failures or logins from unusual sources may indicate attack.

### Q28. What is account lockout?

Account lockout temporarily blocks authentication after repeated failures. It slows brute force but can be abused for denial of service.

Use sensible thresholds, MFA, and monitoring.

### Q29. What is password aging?

Password aging controls expiration and minimum/maximum password lifetime. Modern guidance often emphasizes long unique passwords, breached-password checks, and MFA over frequent arbitrary rotation.

Privileged or service credential rotation still matters.

### Q30. What is a non-login service account?

It is configured with a shell such as `/usr/sbin/nologin` so it cannot open an interactive session.

Applications should use non-login accounts unless interactive access is required.

## Processes, Services, And Logs

### Q31. How do you inspect running processes?

Tools include `ps`, `top`, `htop`, `pstree`, and system monitoring platforms. Review process owner, command, parent process, CPU/memory, and start time.

Unexpected shells, miners, or processes launched by web servers can indicate compromise.

### Q32. What are environment variable risks?

Environment variables may contain secrets and can leak through process inspection, debug output, crash reports, or logs.

Use secret managers and limit process/debug access.

### Q33. What is a zombie process?

A zombie process has completed but its parent has not collected the exit status. It does not execute code but consumes a process table entry.

Many zombies may indicate an application or process-management issue, not usually a direct security incident.

### Q34. What is cron?

Cron schedules recurring commands. It is used for maintenance, backups, and automation.

Attackers may create cron jobs for persistence. Review system and user cron entries during incident response.

### Q35. What is a systemd timer?

A systemd timer schedules service execution similarly to cron but integrates with systemd.

Unexpected timers can also provide persistence.

### Q36. What should application logs contain?

Logs should include timestamp, event type, request or trace identifier, user/service identity, action, result, and relevant source context.

They should not contain passwords, complete tokens, private keys, or unnecessary personal data.

### Q37. What is log rotation?

Log rotation archives, compresses, and removes old logs based on size or time. It prevents disks from filling while retaining evidence.

Retention should satisfy operational, security, and compliance needs.

### Q38. What is auditd?

Linux Audit provides detailed security auditing of system calls, file access, identity changes, and privileged actions.

It can monitor sensitive files, sudo activity, and policy changes.

### Q39. Why protect logs?

Attackers may delete or modify local logs to hide activity. Centralized, access-controlled, and immutable logging improves investigation reliability.

Forward logs to SIEM or a dedicated logging account.

### Q40. What does "no space left on device" mean for security?

Full disks can stop logging, crash services, prevent updates, and create denial of service. Attackers may intentionally generate files or logs to exhaust storage.

Monitor capacity and enforce quotas/rotation.

## Package And Host Security

### Q41. What is patch management?

Patch management identifies, tests, deploys, and verifies security updates for operating systems, packages, runtimes, applications, and firmware.

Prioritize internet-facing and actively exploited vulnerabilities.

### Q42. What is package repository security?

Package managers download software from repositories. Use trusted signed repositories, validate keys, restrict sources, and avoid unverified scripts.

Compromised repositories or mirrors can affect the software supply chain.

### Q43. What is host hardening?

Host hardening reduces attack surface by disabling unnecessary services, applying patches, restricting access, configuring firewalls, protecting logs, enforcing least privilege, and following secure baselines.

CIS Benchmarks are commonly used hardening references.

### Q44. What is CIS Benchmark?

CIS Benchmarks provide configuration recommendations for operating systems, cloud services, databases, containers, and other technologies.

Organizations should test recommendations because some may affect functionality.

### Q45. What is EDR on Linux?

Endpoint Detection and Response monitors processes, files, network activity, and suspicious behavior on Linux hosts.

Examples include Microsoft Defender for Endpoint, CrowdStrike, SentinelOne, and other agents.

### Q46. What is file-integrity monitoring?

File-integrity monitoring detects changes to critical files, binaries, configuration, and web content.

It helps identify tampering, persistence, or unauthorized deployment.

### Q47. What is secure boot?

Secure Boot verifies trusted boot components before execution. It helps prevent unauthorized bootloaders or low-level malware.

Cloud providers may offer secure or shielded VM features.

### Q48. What is SELinux?

SELinux enforces mandatory access-control policies that restrict what processes may access even when normal Unix permissions allow it.

It reduces impact from compromised services but requires correct policy.

### Q49. What is AppArmor?

AppArmor applies profiles that restrict program access to files, capabilities, and operations.

It is used by some Linux distributions and container environments.

### Q50. What is a kernel vulnerability?

A kernel vulnerability affects the operating-system core and may enable privilege escalation, information leakage, or container escape.

Patch hosts and reduce workload privileges to limit impact.

## Networking Fundamentals

### Q51. What is the OSI model?

The OSI model describes networking in seven layers: physical, data link, network, transport, session, presentation, and application.

Interviewers mainly expect you to connect IP to layer 3, TCP/UDP to layer 4, and HTTP/DNS/TLS-related application behavior to upper layers.

### Q52. What is the TCP/IP model?

The TCP/IP model commonly uses link, internet, transport, and application layers. It reflects practical internet networking.

Security controls exist at each layer.

### Q53. What is a subnet?

A subnet divides an IP network into smaller ranges. It supports routing, segmentation, and address management.

Cloud security uses public/private subnets to control exposure.

### Q54. What is CIDR?

CIDR represents an IP network and prefix length. For example, `10.0.0.0/24` contains 256 IPv4 addresses.

`0.0.0.0/0` represents all IPv4 addresses and is dangerous for sensitive ports.

### Q55. What is a default gateway?

A default gateway routes traffic to destinations outside the local network.

Incorrect routes can expose or break network paths.

### Q56. What is routing?

Routing determines how packets move between networks. Route tables map destination ranges to gateways, interfaces, or next hops.

Cloud route-table review helps determine actual internet or internal reachability.

### Q57. What is NAT?

Network Address Translation maps one address to another. Cloud NAT allows private workloads to initiate outbound internet connections without direct inbound exposure.

NAT is not a complete egress-security control.

### Q58. What is a firewall?

A firewall allows or denies traffic based on source, destination, port, protocol, state, or application rules.

Use network firewalls with application controls and IAM.

### Q59. Stateful vs stateless firewall?

A stateful firewall tracks connection state and automatically allows return traffic. A stateless firewall evaluates each packet independently.

AWS Security Groups are stateful; AWS NACLs are stateless.

### Q60. What is network segmentation?

Segmentation separates systems into controlled zones to reduce exposure and lateral movement. Examples include separate web, application, database, management, and production networks.

Allow only required communication between segments.

## DNS, HTTP, And TLS

### Q61. How does DNS resolution work?

A client queries a resolver, which may use cache or ask root, top-level-domain, and authoritative DNS servers to find an address record.

Security concerns include spoofing, hijacking, stale records, takeover, and data exfiltration through DNS.

### Q62. What is an A record?

An A record maps a hostname to an IPv4 address. An AAAA record maps to IPv6.

Review records for unintended public exposure.

### Q63. What is a CNAME record?

A CNAME aliases one hostname to another. Stale CNAME records pointing to unclaimed cloud services can create subdomain takeover risk.

Remove unused DNS records promptly.

### Q64. What is DNS TTL?

TTL controls how long DNS responses are cached. Lower TTL can speed changes but increases query volume; higher TTL slows failover or incident changes.

Plan TTL before migrations.

### Q65. What is DNSSEC?

DNSSEC adds signatures to DNS data so resolvers can validate authenticity and integrity.

It helps prevent some DNS spoofing but does not encrypt DNS queries.

### Q66. What is HTTP request smuggling conceptually?

Request smuggling occurs when front-end and back-end systems disagree about HTTP request boundaries. An attacker may cause one request to be interpreted as multiple requests.

Prevention includes consistent HTTP parsing, patched proxies/servers, and rejecting ambiguous requests.

### Q67. What is HTTP Host header risk?

Applications may trust the Host header when generating reset links, redirects, or routing. If unvalidated, this can enable poisoned links or routing issues.

Use configured trusted hosts rather than arbitrary request headers.

### Q68. What is TLS termination?

TLS termination decrypts HTTPS at a load balancer, reverse proxy, or gateway. Traffic may then continue encrypted or plaintext to backends.

For sensitive environments, protect the internal path and consider end-to-end TLS.

### Q69. What is a certificate chain?

A certificate chain links the server certificate to intermediate and trusted root certificate authorities.

Clients validate the chain, hostname, validity period, and signature.

### Q70. What is HSTS?

HTTP Strict Transport Security tells browsers to use HTTPS for a domain for a defined period. It reduces protocol downgrade and accidental HTTP use.

Deploy carefully, especially with subdomain and preload options.

## Network Security Scenarios

### Q71. A server listens on port 8080. Is it vulnerable?

An open port is not automatically vulnerable. Determine the service, intended exposure, authentication, TLS, patch level, and network restrictions.

Unnecessary exposure should be removed.

### Q72. A database is in a private subnet. Is it secure?

Private placement reduces internet exposure but does not guarantee security. Review security groups, routing, IAM, credentials, encryption, patching, and application authorization.

Private networks can still be compromised.

### Q73. DNS points to a deleted cloud resource. What is the risk?

The hostname may be vulnerable to subdomain takeover if an attacker can claim the external resource.

Remove the stale DNS record or reclaim the service.

### Q74. Logs show outbound DNS requests to unusual random domains. What might it indicate?

It may indicate malware command-and-control, DNS tunneling, misconfigured software, or security testing.

Correlate process, host, user, and network activity before concluding.

### Q75. A container process runs as root on a patched host. Is it fine?

Patching helps but root still increases impact from application compromise, dangerous mounts, or runtime vulnerabilities.

Use non-root and runtime restrictions where possible.

### Q76. SSH key is found in a home directory with world-readable permissions. What do you do?

Restrict permissions immediately, determine whether unauthorized users accessed it, rotate the key if exposure is possible, and review where it is authorized.

Move toward centrally managed or short-lived credentials.

### Q77. An application log contains full Authorization headers. What do you do?

Stop logging them, rotate or revoke exposed tokens where needed, restrict log access, redact historical logs according to policy, and add logging tests.

Tokens are credentials.

### Q78. A security group allows all outbound traffic. Is that always wrong?

It is common but may be too broad for sensitive workloads. Consider egress allowlists, proxies, private endpoints, and DNS controls based on risk.

Egress restrictions reduce data-exfiltration and SSRF impact.

### Q79. How do you troubleshoot "site is not reachable" securely?

Check DNS, route, firewall/security group, load balancer, service health, listening port, TLS, application logs, and recent changes. Avoid temporarily opening all ports or disabling security controls without approval.

Troubleshooting should preserve least privilege.

### Q80. How do you summarize Linux and network security?

Secure the identity, process, file, service, package, log, and network layers. Apply least privilege, hardening, patching, segmentation, restricted administration, secure DNS/TLS, centralized logging, and continuous monitoring.

DevSecOps connects these controls to automated images, IaC, and deployment pipelines.

