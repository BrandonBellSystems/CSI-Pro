# CSI Pro — Customer Success Intelligence

[![Version](https://img.shields.io/badge/version-2.3.0-blue?style=flat-square&labelColor=0f1115)](https://github.com/[YOUR_GITHUB_USERNAME]/CSI-Pro/releases)
[![License](https://img.shields.io/badge/license-Proprietary-orange?style=flat-square&labelColor=0f1115)](LICENSE)
[![Build](https://img.shields.io/badge/build-stable-22c55e?style=flat-square&labelColor=0f1115)](https://github.com/[YOUR_GITHUB_USERNAME]/CSI-Pro/actions)
[![Tests](https://img.shields.io/badge/tests-80%2B%20passing-22c55e?style=flat-square&labelColor=0f1115)](#self-test-suite)
[![WCAG](https://img.shields.io/badge/accessibility-WCAG%202.1%20AA-3b82f6?style=flat-square&labelColor=0f1115)](#technical-specifications)
[![Official Site](https://img.shields.io/badge/site-customersuccessintelligence.com-a855f7?style=flat-square&labelColor=0f1115)](https://customersuccessintelligence.com/)

> **A sovereign, zero-server intelligence platform for Customer Success Managers to predict renewals, generate QBRs, and triage escalations — entirely in-browser.**
> 
**[🌐 Official Website (Free Download + Pro)](https://customersuccessintelligence.com/)**  ·
**[▶️ Try It Now](https://customersuccessintelligence.com/free/CSI-Pro-v2.3.0.html)** ·
**[💎 Get Pro — $29](https://brandonbellsystems.gumroad.com/l/csi-pro?wanted=true)** ·
**[📖 Architecture Deep Dive](docs/architecture.md)**

---

## The Sovereign AI Philosophy

Most AI tools require cloud accounts and server-side data storage — creating a structural contradiction: you're asked to upload sensitive customer data (renewal dates, contract values, sentiment notes) to third-party servers.

CSI Pro is built on a different premise:

- **Zero-Install** — Single HTML file. Open in any browser.
- **Zero-Data-Server** — All intelligence computed locally or via direct API calls. No backend.
- **Zero-Dependency** — No npm, no build tools, no external scripts, no CDN.

**Sensitive customer data never leaves the user's device.** There is no database to breach, no server to compromise, no analytics tracking your usage. The browser is the sole data custodian.

### The Design Trade-off

CSI Pro optimizes for **user control and privacy**, not feature scale:

- **Deterministic scoring as ground truth.** Renewal probability is a business-critical metric. It cannot be probabilistic in the machine-learning sense — subject to temperature variance, model hallucination, or prompt drift. CSI Pro computes the score mathematically. AI optionally enriches the *narrative* — it does not define the number.
- **Local encryption, not E2EE cloud.** We encrypt data locally with AES-256-GCM. This protects against accidental exposure and casual inspection. It does not protect against malware with root access — and we're transparent about that boundary.
- **Single-file constraint.** No bundler, no deployment pipeline, no infrastructure. The trade-off is file size and internal complexity. The benefit is instant auditability: what you download is what you run.

---

## Technical Specifications

| Category | Implementation |
|:---|:---|
| **Architecture** | 20-Layer Deterministic Stack |
| **File Type** | Single HTML file (~400KB compressed) |
| **Security** | AES-256-GCM via PBKDF2 (100,000 iterations, fresh 96-bit IV per call) |
| **Storage** | Hybrid: IndexedDB (Document Vault) + localStorage (Settings) |
| **AI Providers** | 6 supported — OpenRouter (free), Anthropic, Gemini, Ollama, OpenAI, Generic |
| **Offline Mode** | Full core features without internet |
| **Accessibility** | WCAG 2.1 AA — keyboard nav, ARIA live regions, color-blind safe |
| **Browser Support** | Chrome 90+, Firefox 88+, Safari 14+, Edge 90+ |
| **Self-Test Suite** | 80+ automated tests — activate with `?test=true` |
| **License Model** | Free tier (perpetual, no time limit) + one-time Pro purchase ($29 launch · $49 regular) |
| **Privacy** | Zero analytics, zero cookies, zero third-party scripts |

---

## The 20-Layer Stack (Tiered Overview)

### Tier 1 — Security & Persistence (Layers 01–04)

| Layer | Name | Responsibility |
|:---|:---|:---|
| 01 | Error System | Structured error codes, logging, recovery paths |
| 02 | CryptoVault | AES-256-GCM encryption, PBKDF2-SHA-256, fresh IV per call |
| 03 | SecureStore | Encrypted localStorage wrapper, namespace isolation, legacy migration |
| 04 | LicenseValidator | Gumroad API validation, device count enforcement (2 devices), rate limiting, tamper detection |

### Tier 2 — Core Logic Engines (Layers 05–09)

| Layer | Name | Responsibility |
|:---|:---|:---|
| 05 | Utilities | Shared helpers, string sanitization, type guards |
| 06 | Constants | Global thresholds, weights, configuration |
| 07 | Sentiment Lexicon | 5,000+ scored terms — deterministic linguistic analysis, fully offline |
| 08 | Time Utilities | Business-day calculations, renewal windows, SLA computations |
| 09 | Scoring Engine | Multi-variable weighted algorithm producing 0–100% probability |

### Tier 3 — Content Generators (Layers 10–12)

| Layer | Name | Responsibility |
|:---|:---|:---|
| 10 | Account Model | Immutable data structure, validation rules, change tracking |
| 11 | Account Store | Centralized state management, auto-save (3s interval), snapshot system |
| 12 | Generators | QBR Draft, Triage Brief, Prescriptive Advisor — deterministic templates with optional AI enhancement |

### Tier 4 — AI & Research Intelligence (Layers 13–17)

| Layer | Name | Responsibility |
|:---|:---|:---|
| 13 | AI Provider Client | Unified interface across 6 providers, retry/cancel, connection testing |
| 14 | Chat Session Manager | Context-window optimization, history summarization, token compression |
| 15 | Account Intelligence `Pro` | Research fallback chain: Perplexity (5/day) → Brave (2,000/mo) → Wikipedia (unlimited) |
| 16 | Document Vault `Pro` | IndexedDB blob storage, PDF/CSV/TXT extraction, AI context injection |
| 17 | Unified AccountContext `Pro` | Aggregation layer: local data + research intelligence + document context |

### Tier 5 — UI & Systems (Layers 18–20)

| Layer | Name | Responsibility |
|:---|:---|:---|
| 18 | UI Engine | Component renderer, event binding pipeline, 40+ specialized DOM methods |
| 19 | Self-Test Suite | 80+ automated tests, visual report overlay via `?test=true` |
| 20 | Bootstrap | Demo-first launch with 6 pre-built scenarios, milestone system, workflow state machine |

> **For the full layer-by-layer walkthrough and engineering trade-offs, see [docs/architecture.md](docs/architecture.md).**

---

## Getting Started

### Zero to First Score in 60 Seconds

1. **Download** `dist/CSI-Pro-v2.3.0.html` — or [open directly](https://customersuccessintelligence.com/free/CSI-Pro-v2.3.0.html)
2. **Open** in any modern browser — drag and drop works
3. **Start** — pick one of 6 built-in demo scenarios (no data entry required) or enter your own account

```bash
# Or clone and open locally
git clone https://github.com/[YOUR_GITHUB_USERNAME]/CSI-Pro.git
open CSI-Pro/dist/CSI-Pro-v2.3.0.html
