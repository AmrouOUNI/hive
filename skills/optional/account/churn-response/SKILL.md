# churn-response — Generate Retention Action Plan

## When to Use
Account Mgr uses this when CS Lead flags an org as churn risk via churn-detect. Analyzes the specific situation and produces a retention plan for human approval.

## Inputs
- Churn risk alert from CS Lead (org ID, risk level, signals)
- Org's full usage history
- Error logs affecting the org (if applicable)
- Onboarding status

## Procedure

1. Read the churn risk alert — identify which signals triggered it
2. Deep-dive into the org's situation:
   - **What they stopped doing**: Compare current vs peak usage
   - **When the decline started**: Pinpoint the inflection point
   - **Any errors affecting them**: Check if bugs or outages impacted their experience
   - **Onboarding gaps**: Did they miss key setup steps?
3. Generate a retention action plan tailored to the root cause:
   - If errors caused it: prioritize bug fix, draft apology + fix notification
   - If onboarding gap: draft guided walkthrough offer
   - If feature gap: highlight underused features or upcoming ones
   - If unknown: draft check-in message asking what went wrong
4. Submit plan for human approval:

```markdown
---
agent: account-mgr
type: plan
severity: warning
tags: [churn-response]
requires: approval
level: 2
---

## Retention Plan — {org name}

### Situation Analysis
- **Risk level**: {high | medium}
- **Stopped doing**: {specific behaviors}
- **Decline started**: {date/week}
- **Root cause hypothesis**: {errors | onboarding gap | feature gap | unknown}

### Action Plan
1. {action 1 — e.g., fix bug X affecting their enrichments}
2. {action 2 — e.g., send re-engagement email highlighting feature Y}
3. {action 3 — e.g., offer guided walkthrough call}

### Draft Message
{if outreach is part of the plan, include draft here}

**Awaiting human approval before execution.**
```

## Output Format
Markdown plan with situation analysis, action steps, and optional draft message. Requires human approval.

## Rules
- Never execute retention actions without human approval
- Always check error logs — if bugs caused the churn signal, fixing the bug is step 1
- Be specific about what the org stopped doing — "declining usage" is not actionable
- Retention plan must have max 3 actions — focus beats breadth
