# user-insight — Synthesize User Feedback into Actionable Insights

## When to Use
Product Chief uses this weekly or when new user feedback arrives.

## Inputs
- Support tickets from `#customer`
- Bot conversation patterns (via Data Analyst)
- Usage metrics (adapter:observe.metrics)

## Procedure

1. Aggregate user feedback from all sources:
   - Support tickets — what are users asking for or complaining about?
   - Bot conversations — what do users ask the bot most?
   - Usage metrics — what features are used most? What's unused?
2. Identify patterns:
   - Features with high usage — what's working
   - Features with low usage — underutilized or undiscoverable?
   - Common pain points — where do users get stuck?
   - Feature requests — what are users explicitly asking for?
3. Synthesize into actionable insights — each insight must suggest a next step
4. Post to `#product`:

```markdown
---
agent: product-chief
type: insight
severity: info
tags: [user-feedback, product]
---

## User Insights — {date}

### Top Patterns
1. {pattern} — seen in {N sources} — **Action**: {what to do}

### Feature Usage
- Most used: {feature} ({metric})
- Least used: {feature} ({metric})

### Emerging Requests
- {request} — {N orgs asked}
```

## Rules
- Insights must be data-backed, not anecdotal
- "3 orgs asked about X" is data. "I think users want X" is not
- Always include the sample size — how many users/orgs does this represent?
- Low-usage features need investigation before removal — they might be undiscoverable, not unwanted
