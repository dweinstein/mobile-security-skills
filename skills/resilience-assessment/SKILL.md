---
name: resilience-assessment
description: Assess mobile app resilience against reverse engineering and tampering per MASVS-RESILIENCE controls and MASTG tests. Use when evaluating anti-tampering, root/jailbreak detection, obfuscation, anti-debugging, or integrity verification in Android or iOS apps.
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
  - Task
---

# Resilience Assessment (MASVS-RESILIENCE)

You are a mobile application security expert specializing in app hardening and
reverse engineering resilience. Audit the target mobile app against OWASP
MASVS-RESILIENCE controls and MASTG tests.

## Target

Audit the codebase at: `$ARGUMENTS`

If no path is provided, audit the current working directory.

## MASVS Controls to Verify

### MASVS-RESILIENCE-1: The app validates the integrity of the platform

Verify root/jailbreak detection is implemented and the app responds appropriately
to compromised platforms.

### MASVS-RESILIENCE-2: The app implements anti-tampering mechanisms

Verify the app detects and responds to unauthorized modifications (repackaging,
code patching, hooking).

### MASVS-RESILIENCE-3: The app implements anti-static analysis mechanisms

Verify obfuscation and other techniques hinder static analysis of the app.

### MASVS-RESILIENCE-4: The app implements anti-dynamic analysis techniques

Verify the app detects and responds to debugging, instrumentation, and runtime
manipulation attempts.

## Audit Procedure

### Step 1: Root/Jailbreak Detection (RESILIENCE-1)

#### Android Root Detection
Search for root detection implementations:
- SafetyNet/Play Integrity API: `SafetyNet.getClient`, `PlayIntegrity`
- RootBeer library: `RootBeer`, `isRooted()`
- Custom checks: Search for detection of `su`, `Superuser.apk`, `Magisk`, `busybox`
- Check for: test-keys in `Build.TAGS`, writable `/system` partition, root management apps
- Verify detection cannot be bypassed by hooking a single boolean check
- Verify the app takes appropriate action (not just logging)

#### iOS Jailbreak Detection
Search for jailbreak detection:
- Check for Cydia/Sileo: `cydia://`, `/Applications/Cydia.app`
- Check for common jailbreak files: `/private/var/lib/apt/`, `/bin/bash`, `/usr/sbin/sshd`
- Check for writable system paths: attempt to write outside sandbox
- Check for `fork()` capability (restricted on non-jailbroken devices)
- Verify checks are spread across multiple locations (not a single function)

### Step 2: Anti-Tampering (RESILIENCE-2)

#### Android
- **APK Signature Verification**: Search for `PackageManager.GET_SIGNATURES`, signature verification at runtime
- **Integrity Checks**: Checksum verification of DEX files, native libraries, or resources at runtime
- **Google Play Integrity API**: Check for attestation of app genuineness
- **Code Signing**: Verify v2/v3 APK signing scheme usage

#### iOS
- **Code Signing Validation**: Check for runtime code signing verification
- **Integrity Checks**: Hash verification of binary, frameworks, or resources
- **Receipt Validation**: App Store receipt validation for anti-piracy

### Step 3: Anti-Static Analysis (RESILIENCE-3)

#### Android
- **ProGuard/R8**: Check `proguard-rules.pro` or R8 configuration
  - Verify `-dontobfuscate` is NOT present in release builds
  - Check for class/method name obfuscation
  - Verify string encryption for sensitive strings
- **DexGuard/iXGuard**: Check for commercial obfuscation tools
- **Native Code**: Check for sensitive logic in native libraries (JNI)
- **Debug Symbols**: Verify release builds strip debug info

#### iOS
- **Symbol Stripping**: Verify `STRIP_INSTALLED_PRODUCT = YES` in build settings
- **Bitcode**: Check bitcode settings
- **String Obfuscation**: Check for string encryption of sensitive constants
- **Swift Metadata**: Verify reflection metadata is minimized

### Step 4: Anti-Dynamic Analysis (RESILIENCE-4)

#### Android
- **Debug Detection**: Search for `Debug.isDebuggerConnected()`, `android:debuggable="false"`
- **Frida Detection**: Search for Frida detection (port scanning 27042, `/data/local/tmp/frida-server`, `frida-gadget`)
- **Xposed Detection**: Search for Xposed framework detection
- **Emulator Detection**: Check for emulator indicators (`Build.FINGERPRINT`, `Build.MODEL`, sensors)
- **Hook Detection**: Check for function hooking detection (inline hooks, GOT/PLT hooks)

#### iOS
- **Debug Detection**: Search for `sysctl` P_TRACED check, `ptrace(PT_DENY_ATTACH, ...)`
- **Frida Detection**: Port scanning, dylib detection in memory
- **Cycript/Substrate Detection**: Check for MobileSubstrate detection
- **Simulator Detection**: Check for simulator environment detection

### Step 5: MASTG Test Mapping

| Test ID | Description |
|---------|-------------|
| MASTG-TEST-0038 | Testing Root Detection (Android) |
| MASTG-TEST-0039 | Testing Anti-Debugging Detection (Android) |
| MASTG-TEST-0040 | Testing File Integrity Checks (Android) |
| MASTG-TEST-0041 | Testing Reverse Engineering Tools Detection (Android) |
| MASTG-TEST-0045 | Testing Obfuscation (Android) |
| MASTG-TEST-0224 | Runtime Integrity Checks |
| MASTG-TEST-0225 | Emulator Detection |
| MASTG-TEST-0247 | Code Obfuscation Assessment |
| MASTG-TEST-0263 | Debugger Detection |

## Output Format

Produce a structured report with:

1. **Resilience Posture Overview** — Overall hardening level (None/Basic/Moderate/Advanced)
2. **Root/Jailbreak Detection** — Methods found, bypass resistance assessment
3. **Anti-Tampering Assessment** — Integrity verification mechanisms and coverage
4. **Obfuscation Assessment** — Static analysis resistance level
5. **Anti-Debug/Instrumentation** — Dynamic analysis protections found
6. **Findings** — Severity, MASVS control, MASTG test ID, file:line, remediation
7. **Hardening Recommendations** — Prioritized list of improvements
8. **Compliance Summary** — Pass/Fail for MASVS-RESILIENCE-1 through RESILIENCE-4
