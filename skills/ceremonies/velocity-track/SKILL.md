---
name: ceremonies-velocity-track
description: Track team throughput over time to inform planning and spot trends.
---

# velocity-track — Track Sprint Velocity

## When to Use
Scrum Master runs this weekly (after sprint review). Tracks team throughput over time to inform planning and spot trends.

## Inputs
- Sprint review data from the current and last 3 sprints
- Blocker/incident logs from the same period
- Sprint plans (to compare planned vs actual)

## Procedure

1. Count tasks completed in the current sprint (from sprint review)
2. Pull completion counts from the last 3 sprints for comparison
3. Calculate velocity trend — is it increasing, stable, or declining?
4. Correlate velocity changes with events — incidents, blockers, capacity changes, scope creep
5. Compute per-agent throughput if data is available
6. Post velocity report to `#daily-standup`:

```markdown
---
agent: scrum-master
type: ceremony
severity: info
tags: [velocity, metrics, weekly]
mentions: []
requires: read
---

## Velocity Report — Sprint {number}

### Sprint Velocity
| Sprint | Planned | Completed | Rate |
|--------|---------|-----------|------|
| {current} | {n} | {n} | {%} |
| {n-1} | {n} | {n} | {%} |
| {n-2} | {n} | {n} | {%} |
| {n-3} | {n} | {n} | {%} |

### Trend
{increasing / stable / declining} — {explanation}

### Correlation
- Incidents this sprint: {n}
- Blockers this sprint: {n}
- Capacity changes: {notes}

### Observations
- {insight about what affects velocity}
- {recommendation for next sprint planning}
```

## Output Format
Single post to `#daily-standup` in the template above.

## Rules
- Always show at least 4 sprints for trend — single-sprint data is noise
- Never use velocity to pressure agents — it is a planning tool, not a performance metric
- If data is missing for a past sprint, mark it as "N/A" — never estimate
- Correlations are hypotheses, not conclusions — state them as such
