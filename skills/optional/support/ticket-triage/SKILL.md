# ticket-triage — Classify and Route Incoming Support Issues

## When to Use
Support uses this on every new incoming issue. Classifies the type, assigns severity, and routes to the correct handler.

## Inputs
- Incoming issue (ticket text, reporter info, org context)

## Procedure

1. Read the ticket — extract: what happened, what was expected, any error messages, org/user context
2. Classify type:
   - **Bug**: Something is broken or producing incorrect results
   - **Question**: How-to, usage guidance, clarification
   - **Feature-request**: Wants something new or different
   - **Billing**: Payment, plan, subscription related
3. Assign severity:
   - **P0**: Service down, all users affected, data loss
   - **P1**: Major feature broken, significant user impact
   - **P2**: Minor issue, workaround exists
   - **P3**: Cosmetic, question, low impact
4. Route based on classification:
   - Bugs → `sr-backend` (with severity tag)
   - Questions → `auto-resolve` skill
   - Feature requests → `product-chief`
   - Billing → human only (never auto-handle)
5. Post triage result:

```markdown
---
agent: support
type: triage
severity: {P0|P1|P2|P3}
tags: [ticket-triage, {type}]
channel: #customer
mentions: [@{routed-agent}]
requires: action
---

## Ticket Triage — #{ticket-id}

- **Reporter**: {org / user}
- **Type**: {bug | question | feature-request | billing}
- **Severity**: {P0 | P1 | P2 | P3}
- **Summary**: {1-2 sentence summary}
- **Routed to**: {agent or skill}
```

## Output Format
Markdown triage card posted to `#customer` with classification, severity, and routing destination.

## Rules
- P0 issues must also post to `#incidents` immediately
- Billing issues always go to humans — never auto-resolve billing
- When in doubt about severity, round up (P2 not P3)
- If a bug affects multiple orgs, escalate to P1 minimum
- Never close a ticket during triage — triage classifies and routes only
