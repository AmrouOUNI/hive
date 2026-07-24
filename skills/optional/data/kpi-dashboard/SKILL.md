# kpi-dashboard — Daily KPI Dashboard

## When to Use
Data Analyst uses this daily to collect and format all key performance indicators with trend comparisons.

## Inputs
- Product metrics: active orgs, enrichments/day, conversations/day
- Engineering metrics: error rate, test coverage, deploy frequency
- Cost metrics: LLM cost
- Incident metrics: incident count

## Procedure

1. Collect all KPIs for today:
   - **Active orgs**: Count of orgs with login in last 7 days
   - **Enrichments/day**: Total enrichments run today
   - **Conversations/day**: Total conversations held today
   - **Error rate**: Percentage of failed operations
   - **LLM cost**: Total spend on LLM API calls today
   - **Test coverage**: Current coverage percentage
   - **Deploy frequency**: Deploys in last 24h
   - **Incident count**: Open incidents
2. Compare each KPI against:
   - Yesterday (daily trend)
   - Same day last week (weekly trend)
3. Format as dashboard with directional indicators
4. Post to `#daily-standup`:

```markdown
---
agent: data-analyst
type: dashboard
severity: info
tags: [kpi-dashboard, daily]
channel: #daily-standup
---

## KPI Dashboard — {date}

| KPI | Today | vs Yesterday | vs Last Week |
|-----|-------|-------------|--------------|
| Active Orgs | {n} | {+/-n} | {+/-n} |
| Enrichments | {n} | {+/-n} | {+/-n} |
| Conversations | {n} | {+/-n} | {+/-n} |
| Error Rate | {n}% | {+/-n}% | {+/-n}% |
| LLM Cost | ${n} | {+/-$n} | {+/-$n} |
| Test Coverage | {n}% | {+/-n}% | {+/-n}% |
| Deploys (24h) | {n} | {+/-n} | {+/-n} |
| Open Incidents | {n} | {+/-n} | {+/-n} |

### Alerts
- {any KPI with >20% change flagged here}
```

## Output Format
Markdown dashboard table posted to `#daily-standup` with today's values and trend comparisons.

## Rules
- Post daily, every day — consistency matters for trend tracking
- Flag any KPI with >20% change vs yesterday as an alert
- Error rate increasing + deploys increasing = likely deployment issue — note the correlation
- Never round numbers in ways that hide meaningful changes
- If a data source is unavailable, note it as "N/A" rather than skipping the row
