# cohort-analysis — Monthly Cohort Engagement Analysis

## When to Use
CS Lead uses this monthly to compare engagement across org signup cohorts and identify retention patterns.

## Inputs
- Org signup dates
- Monthly engagement metrics per org (logins, enrichments, companies, conversations)
- Org metadata (industry, size, acquisition channel if available)

## Procedure

1. Group all orgs by signup month into cohorts
2. For each cohort, calculate month-over-month engagement metrics:
   - Average logins per org
   - Average enrichments per org
   - Average companies created per org
   - Retention rate (% still active)
3. Build a cohort retention matrix — rows = cohort month, columns = months since signup
4. Segment cohorts by available attributes (industry, team size, acquisition channel) and compare retention across segments
5. Identify which cohort types retain best and worst
6. Post analysis to `#customer` + `#product`:

```markdown
---
agent: cs-lead
type: report
severity: info
tags: [cohort-analysis, monthly]
channels: [#customer, #product]
---

## Cohort Analysis — {month}

### Retention Matrix
| Cohort | Month 1 | Month 2 | Month 3 | ... |
|--------|---------|---------|---------|-----|
| ...    | ...     | ...     | ...     | ... |

### Segment Comparison
| Segment | Avg Retention (3mo) | Avg Enrichments | Notes |
|---------|---------------------|-----------------|-------|
| ...     | ...                 | ...             | ...   |

### Key Findings
- {finding 1}
- {finding 2}
```

## Output Format
Markdown report with retention matrix, segment comparison table, and key findings. Posted to `#customer` and `#product`.

## Rules
- Minimum 3 orgs per cohort to report — smaller cohorts noted but not statistically meaningful
- Never draw causal conclusions — report correlations only
- Compare against previous month's analysis to show trend direction
- Attribute data may be sparse — note data gaps explicitly
