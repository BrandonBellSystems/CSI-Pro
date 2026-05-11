# CSI Pro Security Model & Data Privacy

> **Status:** Production · v2.3.0  
> **Last reviewed:** March 2026

---

## 1. Zero-Knowledge Architecture

CSI Pro operates on a **zero-knowledge principle** for the developer. We do not operate servers that ingest, process, or store user data.

**Data flow perimeter:**

User Input → Client-Side Encryption → Browser Storage → Direct HTTPS API Call to Provider


No data is ever routed through a middle-man server or proxy.

---

## 2. Cryptographic Implementation

### 2.1 Key Derivation — PBKDF2

Passwords and secrets are never stored in plaintext. We derive keys using PBKDF2:

| Parameter | Value | Purpose |
|:---|:---|:---|
| Hash function | SHA-256 | Collision resistance |
| Iterations | 100,000 | Increases brute-force cost exponentially |
| Salt | Unique per installation | Prevents rainbow table attacks |
| Key length | 256 bits | Matches AES-256 requirement |

This aligns with **NIST SP 800-132** recommendations.

### 2.2 Authenticated Encryption — AES-256-GCM

Data at rest is encrypted with AES-256-GCM:

| Parameter | Value |
|:---|:---|
| Algorithm | AES-256-GCM |
| Key size | 256 bits |
| IV | Fresh 96-bit cryptographically-random nonce per encryption call |
| Authentication tag | 128 bits |

**Why GCM over CBC:** GCM is an authenticated encryption mode. It provides both **confidentiality** and **authenticity** — tampered ciphertext fails decryption rather than silently returning corrupted data. This prevents padding oracle attacks and bit-flipping exploits.

### 2.3 IV Management

A fresh IV is generated for every encryption operation using `crypto.getRandomValues()`. IVs are never reused with the same key.

---

## 3. What Is Encrypted

| Data Type | Storage Location | Encryption |
|:---|:---|:---|
| AI Provider API Keys | localStorage (via SecureStore) | AES-256-GCM |
| Account Data | localStorage (via SecureStore) | AES-256-GCM |
| Uploaded Documents | IndexedDB (Document Vault) | AES-256-GCM |
| User Settings | localStorage (via SecureStore) | AES-256-GCM |
| License Key | localStorage (via SecureStore) | AES-256-GCM |

---

## 4. Memory Security

- API keys are held in volatile memory (RAM) **only during active use**
- Keys are decrypted on-demand for AI calls, not held persistently
- On tab close, navigation away, or page refresh: all keys cleared from memory
- No keys are written to disk in plaintext at any point

---

## 5. Attack Surface Analysis

### Mitigated Threats

| Threat | Mitigation |
|:---|:---|
| Server-side data breach | **Impossible** — no server stores user data |
| Man-in-the-middle on API calls | HTTPS enforced; CSP `connect-src` restricts endpoints |
| Cross-site scripting (XSS) | All AI-generated output sanitized via `textContent` before DOM injection |
| Analytics/tracking exfiltration | No Google Analytics, no cookies, no tracking scripts |
| Supply chain attack via npm | No build tools, no dependency tree, no `node_modules` |

### Acknowledged Limitations

| Limitation | Honest Assessment |
|:---|:---|
| Local malware with root access | Can extract encryption keys from memory during active session |
| Physical access | Unlocked device with CSI Pro open exposes decrypted data in memory |
| Backup security | Exported JSON backups contain **decrypted data** — users must protect these files |
| Browser extension access | Extensions with broad permissions may read localStorage; we encrypt contents to reduce exposure |

This is the fundamental trade-off of local-first architecture: we eliminate remote attack surfaces entirely but cannot protect against local device compromise.

---

## 6. Compliance Alignment

| Standard | Status | How |
|:---|:---|:---|
| NIST SP 800-132 | Aligned | PBKDF2 key derivation with 100k iterations, unique salt |
| NIST SP 800-38D | Aligned | AES-256-GCM authenticated encryption with fresh IV per operation |
| GDPR | Compliant by design | No PII transmitted to any server |
| CCPA | Compliant by design | No data sold or shared |
| WCAG 2.1 AA | Compliant | Keyboard nav, ARIA, color-blind safe |

---

## 7. Third-Party Dependencies

| Dependency | Purpose | Data Shared | Security Posture |
|:---|:---|:---|:---|
| **Gumroad** | Payment processing only | Email (for license key delivery) | PCI-DSS compliant. We never receive payment card data |
| **AI Providers** | Optional LLM enhancement | Account context (direct browser→provider) | HTTPS via TLS 1.2+. No proxy. |
| **Wikipedia API** | Free research fallback | Company name (public API) | No key required |

**No other third-party scripts.** Zero analytics. Zero tracking.
