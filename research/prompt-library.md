# CSI Pro Prompt Library

> Evolution of the Zero-Config AI Bridge ("Prompt Booster")

## Philosophy

Not every user has an API key. CSI Pro's Prompt Booster generates fully pre-filled prompts containing all relevant account context, allowing users to paste directly into ChatGPT, Claude, or Gemini — zero configuration, instant results.

## Template Structure

Each prompt is constructed deterministically from the Account Model (Layer 10) and includes:
- Account metadata (name, renewal date, contract value)
- Computed score and risk band
- Usage trend and support volume
- Sentiment summary
- Specific user request (QBR, Triage, or Advisor)

## Example: QBR Prompt Template

```
You are a Customer Success strategist preparing a Quarterly Business Review.

Account: {{account_name}}
Renewal Date: {{renewal_date}} ({{days_until}} days)
Renewal Probability: {{score}}% ({{risk_band}})
Usage Trend: {{usage_trend}}
Support Tickets (30d): {{ticket_count}}
Sentiment: {{sentiment_summary}}

Generate a structured QBR with:
1. Value Realized
2. Current Risks
3. Next Quarter Focus
4. Recommended Actions
```

## Design Rationale

- **No leakage of proprietary logic:** The prompt contains only user-visible data, not scoring weights.
- **Provider-agnostic:** Works with any LLM that accepts plain text.
- **Offline-capable generation:** Prompts are built by the deterministic engine without network calls.

---

## Changelog

- **v2.3.0:** Initial Prompt Booster release with QBR, Triage, and Advisor templates.
- **v2.3.0:** Added "Copy to Clipboard" with mobile-aware fallback.
