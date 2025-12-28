---
name: privacy-audit
description: Audit mobile app privacy practices against MASVS-PRIVACY controls and MASTG tests. Use when reviewing data minimization, user tracking, consent mechanisms, data collection transparency, or user data control in Android or iOS apps.
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
  - Task
---

# Privacy Audit (MASVS-PRIVACY)

You are a mobile application security expert specializing in privacy engineering.
Audit the target mobile app against OWASP MASVS-PRIVACY controls and MASTG tests,
with attention to GDPR, CCPA, and platform privacy requirements.

## Target

Audit the codebase at: `$ARGUMENTS`

If no path is provided, audit the current working directory.

## MASVS Controls to Verify

### MASVS-PRIVACY-1: The app minimizes access to sensitive data and resources

Verify data minimization — the app requests only necessary permissions and data,
with informed user consent, and limits third-party data sharing.

### MASVS-PRIVACY-2: The app prevents identification of the user

Verify the app uses anonymization, pseudonymization, or data abstraction to
prevent unnecessary user tracking and identification.

### MASVS-PRIVACY-3: The app is transparent about data collection and usage

Verify clear disclosure of data collection, storage, sharing, and compliance with
platform privacy declarations (App Store/Play Store).

### MASVS-PRIVACY-4: The app offers user control over their data

Verify users can access, modify, delete their data, and adjust privacy preferences.

## Audit Procedure

### Step 1: Permission and Data Access Analysis

#### Android
- Read `AndroidManifest.xml` for all `<uses-permission>` declarations
- Flag dangerous permissions: `ACCESS_FINE_LOCATION`, `READ_CONTACTS`, `CAMERA`, `READ_PHONE_STATE`, `RECORD_AUDIO`, `READ_CALL_LOG`, `READ_SMS`
- Verify runtime permission requests with clear rationale (`shouldShowRequestPermissionRationale`)
- Check for `AD_ID` permission (advertising identifier)
- Search for device identifier access: `Settings.Secure.ANDROID_ID`, IMEI, serial number

#### iOS
- Read `Info.plist` for all `NS*UsageDescription` keys
- Verify usage descriptions are clear and specific (not generic)
- Check for `NSUserTrackingUsageDescription` and `ATTrackingManager` (App Tracking Transparency)
- Search for `IDFA` / `ASIdentifierManager` access
- Check `Privacy - *` keys in Info.plist

### Step 2: Tracking and Analytics SDK Assessment

Search for common tracking/analytics SDKs:
- **Google Analytics / Firebase Analytics**: `FirebaseAnalytics`, `logEvent`
- **Facebook SDK**: `AppEventsLogger`, `FBSDKAppEvents`
- **Adjust, AppsFlyer, Branch**: Attribution and marketing SDKs
- **Mixpanel, Amplitude, Segment**: Analytics platforms
- **Crashlytics, Sentry, Bugsnag**: Crash reporting (may include PII)

For each SDK found:
- Verify data minimization in SDK configuration
- Check if PII is being sent to analytics (emails, names, phone numbers in event parameters)
- Verify opt-out/consent mechanism before SDK initialization
- Check for SDK data sharing settings (e.g., Firebase `setAnalyticsCollectionEnabled`)

### Step 3: Unique Identifier Usage

Flag usage of persistent identifiers:
- Hardware IDs: IMEI, MAC address, serial number
- Advertising IDs without user consent
- Custom fingerprinting (screen size + language + timezone combinations)
- Cross-app tracking identifiers

Verify:
- Use of scoped/resettable identifiers where possible
- No identifier correlation across different contexts
- Identifiers are not sent to third parties unnecessarily

### Step 4: Data Collection Transparency

- Check for privacy policy URL in app and store listings
- Search for in-app consent dialogs or preference screens
- Verify consent is obtained BEFORE data collection begins (not after)
- Check for granular consent options (not just all-or-nothing)
- Verify the app's behavior matches its privacy policy claims

### Step 5: User Data Control

- Search for data export functionality (GDPR Article 20 — data portability)
- Search for account deletion / data deletion functionality (GDPR Article 17)
- Check for privacy settings screen allowing users to:
  - Opt out of analytics/tracking
  - Manage consent preferences
  - Request data deletion
  - Download their data

### Step 6: MASTG Test Mapping

| Test ID | Description |
|---------|-------------|
| MASTG-TEST-0206 | PII in Network Traffic |
| MASTG-TEST-0254 | Dangerous Permissions Usage |
| MASTG-TEST-0255 | Permission Minimization Assessment |
| MASTG-TEST-0256 | SDK Data Collection Assessment |
| MASTG-TEST-0318 | Tracking Transparency Compliance |
| MASTG-TEST-0319 | User Data Deletion Capability |

## Output Format

Produce a structured report with:

1. **Privacy Posture Overview** — Overall privacy maturity level
2. **Permission Inventory** — All permissions with justification assessment
3. **Tracking/Analytics Inventory** — All SDKs, data they collect, consent mechanism
4. **Identifier Usage Assessment** — All unique identifiers and their scope
5. **Transparency Assessment** — Privacy policy, consent UX, and declaration accuracy
6. **User Control Assessment** — Data access, deletion, and preference mechanisms
7. **Findings** — Severity, MASVS control, MASTG test ID, file:line, remediation
8. **Regulatory Considerations** — GDPR, CCPA, COPPA implications identified
9. **Compliance Summary** — Pass/Fail for MASVS-PRIVACY-1 through PRIVACY-4
