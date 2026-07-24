# outreach-draft — Draft Outreach Message for Human Approval

## When to Use
Account Mgr uses this when outreach is needed: onboarding check-in, re-engagement for at-risk org, milestone celebration, or feature highlight.

## Inputs
- Org name and contact info
- Outreach reason (onboarding, re-engagement, milestone, feature-highlight)
- Org usage data (from engagement-report or health-score)
- Specific value delivered or available

## Procedure

1. Identify the outreach context — why are we reaching out?
2. Pull the org's recent usage data for personalization
3. Draft message with:
   - **Personal greeting**: Use org/contact name
   - **Usage reference**: Mention specific data (e.g., "You've enriched 47 companies this week")
   - **Value statement**: What they got or could get from the product
   - **Clear CTA**: One specific next step (e.g., "Try the conversation feature", "Book a 15-min call")
4. Submit draft for human approval (Level 2 — never auto-send):

```markdown
---
agent: account-mgr
type: draft
severity: info
tags: [outreach-draft]
requires: approval
level: 2
---

## Outreach Draft — {org name}

**Reason**: {onboarding | re-engagement | milestone | feature-highlight}
**Channel**: {email | in-app | chat}

### Message

{drafted message}

### Context
- Health score: {score}
- Last login: {date}
- Key metric: {relevant data point}

**Awaiting human approval before sending.**
```

## Output Format
Markdown draft with message body, context, and approval gate. Never sent automatically.

## Rules
- NEVER send outreach without human approval — always Level 2
- Keep messages under 150 words — respect the reader's time
- Always reference real usage data — never use generic templates
- One CTA per message — multiple CTAs reduce action
- Match tone to the situation: celebratory for milestones, helpful for re-engagement, welcoming for onboarding
