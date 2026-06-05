---
name: masvs-checklist
description: Generate a MASVS v2 compliance checklist with MASTG test mappings for a mobile app. Use when you need a complete security checklist, compliance tracking document, or gap analysis against OWASP MASVS for Android or iOS apps.
disable-model-invocation: true
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
  - Task
  - Write
---

# MASVS Compliance Checklist Generator

You are a mobile application security compliance expert. Generate a comprehensive
MASVS v2 compliance checklist tailored to the target application, with MASTG test
mappings and NowSecure best practice annotations.

## Target

Analyze the codebase at: `$ARGUMENTS`

If no path is provided, analyze the current working directory.

## Procedure

### Step 1: Determine Application Context

Analyze the codebase to determine:
- **Platform**: Android / iOS / Cross-platform
- **App Category**: Finance, Healthcare, Enterprise, Consumer, Government, Gaming, etc.
- **Data Sensitivity**: What types of sensitive data does the app handle?
- **Compliance Requirements**: HIPAA, PCI-DSS, SOC 2, GDPR, CCPA implications
- **Risk Profile**: Tier 1 (No Sensitive Data) / Tier 2 (PII) / Tier 3 (High-Value)

### Step 2: Generate Tailored Checklist

Generate the checklist using the following template for EACH applicable control.
Mark controls as Required/Recommended/Optional based on the app's risk profile.
Treat the priorities below as defaults to be adjusted for the app's threat model, regulatory obligations, deployment model, and platform constraints.

## Checklist Template

### MASVS-STORAGE: Data Storage

| # | Control | Priority | Status | MASTG Tests | MASWE IDs | Notes |
|---|---------|----------|--------|-------------|-----------|-------|
| MASVS-STORAGE-1 | App securely stores sensitive data | Required | Pending | MASTG-TEST-0001 (Android), MASTG-TEST-0052 (iOS), MASTG-TEST-0200 | MASWE-0002, MASWE-0006, MASWE-0007 | |
| MASVS-STORAGE-2 | App prevents leakage of sensitive data | Required | Pending | MASTG-TEST-0003, MASTG-TEST-0053, MASTG-TEST-0011, MASTG-TEST-0201, MASTG-TEST-0202 | MASWE-0001, MASWE-0003, MASWE-0004 | |

### MASVS-CRYPTO: Cryptography

| # | Control | Priority | Status | MASTG Tests | MASWE IDs | Notes |
|---|---------|----------|--------|-------------|-----------|-------|
| MASVS-CRYPTO-1 | App employs current strong cryptography | Required | Pending | MASTG-TEST-0013, MASTG-TEST-0061, MASTG-TEST-0208 | MASWE-0019, MASWE-0020, MASWE-0021, MASWE-0022, MASWE-0023, MASWE-0027 | |
| MASVS-CRYPTO-2 | App performs key management per best practices | Required | Pending | MASTG-TEST-0015, MASTG-TEST-0063, MASTG-TEST-0212 | MASWE-0009, MASWE-0012, MASWE-0013, MASWE-0014 | |

### MASVS-AUTH: Authentication and Authorization

| # | Control | Priority | Status | MASTG Tests | MASWE IDs | Notes |
|---|---------|----------|--------|-------------|-----------|-------|
| MASVS-AUTH-1 | Secure authentication/authorization protocols | Required | Pending | MASTG-TEST-0017, MASTG-TEST-0064 | MASWE-0038, MASWE-0041, MASWE-0042 | |
| MASVS-AUTH-2 | Secure local authentication | Required | Pending | MASTG-TEST-0018, MASTG-TEST-0326, MASTG-TEST-0266 | MASWE-0034, MASWE-0043, MASWE-0044, MASWE-0045 | |
| MASVS-AUTH-3 | Additional auth for sensitive operations | Tier 3 | Pending | MASTG-TEST-0327 | MASWE-0029, MASWE-0030, MASWE-0046 | |

### MASVS-NETWORK: Network Communication

| # | Control | Priority | Status | MASTG Tests | MASWE IDs | Notes |
|---|---------|----------|--------|-------------|-----------|-------|
| MASVS-NETWORK-1 | App secures all network traffic | Required | Pending | MASTG-TEST-0019, MASTG-TEST-0065, MASTG-TEST-0217, MASTG-TEST-0233 | MASWE-0048, MASWE-0049, MASWE-0050, MASWE-0052 | |
| MASVS-NETWORK-2 | App performs identity pinning | Tier 3 | Pending | MASTG-TEST-0020, MASTG-TEST-0021, MASTG-TEST-0066 | MASWE-0047 | |

### MASVS-PLATFORM: Platform Interaction

| # | Control | Priority | Status | MASTG Tests | MASWE IDs | Notes |
|---|---------|----------|--------|-------------|-----------|-------|
| MASVS-PLATFORM-1 | App uses IPC mechanisms securely | Required | Pending | MASTG-TEST-0007, MASTG-TEST-0008, MASTG-TEST-0250, MASTG-TEST-0251 | MASWE-0058, MASWE-0059, MASWE-0063, MASWE-0064, MASWE-0066 | |
| MASVS-PLATFORM-2 | App uses WebViews securely | Required | Pending | MASTG-TEST-0028, MASTG-TEST-0029, MASTG-TEST-0075, MASTG-TEST-0077 | MASWE-0068, MASWE-0069, MASWE-0070, MASWE-0071, MASWE-0072 | |
| MASVS-PLATFORM-3 | App uses the user interface securely | Tier 3 | Pending | MASTG-TEST-0035, MASTG-TEST-0289 | MASWE-0053, MASWE-0054, MASWE-0055, MASWE-0056 | |

### MASVS-CODE: Code Quality

| # | Control | Priority | Status | MASTG Tests | MASWE IDs | Notes |
|---|---------|----------|--------|-------------|-----------|-------|
| MASVS-CODE-1 | App requires up-to-date platform version | Tier 3 | Pending | MASTG-TEST-0272 | MASWE-0077, MASWE-0078 | |
| MASVS-CODE-2 | App has mechanism for enforcing updates | Tier 3 | Pending | MASTG-TEST-0274 | MASWE-0075 | |
| MASVS-CODE-3 | No components with known vulnerabilities | Required | Pending | MASTG-TEST-0222 | MASWE-0076 | |
| MASVS-CODE-4 | App validates and sanitizes all inputs | Required | Pending | MASTG-TEST-0025, MASTG-TEST-0245 | MASWE-0079, MASWE-0081, MASWE-0082, MASWE-0083, MASWE-0084, MASWE-0086, MASWE-0087, MASWE-0088 | |

### MASVS-RESILIENCE: Reverse Engineering Resilience

| # | Control | Priority | Status | MASTG Tests | MASWE IDs | Notes |
|---|---------|----------|--------|-------------|-----------|-------|
| MASVS-RESILIENCE-1 | Platform integrity validation | Tier 3 | Pending | MASTG-TEST-0038 | MASWE-0097, MASWE-0098, MASWE-0099, MASWE-0100 | |
| MASVS-RESILIENCE-2 | Anti-tampering mechanisms | Tier 3 | Pending | MASTG-TEST-0040, MASTG-TEST-0224 | MASWE-0104, MASWE-0105, MASWE-0106, MASWE-0107 | |
| MASVS-RESILIENCE-3 | Anti-static analysis mechanisms | Tier 3 | Pending | MASTG-TEST-0045, MASTG-TEST-0247 | MASWE-0089, MASWE-0090, MASWE-0091, MASWE-0092, MASWE-0093 | |
| MASVS-RESILIENCE-4 | Anti-dynamic analysis techniques | Tier 3 | Pending | MASTG-TEST-0039, MASTG-TEST-0263 | MASWE-0101, MASWE-0102, MASWE-0103 | |

### MASVS-PRIVACY: Privacy

| # | Control | Priority | Status | MASTG Tests | MASWE IDs | Notes |
|---|---------|----------|--------|-------------|-----------|-------|
| MASVS-PRIVACY-1 | App minimizes access to sensitive data | Required | Pending | MASTG-TEST-0254, MASTG-TEST-0255 | MASWE-0117 | |
| MASVS-PRIVACY-2 | App prevents user identification | Tier 3 | Pending | MASTG-TEST-0318 | MASWE-0109, MASWE-0110 | |
| MASVS-PRIVACY-3 | Transparent data collection/usage | Required | Pending | MASTG-TEST-0256 | MASWE-0111, MASWE-0112, MASWE-0115 | |
| MASVS-PRIVACY-4 | User control over their data | Tier 3 | Pending | MASTG-TEST-0319 | MASWE-0113, MASWE-0114 | |

### Step 3: Pre-Populate Status from Code Analysis

For each checklist item, scan the codebase for preliminary evidence of implementation:
- Mark as "Likely Implemented" if clear evidence exists in source or configuration
- Mark as "Partial" if some controls exist but gaps remain
- Mark as "No Evidence Found" if no evidence of the control is visible in the codebase
- Mark as "N/A" if the control doesn't apply to this app
- Add specific file:line references for visible evidence
- Do not claim full compliance where dynamic testing, backend behavior, production configuration, or store metadata are required to validate the control

### Step 4: Gap Analysis

Summarize:
1. **Controls Passing**: Count and list
2. **Controls Failing**: Count and list with remediation priority
3. **Controls Partially Met**: Count and list with specific gaps
4. **Overall Compliance Percentage**: Per category and total

## Output

Write the checklist to `MASVS_CHECKLIST.md` in the project root with:

1. **Application Context** — Platform, category, risk profile
2. **Compliance Checklist** — Full checklist with status from code analysis
3. **Gap Analysis** — Missing/partial controls with priorities
4. **Remediation Roadmap** — Ordered list of fixes by priority
5. **Testing Plan** — MASTG tests to execute, ordered by risk
