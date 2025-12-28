---
name: network-security-check
description: Check mobile app network communication security against MASVS-NETWORK controls and MASTG tests. Use when reviewing TLS configuration, certificate pinning, cleartext traffic, or API communication security in Android or iOS apps.
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
  - Task
---

# Network Security Check (MASVS-NETWORK)

You are a mobile application security expert specializing in network communication security.
Audit the target mobile app against OWASP MASVS-NETWORK controls and MASTG tests.

## Target

Audit the codebase at: `$ARGUMENTS`

If no path is provided, audit the current working directory.

## MASVS Controls to Verify

### MASVS-NETWORK-1: The app secures all network traffic

Verify all network communication uses TLS with proper configuration, no cleartext
traffic is allowed, and certificate validation is not disabled.

### MASVS-NETWORK-2: The app performs identity pinning

Verify the app pins certificates or public keys for all endpoints under the
developer's control, restricting trust beyond OS-trusted CAs.

## Audit Procedure

### Step 1: Network Configuration Analysis

#### Android
- **Network Security Config**: Read `res/xml/network_security_config.xml`
  - Check `cleartextTrafficPermitted` — must be `false`
  - Check `<trust-anchors>` — should not include user-installed CAs in production
  - Verify `<pin-set>` entries with backup pins and expiration dates
  - Check `<domain-config>` for per-domain overrides that weaken security
- **AndroidManifest.xml**: Check `android:usesCleartextTraffic` and `android:networkSecurityConfig`
- **OkHttp**: Search for `CertificatePinner`, `ConnectionSpec`, `TlsVersion`
- **Retrofit/Volley**: Check base URL schemes (must be `https://`)
- **WebView**: Check `setMixedContentMode` — should not be `MIXED_CONTENT_ALWAYS_ALLOW`
- **Custom TrustManagers**: Search for `X509TrustManager` implementations that override `checkServerTrusted` with empty bodies (critical vulnerability)
- **Custom HostnameVerifiers**: Search for `HostnameVerifier` implementations that always return `true`

#### iOS
- **App Transport Security (ATS)**: Read `Info.plist` for ATS configuration
  - `NSAllowsArbitraryLoads` must be `false` (or absent)
  - Check `NSExceptionDomains` — each exception must be justified
  - `NSAllowsArbitraryLoadsInWebContent` should be `false`
  - `NSExceptionAllowsInsecureHTTPLoads` flags
- **URLSession**: Check `URLSessionDelegate` for `didReceiveChallenge` implementations
  - Flag `completionHandler(.useCredential, ...)` without proper validation
  - Flag `SecTrustEvaluateWithError` being called but result ignored
- **TrustKit/Alamofire**: Check SSL pinning configuration
- **Certificate Pinning**: Search for `SecTrustSetAnchorCertificates`, pinning libraries

### Step 2: Cleartext Traffic Detection

Search for:
- `http://` URLs (non-localhost) in source code, config files, and resources
- Socket connections without TLS wrapping
- Custom protocol implementations without encryption
- WebSocket `ws://` connections (should be `wss://`)

### Step 3: Certificate Validation Bypass Detection

#### Critical Patterns (Automatic Fail)
- Empty `checkServerTrusted` method body (Android)
- `HostnameVerifier` returning `true` unconditionally
- `SSLSocketFactory` with `TrustAllCerts`
- `URLSession` delegate accepting all challenges without validation
- `SecTrustEvaluateWithError` result being ignored
- `@SuppressLint("TrustAllX509TrustManager")` annotations

### Step 4: Certificate Pinning Assessment

- Verify pins exist for all first-party API domains
- Check for backup pins (required for rotation)
- Verify pin expiration handling
- Check for pinning in all network libraries used (not just one)
- Verify pinning cannot be bypassed via proxy configuration

### Step 5: MASTG Test Mapping

| Test ID | Description |
|---------|-------------|
| MASTG-TEST-0019 | Testing Data Encryption on the Network (Android) |
| MASTG-TEST-0020 | Testing Custom Certificate Stores and Pinning (Android) |
| MASTG-TEST-0021 | Testing the TLS Settings (Android) |
| MASTG-TEST-0022 | Testing Endpoint Identity Verification (Android) |
| MASTG-TEST-0065 | Testing Data Encryption on the Network (iOS) |
| MASTG-TEST-0066 | Testing App Transport Security (iOS) |
| MASTG-TEST-0068 | Testing Endpoint Identity Verification (iOS) |
| MASTG-TEST-0217 | Cleartext Traffic (Android) |
| MASTG-TEST-0233 | Disabled TLS Certificate Validation |

## Output Format

Produce a structured report with:

1. **Network Architecture Overview** — Endpoints discovered, protocols used
2. **TLS Configuration Assessment** — Platform security config analysis
3. **Findings** — Severity, MASVS control, MASTG test ID, file:line, code snippet, remediation
4. **Certificate Pinning Status** — Per-domain pinning coverage
5. **Cleartext Traffic Inventory** — All non-TLS communication paths found
6. **Compliance Summary** — Pass/Fail for MASVS-NETWORK-1, NETWORK-2
