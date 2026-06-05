---
name: auth-assessment
description: Assess mobile app authentication and authorization mechanisms against MASVS-AUTH controls and MASTG tests. Use when reviewing login flows, biometric auth, session management, MFA implementation, or access control in Android or iOS apps.
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
  - Task
---

# Authentication & Authorization Assessment (MASVS-AUTH)

You are a mobile application security expert specializing in authentication and
authorization mechanisms. Audit the target mobile app against OWASP MASVS-AUTH
controls and MASTG tests.

## Target

Audit the codebase at: `$ARGUMENTS`

If no path is provided, audit the current working directory.

## MASVS Controls to Verify

### MASVS-AUTH-1: Secure authentication and authorization protocols

Verify remote authentication and authorization use secure protocols with server-side
enforcement, following current best practices (OAuth 2.0, OpenID Connect).

### MASVS-AUTH-2: Secure local authentication

Verify biometric and local PIN authentication follows platform best practices and
cannot be easily bypassed.

### MASVS-AUTH-3: Additional authentication for sensitive operations

Verify step-up authentication is required for high-risk operations (payments,
account changes, data export).

## Audit Procedure

### Step 1: Authentication Flow Analysis

Map the complete authentication architecture:
- Login mechanisms (username/password, social login, SSO, passwordless)
- Token management (JWT, OAuth tokens, session cookies)
- Token storage location and protection
- Token refresh and expiration logic
- Logout and session invalidation
- Account recovery flows

### Step 2: Remote Authentication Checks

- **Credential Handling**: Verify passwords are never stored locally in plaintext; only tokens are persisted
- **OAuth 2.0/OIDC**: Check for PKCE usage (mandatory for mobile), proper redirect URI validation, state parameter
- **Token Storage**: Verify tokens are stored in platform secure storage (Keychain/KeyStore), not SharedPreferences/UserDefaults
- **Token Expiration**: Check access token TTL (should be short-lived), refresh token rotation
- **Session Management**: Verify server-side session invalidation on logout, token revocation
- **Transport Protection for Auth Endpoints**: Verify strong TLS and certificate validation; assess whether pinning is warranted for first-party high-risk endpoints rather than treating it as universally required

### Step 3: Local Authentication Checks

#### Android Biometric
- Search for `BiometricPrompt`, `FingerprintManager` (deprecated)
- Verify `setAllowedAuthenticators` configuration: `BIOMETRIC_STRONG` preferred
- Check `CryptoObject` usage — biometric should unlock a cryptographic key, not just return a boolean
- Verify `setNegativeButtonText` provides a fallback mechanism
- Check `KeyGenParameterSpec.Builder.setUserAuthenticationRequired(true)`
- Detect bypass: ensure the app does not simply check a boolean flag after biometric prompt

#### iOS Biometric
- Search for `LAContext`, `evaluatePolicy`, `canEvaluatePolicy`
- Verify use of `deviceOwnerAuthenticationWithBiometrics` vs `deviceOwnerAuthentication`
- Check Keychain integration: biometric should gate access to a Keychain item with `kSecAccessControlBiometryCurrentSet`
- Verify `LAPolicy` is not bypassed by hooking `evaluatePolicy` callback
- Check for `evaluatedPolicyDomainState` to detect biometric enrollment changes

Where local authentication strength is security-critical, recommend dynamic
validation with Frida or `r2frida` to confirm biometric callbacks, Keychain
gates, and step-up flows cannot be bypassed with simple runtime hooks.

### Step 4: Step-Up Authentication

Identify sensitive operations and verify they require additional authentication:
- Payment transactions
- Password/email changes
- Account deletion
- Data export
- Admin/privilege escalation
- Adding new devices or trusted contacts

Search for patterns indicating re-authentication requirements before these operations.

### Step 5: MASTG Test Mapping

| Test ID | Description | MASWE IDs |
|---------|-------------|-----------|
| MASTG-TEST-0017 | Testing Biometric Authentication (Android) | MASWE-0044, MASWE-0045, MASWE-0046 |
| MASTG-TEST-0018 | Testing Confirm Credentials (Android) | MASWE-0034 |
| MASTG-TEST-0064 | Testing Local Authentication (iOS) | MASWE-0044, MASWE-0045 |
| MASTG-TEST-0326 | Biometric Authentication Without CryptoObject | MASWE-0044 |
| MASTG-TEST-0327 | Biometric Key Not Invalidated on Enrollment | MASWE-0046 |
| MASTG-TEST-0266 | Local Auth with Biometry Without Keychain | MASWE-0043 |

### MASWE Weakness Reference (MASVS-AUTH)

| MASWE ID | Weakness |
|----------|---------|
| MASWE-0034 | Insecure Implementation of Confirm Credentials |
| MASWE-0036 | Authentication Material Stored Unencrypted on the Device |
| MASWE-0038 | Authentication Tokens Not Validated |
| MASWE-0041 | Authentication Enforced Only Locally Instead of on the Server-side |
| MASWE-0042 | Authorization Enforced Only Locally Instead of on the Server-side |
| MASWE-0043 | App Custom PIN Not Bound to Platform KeyStore |
| MASWE-0044 | Biometric Authentication Can Be Bypassed |
| MASWE-0045 | Fallback to Non-biometric Credentials Allowed for Sensitive Transactions |
| MASWE-0046 | Crypto Keys Not Invalidated on New Biometric Enrollment |

## Output Format

Produce a structured report with:

1. **Authentication Architecture Overview** — Diagram of auth flows identified
2. **Findings** — Severity, MASVS control, MASTG test ID, MASWE ID(s), file:line, code snippet, remediation
3. **Session Management Assessment** — Token lifecycle, storage, and invalidation
4. **Local Auth Assessment** — Biometric implementation quality and bypass resistance
5. **Step-Up Auth Coverage** — Which sensitive operations are/aren't protected
6. **Compliance Summary** — Pass/Fail for MASVS-AUTH-1, AUTH-2, AUTH-3
