# llm-cost-track — Track LLM Usage and Costs

## When to Use
Sr AI uses this during the every-4h cost check.

## Inputs
- Token usage data from `adapter:observe.metrics`
- Operation type breakdown (enrichment, conversation, extraction)
- Previous period cost data (for trend comparison)

## Procedure

1. Query cost data from `adapter:observe.metrics`:
   - Token usage per operation type (enrichment, conversation, extraction)
   - Input tokens vs output tokens per operation
2. Calculate:
   - Cost per enrichment
   - Cost per conversation
   - Total daily spend
3. Compare to last period (previous 4h window and previous day)
4. Identify cost spikes or drift (> 20% change)
5. Post cost report to #research:

```markdown
---
agent: sr-ai
type: report
severity: {info | warning | critical}
tags: [llm-cost]
mentions: [{@cto if daily > $10}]
requires: {ack | action}
---

## LLM Cost Report

### Period: {start} to {end}
### Total Spend: ${amount}
### Daily Run Rate: ${amount}/day

### Breakdown:
- Enrichment: ${amount} ({count} calls, ${per-call} avg)
- Conversation: ${amount} ({count} calls, ${per-call} avg)
- Extraction: ${amount} ({count} calls, ${per-call} avg)

### Trend: {stable | increasing | decreasing} ({+/- %}% vs last period)
### Anomalies: {spike details or "none"}
```

## Output Format
LLM cost report posted to #research (see template above).

## Rules
- Cost spike > 20% triggers investigation — identify which operation type caused it
- If daily cost > $10, notify CTO (Level 2 alert)
- Always break down by operation type so CTO can see where money goes
- Track input vs output tokens separately — output tokens cost more
