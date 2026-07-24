# onboard-check — Verify New Org Onboarding Completion

## When to Use
Account Mgr uses this on new org signup to verify onboarding milestones are met at 24h, 72h, and 7d checkpoints.

## Inputs
- Org ID (newly signed up)
- Signup timestamp
- Database state for the org

## Procedure

1. Identify the org and its signup timestamp
2. Determine which checkpoint applies (24h, 72h, or 7d post-signup)
3. Check onboarding milestones:
   - **Org created in DB**: row exists in organizations table
   - **At least 1 professional linked**: professional record associated with org
   - **Soul configured**: org_soul record exists with non-default values
   - **First conversation started**: at least 1 conversation record
   - **First company captured**: at least 1 company record
4. Score completion: count of milestones met out of 5
5. If incomplete at 7d checkpoint, flag as churn risk to CS Lead
6. Post onboarding status to `#customer`:

```markdown
---
agent: account-mgr
type: report
severity: info
tags: [onboard-check]
channel: #customer
---

## Onboarding Check — {org name} ({checkpoint})

| Milestone             | Status |
|-----------------------|--------|
| Org created           | ...    |
| Professional linked   | ...    |
| Soul configured       | ...    |
| First conversation    | ...    |
| First company captured| ...    |

**Completion: {x}/5**
**Action needed: {yes/no — details if yes}**
```

## Output Format
Markdown checklist posted to `#customer` with per-milestone status and completion score.

## Rules
- Run at 24h, 72h, and 7d — each checkpoint is progressively stricter
- At 24h: org + professional expected minimum
- At 72h: soul + first conversation expected
- At 7d: all 5 milestones expected — incomplete = churn risk flag
- Never skip the 7d check even if earlier checks passed
