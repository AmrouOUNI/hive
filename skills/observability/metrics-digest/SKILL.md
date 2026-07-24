---
name: observability-metrics-digest
description: Generate the daily and weekly metrics summary from observability data.
---

# metrics-digest — Generate Daily/Weekly Metrics Summary

## When to Use
Obs Chief runs this as part of dawn report (daily) and weekly digest (Monday).

## Inputs
- `adapter:observe.metrics` — current metrics
- `.claude/hive/context/obs-chief.md` — baselines and historical data
- Previous digest (for trend comparison)

## Procedure

1. Collect current metrics via adapters
2. Compare to 24h ago (daily) or 7d ago (weekly)
3. Calculate trends (up/down/stable)
4. Format and post

## Output Format

```markdown
---
agent: obs-chief
type: report
severity: info
tags: [metrics, {daily|weekly}]
requires: info
---

## Metrics Digest — {date} ({daily|weekly})

### Product Health
| Metric | Current | Previous | Delta | Trend |
|--------|---------|----------|-------|-------|
| Active orgs | {n} | {n} | {+/-n} | {arrow} |
| Enrichments/day | {n} | {n} | {%} | {arrow} |
| Conversations/day | {n} | {n} | {%} | {arrow} |
| Companies created | {n} | {n} | {%} | {arrow} |

### System Health
| Metric | Current | Previous | Delta | Status |
|--------|---------|----------|-------|--------|
| Error rate | {%} | {%} | {+/-%} | {emoji} |
| P95 latency | {ms} | {ms} | {+/-ms} | {emoji} |
| DB connections | {n} | {n} | {+/-n} | {emoji} |

### AI Pipeline
| Metric | Current | Previous | Delta | Status |
|--------|---------|----------|-------|--------|
| LLM cost/day | ${n} | ${n} | {%} | {emoji} |
| Enrichment accuracy | {%} | {%} | {+/-%} | {emoji} |

### Incidents Since Last Digest
| ID | Severity | Summary | Status |
```

## Rules
- Status emoji: green = within 10% of baseline, yellow = 10-20% deviation, red = > 20%
- Always include incident count, even if zero
- Weekly digest includes 7d trend charts (text-based sparklines)
