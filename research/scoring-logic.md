# CSI Pro Renewal Scoring Algorithm — Derivation and Weights

> **Status:** Deterministic baseline algorithm  
> **Version:** v2.3.0  
> **Methodology:** Research-based derivation from industry benchmarks

---

## 1. Methodology

The CSI Pro scoring algorithm is **not** machine-learned from historical data. It is **designed** based on published research in Customer Success literature and benchmark analysis.

**Why designed:** Renewal probability is a decision tool, not a prediction model. Transparency requires explicit weights, not black-box models.

---

## 2. Factor Definitions

### 2.1 Usage Trend (`usage_trend`)

**Definition:** Percent change in product usage over trailing period.  
**Input:** -100% to +100%  
**Normalization:** `usage_trend` clamped to [-100, 100], then scaled to [0, 1]:
```python
normalized = (usage_trend + 100) / 200
```

**Why 30% weight:** Usage is the strongest direct signal of product satisfaction. Declining usage directly correlates with churn.

---

### 2.2 Support Volume (`support_tickets`)

**Definition:** Number of support tickets opened in trailing 30 days.  
**Input:** 0 to 100+ tickets  
**Normalization:**
```python
normalized = min(1, 1 - (support_tickets / 50))
```

**Why 20% weight:** High ticket volume signals friction and dissatisfaction.

---

### 2.3 Sentiment Score (`sentiment`)

**Definition:** Numeric score from linguistic analysis of CSM notes.  
**Algorithm:** 5,000+ term lexicon with intensity scaling.  
**Normalization:** `sentiment` clamped to [-1, 1], scaled to [0, 1].

**Why 20% weight:** Captures qualitative signals that quantitative metrics miss — relationship quality, executive satisfaction.

---

### 2.4 Renewal Proximity (`time_to_renewal`)

**Definition:** Days until contract renewal.  
**Input:** 0 to 365 days  
**Normalization:**
```python
normalized = (time_to_renewal - 0) / (365 - 0)
```

**Why 15% weight:** Urgency increases as time decreases, but time alone is not primary.

---

### 2.5 Contract Value (`arr_value`)

**Definition:** Annual Recurring Revenue.  
**Input:** $0 to $1,000,000+  
**Normalization:**
```python
normalized = min(1, (arr_value / 100000))
```

**Why 10% weight:** Larger customers receive more attention but have higher expectations.

---

### 2.6 Engagement Depth (`feature_adoption`)

**Definition:** Count of product features actively used.  
**Input:** 0 to 20 features  
**Normalization:**
```python
normalized = feature_count / 20
```

**Why 5% weight:** Feature breadth indicates stickiness but is not conclusive alone.

---

## 3. Score Calculation

### Formula
```python
Score = (
    Usage_Trend      * 0.30 +
    Support_Volume   * 0.20 +
    Sentiment_Score  * 0.20 +
    Proximity        * 0.15 +
    Contract_Value   * 0.10 +
    Engagement_Depth * 0.05
)

Final Score = clamp(Score, 0, 100)
```

### Risk Band Thresholds

| Band | Score Range | Indicator | Action |
|:---|:---|:---|:---|
| **Critical** | 0–25% | 🔴 Red | Immediate escalation required |
| **High Risk** | 26–50% | 🟡 Yellow | Proactive intervention recommended |
| **Monitor** | 51–75% | 🔵 Blue | Watch closely, prepare contingency |
| **Healthy** | 76–100% | 🟢 Green | Maintain and expand |

---

## 4. Deterministic Guarantee

Given identical inputs, the Scoring Engine will **always** produce the same output. This is deliberate: renewal probability is a **measurement**, not a prediction. AI may enhance the narrative around the score, but the score itself is mathematically derived.
