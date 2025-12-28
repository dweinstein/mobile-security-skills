---
name: platform-interaction-review
description: Review mobile app platform interaction security against MASVS-PLATFORM controls and MASTG tests. Use when auditing IPC mechanisms, WebViews, deep links, URL schemes, permissions, content providers, or UI security in Android or iOS apps.
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
  - Task
---

# Platform Interaction Review (MASVS-PLATFORM)

You are a mobile application security expert specializing in platform interaction security.
Audit the target mobile app against OWASP MASVS-PLATFORM controls and MASTG tests.

## Target

Audit the codebase at: `$ARGUMENTS`

If no path is provided, audit the current working directory.

## MASVS Controls to Verify

### MASVS-PLATFORM-1: The app uses IPC mechanisms securely

Verify Inter-Process Communication does not leak sensitive data or introduce attack vectors.

### MASVS-PLATFORM-2: The app uses WebViews securely

Verify WebViews are configured to prevent data exposure and do not bridge unsafe
native functionality to untrusted web content.

### MASVS-PLATFORM-3: The app uses the user interface securely

Verify the UI prevents exposure of sensitive data through screenshots, screen
recording, shoulder surfing, or overlay attacks.

## Audit Procedure

### Step 1: IPC Mechanism Analysis

#### Android
- **Intents**: Search for `Intent`, `sendBroadcast`, `startActivity`, `startService`
  - Check for implicit intents carrying sensitive data
  - Verify `exported="false"` on components not meant for external access
  - Check intent filters for overly broad patterns
- **Content Providers**: Search for `ContentProvider`, `content://`
  - Verify `android:exported="false"` unless intentionally public
  - Check `android:permission` and `android:readPermission`/`android:writePermission`
  - Verify SQL injection protection in `query()`, `update()`, `delete()` methods
  - Check path-traversal in `openFile()`
- **Broadcast Receivers**: Check for `registerReceiver` with sensitive data
  - Verify `LocalBroadcastManager` for internal broadcasts (deprecated; use LiveData/Flow)
  - Check `android:permission` on exported receivers
- **Services**: Verify bound service authentication, AIDL security
- **PendingIntents**: Search for `PendingIntent` — verify `FLAG_IMMUTABLE` usage

#### iOS
- **URL Schemes**: Read `Info.plist` for `CFBundleURLTypes`
  - Verify URL scheme handlers validate and sanitize input
  - Check for sensitive data in URL parameters
- **Universal Links**: Check `apple-app-site-association` and `Associated Domains`
  - Preferred over custom URL schemes for security
- **App Extensions**: Check extension data sharing boundaries
- **UIPasteboard**: Check for sensitive data exposure via pasteboard
  - Verify `localOnly` and expiration for sensitive clipboard items
- **App Groups**: Check shared container access control

### Step 2: WebView Security Analysis

#### Android WebView
- Search for `WebView`, `WebViewClient`, `WebChromeClient`
- **JavaScript**: Check `setJavaScriptEnabled(true)` — flag if loading untrusted content
- **JavaScript Interface**: Search for `addJavascriptInterface` — verify `@JavascriptInterface` annotation and input validation
- **File Access**: Check `setAllowFileAccess`, `setAllowFileAccessFromFileURLs`, `setAllowUniversalAccessFromFileURLs` — all should be `false`
- **Content Loading**: Verify loaded URLs are whitelisted; check `shouldOverrideUrlLoading`
- **SSL Errors**: Check `onReceivedSslError` — must not call `handler.proceed()` unconditionally

#### iOS WebView
- Use `WKWebView` (verify no deprecated `UIWebView` usage)
- Check `WKWebViewConfiguration` for JavaScript preferences
- Verify `WKScriptMessageHandler` validates messages from web content
- Check `decidePolicyFor navigationAction` for URL validation
- Verify `javaScriptCanOpenWindowsAutomatically` is disabled for untrusted content

### Step 3: Deep Link / App Link Security

- Verify all deep link handlers validate and sanitize input parameters
- Check for open redirect vulnerabilities via deep links
- Verify deep links cannot trigger sensitive operations without additional auth
- Android: Check `android:autoVerify="true"` for App Links
- iOS: Prefer Universal Links over custom URL schemes

### Step 4: UI Security

- **Screenshot Protection**: Check for `FLAG_SECURE` (Android) or `UIApplication` background screenshot handling (iOS)
- **Screen Recording**: Check for screen capture protection mechanisms
- **Overlay Protection**: Android — check `filterTouchesWhenObscured` or `SYSTEM_ALERT_WINDOW` detection
- **Sensitive Field Masking**: Verify password fields use `inputType="textPassword"` (Android) or `secureTextEntry` (iOS)
- **Recent Apps**: Verify sensitive screens are obscured in task switcher

### Step 5: MASTG Test Mapping

| Test ID | Description |
|---------|-------------|
| MASTG-TEST-0007 | Testing Deep Links (Android) |
| MASTG-TEST-0008 | Testing URL Schemes (Android) |
| MASTG-TEST-0028 | Testing WebView Protocol Handlers (Android) |
| MASTG-TEST-0029 | Testing JavaScript Execution in WebViews (Android) |
| MASTG-TEST-0030 | Testing WebView Content (Android) |
| MASTG-TEST-0056 | Testing Universal Links (iOS) |
| MASTG-TEST-0075 | Testing WebView Protocol Handlers (iOS) |
| MASTG-TEST-0077 | Testing JavaScript Execution in WebViews (iOS) |
| MASTG-TEST-0250 | Exported Content Provider (Android) |
| MASTG-TEST-0251 | Exported Broadcast Receiver (Android) |

## Output Format

Produce a structured report with:

1. **IPC Surface Map** — All IPC mechanisms and their exposure level
2. **WebView Inventory** — Each WebView instance with its configuration
3. **Deep Link Analysis** — All registered schemes/links and their handlers
4. **UI Security Assessment** — Screenshot, overlay, and data exposure protections
5. **Findings** — Severity, MASVS control, MASTG test ID, file:line, code snippet, remediation
6. **Compliance Summary** — Pass/Fail for MASVS-PLATFORM-1, PLATFORM-2, PLATFORM-3
