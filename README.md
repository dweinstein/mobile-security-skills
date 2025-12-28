# MASTG Skills — Mobile App Security Testing Skills for Claude Code

A shareable collection of [Claude Code Agent Skills](https://code.claude.com/docs/en/skills) for mobile application security testing, built on:

- **OWASP MASVS v2** — Mobile Application Security Verification Standard
- **OWASP MASTG** — Mobile Application Security Testing Guide
- **NowSecure** — Secure Mobile Development best practices

## Skills

### Security Audit Skills (per MASVS category)

| Skill | MASVS Category | Description |
|-------|---------------|-------------|
| [secure-storage-audit](skills/secure-storage-audit/) | MASVS-STORAGE | Audit data-at-rest: local storage, logs, backups, clipboard, keyboard cache |
| [crypto-review](skills/crypto-review/) | MASVS-CRYPTO | Review encryption algorithms, key management, RNG, hardcoded secrets |
| [auth-assessment](skills/auth-assessment/) | MASVS-AUTH | Assess login flows, biometric auth, session management, step-up auth |
| [network-security-check](skills/network-security-check/) | MASVS-NETWORK | Check TLS config, certificate pinning, cleartext traffic, cert validation |
| [platform-interaction-review](skills/platform-interaction-review/) | MASVS-PLATFORM | Review IPC, WebViews, deep links, content providers, UI security |
| [code-quality-scan](skills/code-quality-scan/) | MASVS-CODE | Scan dependencies, input validation, injection flaws, platform versions |
| [resilience-assessment](skills/resilience-assessment/) | MASVS-RESILIENCE | Assess anti-tampering, root/jailbreak detection, obfuscation, anti-debug |
| [privacy-audit](skills/privacy-audit/) | MASVS-PRIVACY | Audit permissions, tracking, consent, data minimization, user controls |

### Planning & Compliance Skills

| Skill | Description |
|-------|-------------|
| [mobile-threat-model](skills/mobile-threat-model/) | Generate STRIDE threat models mapped to MASVS controls with NowSecure risk tiering |
| [masvs-checklist](skills/masvs-checklist/) | Generate tailored MASVS v2 compliance checklists with MASTG test mappings |
| [secure-mobile-dev-guide](skills/secure-mobile-dev-guide/) | Interactive secure development guidance with code examples for Android and iOS |
| [mobile-pentest-plan](skills/mobile-pentest-plan/) | Generate comprehensive penetration testing plans based on MASTG methodology |

## Installation

### Option 1: As a project plugin

Add to your project's `.claude/settings.json`:

```json
{
  "plugins": [
    "/path/to/mastg-skills"
  ]
}
```

### Option 2: Copy into your project

Copy the `skills/` directory into your project's `.claude/skills/` directory:

```bash
cp -r skills/* /path/to/your-project/.claude/skills/
```

### Option 3: Personal skills

Copy into your personal Claude Code skills directory:

```bash
cp -r skills/* ~/.claude/skills/
```

## Usage

Skills are invoked automatically by Claude Code when relevant, or manually:

```
# Run a specific audit against your codebase
/secure-storage-audit ./path/to/mobile/app
/crypto-review ./path/to/mobile/app
/network-security-check ./path/to/mobile/app

# Generate compliance artifacts
/masvs-checklist ./path/to/mobile/app
/mobile-threat-model ./path/to/mobile/app
/mobile-pentest-plan ./path/to/mobile/app

# Get secure development guidance
/secure-mobile-dev-guide how to store tokens securely
/secure-mobile-dev-guide certificate pinning implementation
```

## MASVS v2 Coverage

All 8 MASVS control groups are covered with 24 individual controls:

| Category | Controls | Skills |
|----------|----------|--------|
| MASVS-STORAGE | 2 controls | secure-storage-audit |
| MASVS-CRYPTO | 2 controls | crypto-review |
| MASVS-AUTH | 3 controls | auth-assessment |
| MASVS-NETWORK | 2 controls | network-security-check |
| MASVS-PLATFORM | 3 controls | platform-interaction-review |
| MASVS-CODE | 4 controls | code-quality-scan |
| MASVS-RESILIENCE | 4 controls | resilience-assessment |
| MASVS-PRIVACY | 4 controls | privacy-audit |

## NowSecure Risk Tiering

Skills apply NowSecure's tiered security policy model:

- **Tier 1** — No Sensitive Data: Basic network and code quality controls
- **Tier 2** — Handles PII: Full storage, crypto, auth, platform, and privacy controls
- **Tier 3** — Flagship / High-Value: All controls including resilience, pinning, and advanced privacy

## References

- [OWASP MASVS v2](https://mas.owasp.org/MASVS/)
- [OWASP MASTG](https://mas.owasp.org/MASTG/)
- [OWASP MAS Checklist](https://mas.owasp.org/checklists/)
- [NowSecure Secure Mobile Development](https://github.com/nowsecure/secure-mobile-development)
- [Claude Code Skills Documentation](https://code.claude.com/docs/en/skills)
