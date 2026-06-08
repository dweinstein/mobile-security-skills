---
name: maswe-mapper
description: Map mobile security findings from a report or audit to MASWE (Mobile Application Security Weakness Enumeration) IDs. Use when standardizing vulnerability reports, converting free-text findings to MASWE identifiers, or enriching existing audit outputs with normalized weakness references for compliance documentation.
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
  - Task
  - Write
---

# MASWE Weakness Mapper

You are a mobile application security expert. Your task is to map security findings
to their canonical MASWE (Mobile Application Security Weakness Enumeration) identifiers,
enabling standardized, traceable vulnerability reporting across MASVS categories.

## Input

Analyze the findings from: `$ARGUMENTS`

The input may be:
- A free-text audit report (`.md`, `.txt`, `.pdf` path)
- A findings table from a previous skill run (e.g., output of `secure-storage-audit`)
- A raw list of vulnerability descriptions
- The current working directory (scan for any MASVS_CHECKLIST.md or audit reports)

## MASWE Catalog

### MASVS-STORAGE

| MASWE ID | Weakness |
|----------|---------|
| MASWE-0001 | Insertion of Sensitive Data into Logs |
| MASWE-0002 | Sensitive Data Stored With Insufficient Access Restrictions in Internal Locations |
| MASWE-0003 | Backup Unencrypted |
| MASWE-0004 | Sensitive Data Not Excluded From Backup |
| MASWE-0006 | Sensitive Data Stored Unencrypted in Private Storage Locations |
| MASWE-0007 | Sensitive Data Stored Unencrypted in Shared Storage Requiring No User Interaction |

### MASVS-CRYPTO

| MASWE ID | Weakness |
|----------|---------|
| MASWE-0009 | Improper Cryptographic Key Generation |
| MASWE-0010 | Improper Cryptographic Key Derivation |
| MASWE-0011 | Cryptographic Key Rotation Not Implemented |
| MASWE-0012 | Insecure or Wrong Usage of Cryptographic Key |
| MASWE-0013 | Hardcoded Cryptographic Keys in Use |
| MASWE-0014 | Cryptographic Keys Not Properly Protected at Rest |
| MASWE-0015 | Deprecated Android KeyStore Implementations |
| MASWE-0016 | Unsafe Handling of Imported Cryptographic Keys |
| MASWE-0017 | Cryptographic Keys Not Properly Protected on Export |
| MASWE-0018 | Cryptographic Keys Access Not Restricted |
| MASWE-0019 | Risky Cryptography Implementations |
| MASWE-0020 | Improper Encryption |
| MASWE-0021 | Improper Hashing |
| MASWE-0022 | Predictable Initialization Vectors (IVs) |
| MASWE-0023 | Risky Padding |
| MASWE-0024 | Improper Use of Message Authentication Code (MAC) |
| MASWE-0025 | Improper Generation of Cryptographic Signatures |
| MASWE-0026 | Improper Verification of Cryptographic Signature |
| MASWE-0027 | Improper Random Number Generation |

### MASVS-AUTH

| MASWE ID | Weakness |
|----------|---------|
| MASWE-0005 | API Keys Hardcoded in the App Package |
| MASWE-0028 | MFA Implementation Best Practices Not Followed |
| MASWE-0029 | Step-Up Authentication Not Implemented After Login |
| MASWE-0030 | Re-Authentication Not Triggered On Contextual State Changes |
| MASWE-0031 | Insecure Use of Android Protected Confirmation |
| MASWE-0032 | Platform-Provided Authentication APIs Not Used |
| MASWE-0033 | Authentication or Authorization Protocol Security Best Practices Not Followed |
| MASWE-0034 | Insecure Implementation of Confirm Credentials |
| MASWE-0035 | Passwordless Authentication Not Implemented |
| MASWE-0036 | Authentication Material Stored Unencrypted on the Device |
| MASWE-0037 | Authentication Material Sent over Insecure Connections |
| MASWE-0038 | Authentication Tokens Not Validated |
| MASWE-0039 | Shared Web Credentials and Website-association Not Implemented |
| MASWE-0040 | Insecure Authentication in WebViews |
| MASWE-0041 | Authentication Enforced Only Locally Instead of on the Server-side |
| MASWE-0042 | Authorization Enforced Only Locally Instead of on the Server-side |
| MASWE-0043 | App Custom PIN Not Bound to Platform KeyStore |
| MASWE-0044 | Biometric Authentication Can Be Bypassed |
| MASWE-0045 | Fallback to Non-biometric Credentials Allowed for Sensitive Transactions |
| MASWE-0046 | Crypto Keys Not Invalidated on New Biometric Enrollment |

### MASVS-NETWORK

| MASWE ID | Weakness |
|----------|---------|
| MASWE-0047 | Insecure Identity Pinning |
| MASWE-0048 | Insecure Machine-to-Machine Communication |
| MASWE-0049 | Proven Networking APIs Not Used |
| MASWE-0050 | Cleartext Traffic |
| MASWE-0051 | Unprotected Open Ports |
| MASWE-0052 | Insecure Certificate Validation |

### MASVS-PLATFORM

| MASWE ID | Weakness |
|----------|---------|
| MASWE-0053 | Sensitive Data Leaked via the User Interface |
| MASWE-0054 | Sensitive Data Leaked via Notifications |
| MASWE-0055 | Sensitive Data Leaked via Screenshots or Screen Recordings |
| MASWE-0056 | Tapjacking Attacks |
| MASWE-0057 | StrandHogg Attack / Task Affinity Vulnerability |
| MASWE-0058 | Insecure Deep Links |
| MASWE-0059 | Use of Unauthenticated Platform IPC |
| MASWE-0060 | Insecure Use of UIActivity |
| MASWE-0061 | Insecure Use of App Extensions |
| MASWE-0062 | Insecure Services |
| MASWE-0063 | Insecure Broadcast Receivers |
| MASWE-0064 | Insecure Content Providers |
| MASWE-0065 | Sensitive Data Permanently Shared with Other Apps |
| MASWE-0066 | Insecure Intents |
| MASWE-0068 | JavaScript Bridges in WebViews |
| MASWE-0069 | WebViews Allows Access to Local Resources |
| MASWE-0070 | JavaScript Loaded from Untrusted Sources |
| MASWE-0071 | WebViews Loading Content from Untrusted Sources |
| MASWE-0072 | Universal XSS |
| MASWE-0073 | Insecure WebResourceResponse Implementations |
| MASWE-0074 | Web Content Debugging Enabled |
| MASWE-0118 | Sensitive Data Not Removed After Use |

### MASVS-CODE

| MASWE ID | Weakness |
|----------|---------|
| MASWE-0075 | Enforced Updating Not Implemented |
| MASWE-0076 | Dependencies with Known Vulnerabilities |
| MASWE-0077 | Running on a Recent Platform Version Not Ensured |
| MASWE-0078 | Latest Platform Version Not Targeted |
| MASWE-0079 | Unsafe Handling of Data from the Network |
| MASWE-0080 | Unsafe Handling of Data from Backups |
| MASWE-0081 | Unsafe Handling of Data From External Interfaces |
| MASWE-0082 | Unsafe Handling of Data From Local Storage |
| MASWE-0083 | Unsafe Handling of Data From The User Interface |
| MASWE-0084 | Unsafe Handling of Data from IPC |
| MASWE-0085 | Unsafe Dynamic Code Loading |
| MASWE-0086 | SQL Injection |
| MASWE-0087 | Insecure Parsing and Escaping |
| MASWE-0088 | Insecure Object Deserialization |
| MASWE-0116 | Compiler-Provided Security Features Not Used |

### MASVS-RESILIENCE

| MASWE ID | Weakness |
|----------|---------|
| MASWE-0008 | Missing Device Secure Lock Verification Implementation |
| MASWE-0067 | Debuggable Flag Not Disabled |
| MASWE-0089 | Code Obfuscation Not Implemented |
| MASWE-0090 | Resource Obfuscation Not Implemented |
| MASWE-0091 | Anti-Deobfuscation Techniques Not Implemented |
| MASWE-0092 | Static Analysis Tools Not Prevented |
| MASWE-0093 | Debugging Symbols Not Removed |
| MASWE-0094 | Non-Production Resources Not Removed |
| MASWE-0095 | Code That Disables Security Controls Not Removed |
| MASWE-0096 | Data Sent Unencrypted Over Encrypted Connections |
| MASWE-0097 | Root/Jailbreak Detection Not Implemented |
| MASWE-0098 | App Virtualization Environment Detection Not Implemented |
| MASWE-0099 | Emulator Detection Not Implemented |
| MASWE-0100 | Device Attestation Not Implemented |
| MASWE-0101 | Debugger Detection Not Implemented |
| MASWE-0102 | Dynamic Analysis Tools Detection Not Implemented |
| MASWE-0103 | RASP Techniques Not Implemented |
| MASWE-0104 | App Integrity Not Verified |
| MASWE-0105 | Integrity of App Resources Not Verified |
| MASWE-0106 | Official Store Verification Not Implemented |
| MASWE-0107 | Runtime Code Integrity Not Verified |

### MASVS-PRIVACY

| MASWE ID | Weakness |
|----------|---------|
| MASWE-0108 | Sensitive Data in Network Traffic |
| MASWE-0109 | Lack of Anonymization or Pseudonymisation Measures |
| MASWE-0110 | Use of Unique Identifiers for User Tracking |
| MASWE-0111 | Inadequate Privacy Policy |
| MASWE-0112 | Inadequate Data Collection Declarations |
| MASWE-0113 | Lack of Proper Data Management Controls |
| MASWE-0114 | Inadequate Data Visibility Controls |
| MASWE-0115 | Inadequate or Ambiguous User Consent Mechanisms |
| MASWE-0117 | Inadequate Permission Management |

## Mapping Procedure

### Step 1: Extract Findings

Parse the input to extract individual findings. For each finding, capture:
- Title or short description
- Affected component / file path (if available)
- Severity level (if stated)
- Any existing MASTG test IDs already referenced

### Step 2: Classify by MASVS Category

Determine which MASVS domain each finding belongs to:
- Data storage, backups, logs → MASVS-STORAGE
- Cryptography, keys, RNG → MASVS-CRYPTO
- Auth, biometrics, session → MASVS-AUTH
- TLS, pinning, cleartext → MASVS-NETWORK
- IPC, WebViews, deep links, UI → MASVS-PLATFORM
- Dependencies, injection, input validation → MASVS-CODE
- Root detection, obfuscation, anti-debug → MASVS-RESILIENCE
- Tracking, consent, permissions → MASVS-PRIVACY

### Step 3: Match to MASWE IDs

For each finding, select the most specific matching MASWE ID(s) from the catalog above.
- Prefer specific IDs over broad ones (e.g., MASWE-0013 "Hardcoded Keys" over MASWE-0012 "Insecure Key Usage" when the key is hardcoded)
- Assign multiple IDs only when the finding covers multiple distinct weaknesses
- If no exact match exists, note it as "No matching MASWE" rather than forcing an imprecise mapping

### Step 4: Confidence Assessment

For each mapping, rate confidence:
- **High** — finding description directly matches MASWE title and scope
- **Medium** — finding is within MASWE scope but description is ambiguous
- **Low** — best-effort match; MASWE may not fully capture the finding

## Output Format

Produce a structured mapping report:

### Mapped Findings Table

| # | Finding Title | Severity | MASWE ID(s) | MASWE Weakness Name(s) | Confidence | Notes |
|---|--------------|----------|-------------|------------------------|------------|-------|

### Unmapped Findings

List any findings that could not be confidently mapped to a MASWE ID, with a brief
explanation of why (too specific, out of scope, infrastructure-level, etc.).

### Coverage Summary

| MASVS Category | Findings Count | MASWE IDs Referenced |
|----------------|----------------|----------------------|

### Reference Links

For each unique MASWE ID cited, provide:
- `https://mas.owasp.org/MASWE/<MASWE-XXXX>/`
