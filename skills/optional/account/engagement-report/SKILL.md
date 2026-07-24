# engagement-report — Weekly Per-Org Engagement Report

## When to Use
Account Mgr uses this weekly to produce a per-org engagement summary with lifecycle classification.

## Inputs
- Org list from database
- Per-org metrics: last login, enrichments this week, companies added, conversations held, features used
- Onboarding completion status

## Procedure

1. Pull all orgs and their weekly activity data
2. For each org, collect:
   - Last login timestamp
   - Total enrichments this week
   - Companies added this week
   - Conversations held this week
   - Distinct features used this week
3. Classify lifecycle stage:
   - **Onboarding**: Signed up <14 days ago, not all milestones complete
   - **Active**: Regular usage (logged in within 7 days, using 2+ features)
   - **At-risk**: Logged in but declining usage or partial feature adoption
   - **Churned**: No login for 14+ days
4. Post per-org report to `#customer`:

```markdown
---
agent: account-mgr
type: report
severity: info
tags: [engagement-report, weekly]
channel: #customer
---

## Engagement Report — {week}

| Org | Last Login | Enrichments | Companies | Conversations | Features Used | Stage |
|-----|-----------|-------------|-----------|---------------|---------------|-------|
| ... | ...       | ...         | ...       | ...           | ...           | ...   |

### Stage Summary
- Onboarding: {count}
- Active: {count}
- At-risk: {count}
- Churned: {count}
```

## Output Format
Markdown table posted to `#customer` with per-org metrics and lifecycle stage classification.

## Rules
- Report every org, not just active ones — churned orgs need visibility
- Lifecycle stage must be consistent with health-score and churn-detect outputs
- Never omit an org from the report even if they have zero activity
- Compare with previous week to note direction of change
