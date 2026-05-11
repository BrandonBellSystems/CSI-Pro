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
 
### Tier 2: Core Logic Engines (Layers 05–09)

**Layer 07 — Sentiment Lexicon**
A rule-based linguistic analysis engine with 5,000+ scored terms. Provides immediate, offline sentiment scoring without requiring any AI provider.

**Layer 09 — Scoring Engine**
The heart of the system. A multi-variable mathematical model:

Renewal Probability = f(usage_trend, support_volume, sentiment_score,
renewal_proximity, contract_value, engagement_depth)


Each factor has explicit weights derived from CS domain research. See [research/scoring-logic.md](../research/scoring-logic.md) for the derivation.

### Tier 3: Content Generators (Layers 10–12)

**Layer 10 — Account Model**
Immutable data structure with validation rules, default values, and change tracking. Prevents state corruption at the data boundary.

**Layer 11 — Account Store**
Centralized state management with:
- Auto-save every 3 seconds during input
- Snapshot system for manual saves (`Ctrl+S`)
- Undo/redo stack for form interactions

**Layer 12 — Generators**
Three template-driven engines:
- **QBR Generator** — Value Realized / Current Risks / Next Quarter Focus
- **Triage Brief** — What Happened / Why It Matters / Recommended Action
- **Prescriptive Advisor** — Three recovery paths + hybrid optimal solution

Each produces markdown-ready output. In Pro mode, AI enhances the template with contextual language.

### Tier 4: AI & Research Intelligence (Layers 13–17)

**Layer 13 — AI Provider Client**
A polymorphic interface supporting 6 providers with a unified API:
- OpenRouter, Anthropic, Google Gemini, Ollama, OpenAI, Generic

Features: automatic provider fallback, exponential backoff retry, request cancellation, connection testing.

**Layer 14 — Chat Session Manager**
Optimizes token usage by compressing account context before transmission. Maintains session history while staying within context windows.

**Layer 15 — Account Intelligence (Pro)**
Implements a **fallback chain** to ensure the system never returns a null result:

Perplexity (5/day) → Brave Search (2,000/mo) → Wikipedia (unlimited)


**Layer 16 — Document Vault (Pro)**
IndexedDB-backed storage for PDF, CSV, and TXT files. Parsed locally and content injected into AI context window.

**Layer 17 — Unified AccountContext (Pro)**
Aggregation layer: merges local account data, research intelligence, and document context into a single context object.

### Tier 5: UI & Systems (Layers 18–20)

**Layer 18 — UI Engine**
Custom component renderer without a virtual DOM. 40+ specialized methods. Centralized event pipeline with keyboard shortcut system.

**Layer 19 — Self-Test Suite**
80+ automated tests activated via `?test=true`:
- Encryption/decryption cycles
- Scoring algorithm accuracy
- API connection handling
- UI component rendering
- Accessibility compliance

**Layer 20 — Bootstrap**
Manages application lifecycle:
- Demo-first launch (6 pre-built scenarios)
- Pre-generated outputs for immediate value
- Milestone system tracking user progression

---

## 4. Engineering Trade-offs

### 4.1 Single-File vs. Build Tools

**Decision:** Zero bundler. Zero npm. Single HTML file.

**Rationale:** Traditional build tools create dependency trees that are difficult to audit. By using a structured internal component system, we achieve instant auditability and zero deployment friction.

**Cost:** File size (~400KB). No tree-shaking. No HMR.

### 4.2 Deterministic Scoring vs. Pure AI

**Decision:** Rule-based scoring as baseline, AI as enhancer.

**Rationale:** LLMs hallucinate. A CSM presenting a "78% renewal probability" to leadership needs that number to be reproducible. The deterministic engine provides mathematical certainty; the AI provides richer language.

### 4.3 Local Encryption vs. Server-Side E2EE

**Decision:** Client-side AES-256-GCM. No server.

**Rationale:** True E2EE requires a server to manage key exchange. Our Sovereign model eliminates the server entirely — no server to breach, no key management infrastructure to compromise. The trade-off is no cross-device sync without manual export/import. When Cloud Sync launches, it will be an optional layer, not a replacement.

### 4.4 IndexedDB vs. localStorage for Documents

**Decision:** IndexedDB for binary/large data, localStorage for settings.

**Rationale:** localStorage has a ~5MB limit and only stores strings. IndexedDB handles binary blobs efficiently. Settings are small JSON objects that fit localStorage naturally.

---

## 5. Threat Model

| Threat | Mitigation | Limitation |
|:---|:---|:---|
| Data inspected in localStorage | AES-256-GCM encryption | Does not protect against malware with memory access |
| XSS via AI-generated output | HTML sanitization before DOM injection | Prompt injection could produce misleading content |
| License key theft | Device binding + rate limiting | Cannot prevent key sharing entirely |
| Cold boot memory dump | API keys cleared from memory on tab close | Brief exposure window while tab is active |
| Browser data cleared | Export/import backup system | User must proactively backup |

**Honest assessment:** Local encryption protects against accidental data exposure and casual inspection. It does not protect against a determined attacker with root access. This is a fundamental constraint of client-side cryptography.
