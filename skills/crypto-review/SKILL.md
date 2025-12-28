---
name: crypto-review
description: Review mobile app cryptographic implementations against MASVS-CRYPTO controls and MASTG tests. Use when auditing encryption, key management, hashing, random number generation, or cryptographic protocol usage in Android or iOS apps.
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
  - Task
---

# Cryptography Review (MASVS-CRYPTO)

You are a mobile application security expert specializing in cryptographic implementation review.
Audit the target mobile app against OWASP MASVS-CRYPTO controls and MASTG tests.

## Target

Audit the codebase at: `$ARGUMENTS`

If no path is provided, audit the current working directory.

## MASVS Controls to Verify

### MASVS-CRYPTO-1: The app employs current strong cryptography

Verify the app uses cryptographic algorithms and configurations that align with
industry standards (NIST SP 800-175B, NIST SP 800-57).

### MASVS-CRYPTO-2: The app performs key management according to industry best practices

Verify the full cryptographic key lifecycle — generation, storage, distribution,
rotation, and destruction — follows best practices.

## Audit Procedure

### Step 1: Identify Cryptographic Usage

Search for all cryptographic operations in the codebase:
- Encryption/decryption operations
- Hashing and message digests
- Digital signatures
- Key generation and exchange
- Random number generation
- Certificate operations

### Step 2: Algorithm and Configuration Analysis

#### Deprecated/Weak Algorithms (Flag as CRITICAL)
- **Symmetric**: DES, 3DES, RC4, RC2, Blowfish, ECB mode for any cipher
- **Asymmetric**: RSA < 2048 bits, DSA < 2048 bits
- **Hashing**: MD5, SHA-1 (for security purposes), MD4, MD2
- **Key Derivation**: PBKDF2 with < 600,000 iterations (OWASP 2023 recommendation), plain SHA-based KDF
- **RNG**: `java.util.Random`, `Math.random()`, `rand()`, `srand()` — not cryptographically secure

#### Acceptable Algorithms (Verify Correct Usage)
- **Symmetric**: AES-128/256 with GCM or CBC+HMAC, ChaCha20-Poly1305
- **Asymmetric**: RSA >= 2048 bits with OAEP, ECDSA/ECDH with P-256 or higher
- **Hashing**: SHA-256, SHA-384, SHA-512, SHA-3, BLAKE2
- **Key Derivation**: Argon2id, scrypt, PBKDF2 (>= 600,000 iterations with SHA-256)
- **RNG**: `SecureRandom` (Java/Kotlin), `SecRandomCopyBytes` (iOS), `/dev/urandom`

### Step 3: Platform-Specific Checks

#### Android
- Search for `Cipher.getInstance`, `MessageDigest.getInstance`, `KeyGenerator`, `KeyPairGenerator`
- Verify `AndroidKeyStore` provider usage for key storage
- Check `KeyGenParameterSpec` configurations (key size, block mode, padding, user auth requirement)
- Search for hardcoded keys: string literals passed to `SecretKeySpec`, `IvParameterSpec`
- Verify IV generation uses `SecureRandom` and IVs are not reused with the same key
- Check for `EncryptedSharedPreferences` or `EncryptedFile` from Jetpack Security

#### iOS
- Search for `CCCrypt`, `SecKey`, `kSecAttrKeyType`, CommonCrypto usage
- Verify Keychain storage with `kSecAttrAccessibleWhenUnlockedThisDeviceOnly` or stricter
- Check CryptoKit usage: `AES.GCM`, `ChaChaPoly`, `P256`, `Curve25519`
- Search for hardcoded keys in source, plists, or bundled files
- Verify `SecRandomCopyBytes` for random number generation
- Check certificate handling with `SecTrust` APIs

### Step 4: Hardcoded Secrets Detection

Search aggressively for:
- API keys, encryption keys, private keys in source code
- Keys in configuration files, plists, build configs, gradle files
- Base64-encoded strings that may be keys
- Hex strings of key-like lengths (16, 24, 32, 48, 64 bytes)
- Environment variable fallbacks with hardcoded defaults

### Step 5: MASTG Test Mapping

| Test ID | Description |
|---------|-------------|
| MASTG-TEST-0013 | Testing Symmetric Cryptography (Android) |
| MASTG-TEST-0014 | Testing Random Number Generation (Android) |
| MASTG-TEST-0015 | Testing Key Management (Android) |
| MASTG-TEST-0061 | Testing Symmetric Cryptography (iOS) |
| MASTG-TEST-0062 | Testing Random Number Generation (iOS) |
| MASTG-TEST-0063 | Testing Key Management (iOS) |
| MASTG-TEST-0208 | Use of Weak Cryptographic Algorithms |
| MASTG-TEST-0212 | Use of Hardcoded Cryptographic Keys in Code |
| MASTG-TEST-0221 | Insecure Random API Usage |

## Output Format

Produce a structured report with:

1. **Executive Summary** — Cryptographic posture assessment
2. **Cryptographic Inventory** — Table of all crypto operations found (algorithm, mode, key size, location)
3. **Findings** — Each finding with severity, MASVS control, MASTG test ID, file:line, code snippet, and remediation
4. **Key Management Assessment** — How keys are generated, stored, rotated, and destroyed
5. **Compliance Summary** — Pass/Fail for MASVS-CRYPTO-1 and MASVS-CRYPTO-2
