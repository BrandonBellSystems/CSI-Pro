# CSI Pro System Architecture

> **Status:** Production · v2.3.0 · Single-File HTML Runtime
> **Author:** Brandon Bell Systems
> **Last Updated:** March 2026

---

## 1. Architectural Philosophy

CSI Pro is engineered as a **Sovereign Intelligence Platform**. It replaces the traditional Client-Server SaaS model with a **Client-Only Runtime** — the entire stack collapses into a single file.

### Why

Traditional CS tools create a structural contradiction: they ask CSMs to upload sensitive customer data (renewal dates, contract values, sentiment notes) to third-party servers. CSI Pro resolves this by making the browser the sole data custodian:

- **Zero-Latency Execution** — No network round-trips for core logic
- **Total Data Sovereignty** — The user's browser is the only runtime
- **High Availability** — 100% functional offline

### The Core Design Decision: Deterministic Baseline First

LLMs are stochastic — they produce different outputs for identical inputs. For critical business metrics like "Will this account renew?", that's unacceptable.

CSI Pro uses a **deterministic scoring engine as ground truth**, then optionally enhances the *narrative* output with AI:

Raw Metrics → Deterministic Engine → Ground Truth Score
↘
AI Enhancement Layer → Enriched Narrative (optional)


AI enhances the story. It does not compute the score.

---

## 2. Data Flow Lifecycle

All data follows a strict one-way sanitization path:

1. INGEST User input → UI Engine → Sanitization
2. PROCESS Sanitized data → Scoring Engine → Renewal Probability
3. ENRICH (Optional) Sanitized context → AI Provider → Enhanced output
4. ENCRYPT All persisted data → CryptoVault → AES-256-GCM ciphertext
5. PERSIST Ciphertext → SecureStore → localStorage / IndexedDB
6. PRESENT Decrypted data → Component Renderer → DOM


At no point does data traverse a server under our control. API calls go directly from the user's browser to their configured AI provider.

---

## 3. Tier-by-Tier Walkthrough

### Tier 1: Security & Persistence (Layers 01–04)

The "hard shell" of the application. All data entering persistence is intercepted by `CryptoVault` before hitting disk.

**Layer 01 — Error System**
- Structured error codes: `ERR_CRYPTO_DECRYPT_FAILED`, `ERR_LICENSE_RATE_LIMITED`, etc.
- Graceful degradation paths — the app never crashes without a user-facing explanation
- Recovery suggestions embedded in error responses

**Layer 02 — CryptoVault**
- Algorithm: AES-256-GCM (confidentiality + authenticity in one operation)
- Key Derivation: PBKDF2-SHA-256, 100,000 iterations
- IV: Fresh cryptographically-random 96-bit nonce per encryption call
- Purpose: Even if localStorage is inspected, data remains ciphertext

**Layer 03 — SecureStore**
- Encrypted JSON wrapper over `localStorage` (settings) and `IndexedDB` (documents)
- Namespace isolation prevents key collision
- Legacy migration path for format changes across versions

**Layer 04 — LicenseValidator**
- Gumroad API integration for entitlement verification
- Device count enforcement: maximum 2 simultaneous activations
- Rate limiting: 5 validation attempts per hour
- Tamper detection: hash verification prevents license key modification

