# nps-track — Track Satisfaction Proxy Signals

## When to Use
CS Lead uses this monthly to estimate customer satisfaction from indirect signals. No formal NPS survey at Stage 2 — this uses proxy metrics instead.

## Inputs
- Support ticket history and sentiment
- Feature request frequency per org
- Engagement trend data (logins, usage over time)
- Churn-detect and health-score outputs

## Procedure

1. Collect proxy satisfaction signals for each org:
   - **Support ticket sentiment**: Classify recent tickets as positive, neutral, or negative tone
   - **Feature request frequency**: High frequency can signal investment (positive) or frustration (negative) — cross-reference with tone
   - **Engagement trend**: Increasing = positive, stable = neutral, declining = negative
2. Compute a proxy NPS score per org:
   - Promoter signals: positive sentiment + growing engagement + constructive feature requests
   - Passive signals: neutral sentiment + stable engagement
   - Detractor signals: negative sentiment + declining engagement + complaint-style requests
3. Aggregate across all orgs into an overall proxy NPS (-100 to +100)
4. Compare with previous month to show trend
5. Post trend to `#customer`:

```markdown
---
agent: cs-lead
type: report
severity: info
tags: [nps-track, monthly]
channel: #customer
---

## Satisfaction Proxy — {month}

### Overall Proxy NPS: {score} ({trend vs last month})

| Segment   | Count | % of Total |
|-----------|-------|------------|
| Promoters | ...   | ...        |
| Passives  | ...   | ...        |
| Detractors| ...   | ...        |

### Per-Org Breakdown
| Org | Sentiment | Engagement Trend | Request Tone | Classification |
|-----|-----------|------------------|--------------|----------------|
| ... | ...       | ...              | ...          | ...            |

### Trend
- This month: {score}
- Last month: {score}
- Direction: {improving / stable / declining}
```

## Output Format
Markdown report posted to `#customer` with proxy NPS, segment breakdown, per-org classification, and month-over-month trend.

## Rules
- This is a proxy, not real NPS — always label it as such
- Never present proxy scores as definitive satisfaction measurements
- Sentiment classification must be conservative — when in doubt, classify as neutral
- Flag detractor orgs to Account Mgr for follow-up
