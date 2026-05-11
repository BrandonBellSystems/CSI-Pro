# CSI Pro User Guide

> **Version:** 2.3.0 Â· Complete documentation for all features

---

## 1. Getting Started

### Launch CSI Pro

1. **Option A â€” Download:** Save from the [Official Site](https://customersuccessintelligence.com/) and double-click to open
2. **Option B â€” Direct:** Click **[Try It Now](https://customersuccessintelligence.com/free/CSI-Pro-v2.3.0.html)** â€” loads instantly in browser
3. **Option C â€” Clone:**
   ```bash
   git clone https://github.com/BrandonBellSystems/CSI-Pro.git
   open CSI-Pro/dist/CSI-Pro-v2.3.0.html
   ```

### System Requirements

- Chrome 90+, Firefox 88+, Safari 14+, Edge 90+
- No server, no installation, no account required

---

## 2. Analyzing an Account

| Field | Description | Required |
|:---|:---|:---|
| **Account Name** | Customer name | Yes |
| **Contract Value** | Annual contract value | No |
| **Renewal Date** | Upcoming renewal date | Yes |
| **Usage Trend** | % change over last 30 days | Yes |
| **Support Tickets** | Tickets in last 30 days | Yes |
| **Sentiment Notes** | Freeform text | No |

Click **Analyze** (or `Ctrl+Enter`) to generate the score.

---

## 3. Generating Deliverables

### QBR Draft
- **Sections:** Value Realized, Current Risks, Next Quarter Focus
- **Quick Actions:** Copy Brief Â· Email Draft Â· Collapse

### Triage Brief
- **Sections:** What Happened, Why It Matters, Recommended Action

### Prescriptive Advisor
- **Output:** Three recovery paths + hybrid optimal solution

---

## 4. AI Configuration (Pro)

1. Go to Settings (`Ctrl+K`)
2. Select provider â€” [OpenRouter](https://openrouter.ai/keys) is free
3. Paste API key â€” encrypted locally
4. Click **Test Connection**

No API key? Use the **Prompt Booster** to copy a pre-filled prompt into ChatGPT/Claude/Gemini.

---

## 5. Account Intelligence (Pro)

**Fallback Chain:** Perplexity (5/day) â†’ Brave Search (2,000/mo) â†’ Wikipedia (unlimited)

**Free Trial:** 3 searches without purchase.

---

## 6. Document Vault (Pro)

Upload PDF, CSV, or TXT files. Content is parsed locally and injected into AI context for personalized QBRs.

---

## 7. Data Management

- **Auto-Save:** Every 3 seconds while typing
- **Manual Snapshots:** `Ctrl+S`
- **Export:** Settings â†’ Data Management â†’ Export ALL Data (encrypted JSON)
- **Import:** Settings â†’ Data Management â†’ Import Data

> âš ï¸ Clearing browser data removes all CSI Pro data. Export a backup first.

---

## 8. License Management

- Activate Pro in Settings by pasting your Gumroad license key
- **Device limit:** 2 simultaneous activations
- **Deactivation:** Available in Settings on the device you want to remove

---

## 9. Keyboard Shortcuts

| Shortcut | Action |
|:---|:---|
| `Ctrl+Enter` | Analyze account |
| `Ctrl+S` | Save snapshot |
| `Ctrl+K` | Open Settings |
| `Esc` | Close modal |