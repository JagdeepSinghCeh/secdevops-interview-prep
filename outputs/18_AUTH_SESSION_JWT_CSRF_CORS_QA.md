# Authentication, Session, JWT, CSRF, And CORS Deep Q&A

This file covers identity-related application security questions. Authentication proves identity, session management maintains that identity, and authorization controls access. Mistakes in any layer can lead to account takeover or data exposure.

## Authentication Fundamentals

### Q1. What is authentication?

Authentication verifies the identity of a user, service, or device. Methods include passwords, MFA, certificates, SSO, API keys, and workload identity.

Authentication answers "Who are you?" It does not answer what the identity may access.

### Q2. What is authorization?

Authorization decides what an authenticated identity may do. It checks roles, permissions, ownership, attributes, and business relationships.

Authentication without authorization can still expose every resource to every logged-in user.

### Q3. What is identification?

Identification is the act of claiming an identity, such as entering a username or email. Authentication then verifies that claim using a password, MFA, certificate, or another factor.

The distinction is useful in identity discussions.

### Q4. What is MFA?

MFA means Multi-Factor Authentication. It requires factors from different categories: something you know, have, or are.

MFA reduces password-compromise risk, especially for administrators, cloud accounts, source control, and production systems.

### Q5. What is broken authentication?

Broken authentication is a broad category where weaknesses in login, MFA, password reset, session handling, token validation, or account recovery allow impersonation or account takeover.

Testing must cover the entire identity lifecycle, not only the login form.

### Q6. What is credential stuffing?

Credential stuffing uses username/password pairs leaked from other services to attempt logins. It succeeds because users reuse passwords.

Controls include MFA, rate limiting, breached-password checks, bot detection, and suspicious-login monitoring.

### Q7. What is password spraying?

Password spraying tries a small set of common passwords against many accounts to avoid lockouts.

Controls include MFA, detection across multiple accounts, smart lockout, and strong password policy.

### Q8. What is brute force?

Brute force tries many possible passwords against one or more accounts. It may be online against a login page or offline against stolen password hashes.

Rate limiting helps online attacks. Strong password hashing helps offline attacks.

### Q9. What is username enumeration?

Username enumeration occurs when responses reveal whether an account exists through messages, status codes, timing, password reset behavior, or registration.

Use generic responses and consistent workflows while still logging detailed internal reasons.

### Q10. How do you test login security?

Check HTTPS, username enumeration, rate limiting, lockout behavior, MFA, password policy, session rotation, cookies, error handling, default credentials, and logging.

Use only authorized test accounts and avoid locking real users.

## Password Security

### Q11. How should passwords be stored?

Passwords should be salted and hashed using adaptive password-hashing algorithms such as Argon2, bcrypt, scrypt, or PBKDF2 with appropriate parameters.

They should never be stored in plaintext or reversible encryption for normal authentication.

### Q12. Why is SHA-256 alone unsuitable for password hashing?

SHA-256 is designed to be fast. Attackers can try billions of guesses efficiently using GPUs. Password hashing should intentionally be slow and memory-intensive.

Use Argon2id, bcrypt, scrypt, or PBKDF2 according to organizational standards.

### Q13. What is a salt?

A salt is a unique random value added to each password before hashing. It prevents identical passwords from producing identical hashes and defeats precomputed rainbow tables.

Modern password-hashing libraries manage salts automatically.

### Q14. What is a pepper?

A pepper is an additional secret value used with password hashing and stored separately from the password database, often in a secrets manager or HSM-backed service.

It can add protection if the database alone is stolen, but key management is required.

### Q15. What is a strong password policy?

A strong policy encourages long passwords or passphrases, blocks known breached passwords, prevents default passwords, supports password managers, and avoids unnecessary frequent rotation without evidence of compromise.

MFA and rate limiting are equally important.

### Q16. What is password reset risk?

Password reset flows can be weaker than login. Risks include predictable tokens, long expiry, reusable links, account enumeration, weak identity verification, and tokens exposed in URLs or logs.

Reset tokens should be random, short-lived, single-use, and invalidated after use.

### Q17. What should happen after password reset?

The application should invalidate the reset token, consider invalidating active sessions, notify the user, log the event, and require strong authentication.

High-risk systems may require reauthentication or MFA.

### Q18. What is default credential risk?

Default credentials are known usernames/passwords shipped with software or devices. Attackers routinely try them.

Applications and infrastructure should require changing defaults and disable unused accounts.

### Q19. What is account lockout risk?

Lockout slows brute force but can be abused to deny service by locking many users. Use smart lockout, rate limiting, risk-based controls, and MFA rather than permanent hard lockouts alone.

Monitor repeated failures across IPs and accounts.

### Q20. What is breached-password checking?

Breached-password checking compares chosen passwords against known compromised password lists without exposing the password. It prevents users from choosing credentials attackers already know.

It is stronger than complexity rules alone.

## Session Management

### Q21. What is a session?

A session maintains a user's authenticated state across requests. It may use a server-side session identifier stored in a cookie or token-based mechanisms.

Session security is critical because stealing a session may bypass the password and MFA.

### Q22. What is a session ID?

A session ID is an unpredictable value linking a client to server-side session data. It should contain sufficient randomness and no sensitive information.

It must be protected in transit, storage, logs, and browser context.

### Q23. What is session fixation?

Session fixation occurs when an attacker sets or predicts a session identifier before authentication, and the application continues using it after login.

Regenerate the session ID after authentication and privilege changes.

### Q24. What is session hijacking?

Session hijacking is unauthorized use of a valid session token. Tokens may be stolen through XSS, insecure transport, logs, malware, or browser storage.

Use HTTPS, secure cookies, token rotation, short expiry, and XSS prevention.

### Q25. What should logout do?

Logout should invalidate the server-side session or token where possible, clear cookies, and prevent reuse. Simply deleting a browser cookie without server invalidation may leave the session usable.

For high-risk apps, revoke refresh tokens and related sessions.

### Q26. What is idle timeout?

Idle timeout ends a session after a period without activity. It reduces exposure from unattended devices or stolen sessions.

The duration should match application sensitivity.

### Q27. What is absolute timeout?

Absolute timeout ends a session after a maximum lifetime regardless of activity. It prevents sessions from remaining valid indefinitely.

Use both idle and absolute limits for sensitive apps.

### Q28. What is concurrent session control?

Concurrent session control limits or monitors multiple active sessions for an account. Some systems show active devices and allow revocation.

It can help users and security teams detect account compromise.

### Q29. What are secure cookie attributes?

`Secure` sends cookies only over HTTPS. `HttpOnly` prevents JavaScript access. `SameSite` limits cross-site sending. `Path`, `Domain`, and expiry should also be scoped correctly.

Session cookies should normally use Secure and HttpOnly.

### Q30. What is SameSite?

SameSite controls when cookies are sent with cross-site requests. Values include Strict, Lax, and None. `SameSite=None` requires Secure in modern browsers.

It helps reduce CSRF but may not replace CSRF tokens for sensitive flows.

## JWT Security

### Q31. What is JWT?

JWT means JSON Web Token. It is a compact token format with header, payload, and signature. Claims may include subject, issuer, audience, expiry, and roles.

JWT payload is encoded, not encrypted by default. Sensitive information should not be placed in it casually.

### Q32. What are JWT header, payload, and signature?

The header identifies token type and signing algorithm. The payload contains claims. The signature protects integrity and authenticity.

The receiver must verify the signature and validate claims.

### Q33. Is JWT encrypted?

Usually no. Standard signed JWTs are readable by anyone who obtains them. JWE is a separate encrypted token standard.

Do not store passwords, secrets, or unnecessary personal data in JWT payloads.

### Q34. What is the `alg` claim?

The `alg` header indicates the signing algorithm. The server must enforce allowed algorithms rather than trusting arbitrary client choice.

Accepting `none` or algorithm confusion can break verification.

### Q35. What is JWT algorithm confusion?

Algorithm confusion occurs when verification logic accepts an unintended algorithm or misuses asymmetric keys as symmetric secrets.

Use mature libraries and configure the expected algorithm explicitly.

### Q36. What is `exp` claim?

`exp` defines token expiration time. The server must reject expired tokens.

Short access-token lifetime reduces risk from theft.

### Q37. What is `iss` claim?

`iss` identifies the token issuer. Applications should verify the expected issuer to avoid accepting tokens from an untrusted identity provider.

Issuer validation is required alongside signature verification.

### Q38. What is `aud` claim?

`aud` identifies intended recipients. An API should verify that it is an intended audience.

Without audience checks, a token issued for one service may be accepted by another.

### Q39. What is `sub` claim?

`sub` identifies the subject, usually a user or service. It should map to a trusted identity in the application.

Do not trust additional role claims without verification and authorization logic.

### Q40. What is JWT refresh token?

A refresh token obtains new access tokens without requiring login each time. Refresh tokens are usually longer-lived and therefore highly sensitive.

Use secure storage, rotation, revocation, reuse detection, and narrow scope.

### Q41. What is refresh-token rotation?

Rotation issues a new refresh token whenever the old one is used and invalidates the previous token. Reuse of an old token may indicate theft.

It reduces the lifetime of stolen refresh tokens.

### Q42. Where should JWTs be stored in browsers?

There is no universal answer. HttpOnly Secure cookies reduce JavaScript theft but require CSRF protections. Browser storage is easier for some architectures but is exposed to XSS.

Choose based on architecture and implement defense in depth.

### Q43. How do you test JWT validation safely?

Using a test token, check whether the application rejects expired tokens, altered payloads, wrong issuer, wrong audience, invalid signature, and tokens from another user or environment.

Do not attempt to impersonate real users.

### Q44. What is JWKS?

JWKS is a JSON Web Key Set containing public keys used to verify tokens. Identity providers publish JWKS endpoints for signature verification.

Applications must validate issuer, key selection, algorithm, and caching safely.

### Q45. What is `kid` risk?

`kid` identifies the signing key. Unsafe implementations may use it in file paths, database queries, or remote fetches without validation.

Use trusted key sets and mature libraries.

## OAuth And OIDC

### Q46. What is OAuth 2.0?

OAuth 2.0 is an authorization framework that allows a client to access resources on behalf of a user or itself using access tokens.

OAuth is not automatically authentication. OIDC adds an identity layer.

### Q47. What is OpenID Connect?

OIDC is an authentication layer built on OAuth 2.0. It introduces ID tokens and standardized identity claims.

It is commonly used for SSO and cloud identity federation.

### Q48. What is authorization code flow?

Authorization code flow redirects a user to an authorization server, returns a short-lived code, and exchanges the code for tokens through a secure back channel.

With PKCE, it is recommended for public clients such as mobile and single-page applications.

### Q49. What is PKCE?

PKCE protects authorization code flow from code interception. The client creates a verifier and challenge, then proves possession during token exchange.

It is important for clients that cannot safely store a client secret.

### Q50. What is redirect URI validation?

The authorization server must allow only registered redirect URIs. Weak matching or open redirects can send authorization codes or tokens to attacker-controlled locations.

Use exact matching where possible.

### Q51. What is OAuth state parameter?

`state` links the authorization response to the initiating browser session and helps prevent CSRF and login confusion.

It should be unpredictable, session-bound, and validated.

### Q52. What is OAuth scope?

Scope defines the access requested by a client, such as reading profile or writing files.

Use least privilege and avoid granting broad scopes unnecessarily.

### Q53. What is consent phishing?

Consent phishing tricks users or admins into granting a malicious application access to cloud or SaaS data through OAuth permissions.

Controls include app consent policies, publisher verification, admin review, and monitoring.

### Q54. What is token introspection?

Token introspection lets a resource server ask an authorization server whether a token is active and retrieve metadata.

It is commonly used with opaque tokens.

### Q55. What is token revocation?

Token revocation invalidates refresh or access tokens before natural expiry. It is important after logout, compromise, password reset, or account disabling.

Systems need a practical revocation strategy.

## CSRF

### Q56. What is CSRF?

CSRF means Cross-Site Request Forgery. It tricks a victim's browser into sending an unwanted request to a site where the victim is authenticated.

It commonly affects applications that rely on automatically sent cookies.

### Q57. What conditions are needed for CSRF?

The victim must be authenticated, the browser must automatically attach credentials, the attacker must be able to cause the request, and the application must lack strong request-intent validation.

The targeted action must also have meaningful impact.

### Q58. What is a CSRF token?

A CSRF token is an unpredictable, session-bound value included in legitimate state-changing requests. The server verifies it before processing.

Tokens should not be placed in cross-site-accessible locations.

### Q59. How does SameSite help against CSRF?

SameSite limits when cookies are attached to cross-site requests. Strict and Lax reduce many CSRF attacks.

Sensitive apps should still evaluate CSRF tokens and origin validation.

### Q60. Do bearer-token APIs have CSRF risk?

If the token is stored outside cookies and manually added to the Authorization header, classic CSRF risk is lower because cross-site forms cannot automatically attach it.

XSS and token storage remain major concerns.

### Q61. How do you test CSRF safely?

Use a test account and a harmless reversible action. Determine whether a cross-site request can perform the action without a valid token or origin check.

Avoid testing destructive or real-user actions.

### Q62. What is login CSRF?

Login CSRF causes a victim to log into an account controlled by the attacker. The victim may then enter sensitive data into the attacker's account.

Login endpoints also need CSRF protection or state binding.

### Q63. Are GET requests safe from CSRF?

GET requests should not change state. If they do, they may be exploitable through links, images, or browser navigation.

Use POST/PUT/PATCH/DELETE for state changes and still apply CSRF defenses.

### Q64. What is Origin header validation?

The server checks whether the request's `Origin` matches trusted application origins. It is a useful CSRF defense for modern browsers.

Use it as defense in depth with tokens and SameSite.

### Q65. What is Referer validation?

Referer can help determine request origin but may be absent or affected by privacy policies. It can be a fallback signal, not the only control.

Security decisions should not depend solely on it.

## CORS

### Q66. What is CORS?

CORS means Cross-Origin Resource Sharing. It is a browser mechanism that controls whether JavaScript from one origin may read responses from another origin.

CORS is not a general server-to-server access control.

### Q67. What is an origin?

An origin is the combination of scheme, host, and port. `https://example.com` and `http://example.com` are different origins, as are different ports or subdomains.

CORS policies operate on origins.

### Q68. What is `Access-Control-Allow-Origin`?

This response header identifies which origin may read the response through browser JavaScript.

The server should allow only trusted origins for sensitive responses.

### Q69. What is `Access-Control-Allow-Credentials`?

This header tells the browser whether credentials such as cookies may be included in cross-origin requests.

Credentialed CORS needs strict origin allowlisting.

### Q70. Why is reflecting arbitrary Origin dangerous?

If the server copies any supplied Origin into `Access-Control-Allow-Origin` and allows credentials, a malicious site may read sensitive authenticated responses.

Validate origins against a strict allowlist.

### Q71. Can wildcard origin be used with credentials?

Browsers do not allow `Access-Control-Allow-Origin: *` with credentialed requests. Some insecure servers instead reflect arbitrary origins, which creates risk.

Do not use broad origin trust for sensitive APIs.

### Q72. What is CORS preflight?

The browser sends an `OPTIONS` request before certain cross-origin requests to ask whether the method and headers are allowed.

Preflight is not authentication. The actual endpoint still needs authorization.

### Q73. Is CORS a server security boundary?

No. CORS is enforced by browsers. curl, mobile apps, and backend services are not restricted by browser CORS.

Always enforce authentication and authorization server-side.

### Q74. How do you test CORS safely?

Send requests with controlled Origin values and inspect CORS response headers. Verify whether untrusted origins can read sensitive responses with credentials using a test account.

Do not collect real-user data.

### Q75. What is `null` origin risk?

Some contexts produce `Origin: null`, such as sandboxed documents or local files. Trusting null broadly can expose responses to untrusted contexts.

Allow it only with a specific justified design.

## Identity Scenarios

### Q76. Login has rate limiting by IP only. What is missing?

Attackers can distribute attempts across many IPs or target one account. Add account-based and risk-based controls, MFA, breached-password detection, and monitoring.

Rate limiting should use multiple signals.

### Q77. JWT signature is valid but audience is not checked. What is the risk?

A token issued for another service may be accepted by this API. This can cross security boundaries.

Validate signature, issuer, audience, expiry, and intended token type.

### Q78. Logout only deletes localStorage token. Is that enough?

The token may remain valid until expiry and can be reused if stolen. Revoke refresh tokens or sessions where possible and use short-lived access tokens.

Client-side deletion alone is incomplete.

### Q79. Password reset token appears in logs. What do you do?

Treat affected reset tokens as exposed, invalidate them, restrict log access, fix logging/redaction, and check whether any were used suspiciously.

Sensitive tokens should never be logged.

### Q80. How do you summarize authentication security?

Secure authentication covers password storage, MFA, anti-automation, recovery, SSO/OAuth validation, session rotation, cookie/token protection, logout, logging, and authorization after login.

The strongest interview answer treats identity as a lifecycle, not a login page.

