# health-score — Calculate Per-Org Health Score

## When to Use
CS Lead uses this weekly to assess the health of each organization. Produces a 0-100 score per org with color-coded status.

## Inputs
- Org list from database
- Login frequency data (last 7 days)
- Enrichment run counts
- Companies created counts
- Conversations held counts
- Error rate per org

## Procedure

1. Pull the list of all active orgs
2. For each org, collect metrics for the past 7 days:
   - Login frequency (weight: 25%)
   - Enrichments run (weight: 20%)
   - Companies created (weight: 20%)
   - Conversations held (weight: 20%)
   - Error rate affecting them — inverted, lower is better (weight: 15%)
3. Normalize each metric to a 0-100 scale based on expected usage benchmarks
4. Calculate weighted average to produce a single 0-100 score
5. Classify each org:
   - **Green** (70-100): Healthy, active usage
   - **Yellow** (40-69): Needs attention, declining or partial usage
   - **Red** (0-39): At risk, minimal or no engagement
6. Post health dashboard to `#customer`:

```markdown
---
agent: cs-lead
type: report
severity: info
tags: [health-score, weekly]
channel: #customer
---

## Org Health Dashboard — {week}

| Org | Score | Status | Logins | Enrichments | Companies | Conversations | Error Rate |
|-----|-------|--------|--------|-------------|-----------|---------------|------------|
| ... | ...   | ...    | ...    | ...         | ...       | ...           | ...        |

### Summary
- Green: {count} orgs
- Yellow: {count} orgs
- Red: {count} orgs
```

## Output Format
Markdown table posted to `#customer` with per-org scores, color status, and metric breakdown.

## Rules
- Run weekly, same day each week for trend comparability
- Never alter raw metrics — report what the data shows
- Red orgs must be cross-referenced with churn-detect for action
- If an org has zero logins for 7+ days, score caps at 20 regardless of other metrics
