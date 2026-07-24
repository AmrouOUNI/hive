# escalate — Package and Route Unresolved Issues

## When to Use
Support uses this when an issue cannot be resolved at the support level. Packages all context and routes to the correct agent.

## Inputs
- Original ticket
- Triage classification (from ticket-triage)
- Resolution attempts (what was tried)
- Reproduction steps (if available)

## Procedure

1. Package the issue with full context:
   - Original report (verbatim)
   - Triage classification and severity
   - What was attempted to resolve it
   - Reproduction steps if available
   - Affected org/user details
2. Route to the correct handler:
   - **Bugs** → `sr-backend` + create GH Issue
   - **Security** → `sec-chief`
   - **Infra** → `devops`
   - **Billing** → human (tag in `#customer`)
3. Post escalation to the appropriate channel:
   - Bugs → `#incidents`
   - All other escalations → `#customer`

```markdown
---
agent: support
type: escalation
severity: {from triage}
tags: [escalate, {type}]
channel: {#incidents | #customer}
mentions: [@{target-agent}]
requires: action
---

## Escalation — #{ticket-id}

### Original Report
{verbatim ticket content}

### Triage
- **Type**: {bug | security | infra | billing}
- **Severity**: {P0 | P1 | P2 | P3}

### What Was Tried
- {attempt 1}
- {attempt 2}

### Reproduction Steps
{steps if available, or "Not yet reproduced"}

### Routed To
{agent} — {reason}
```

## Output Format
Markdown escalation card posted to `#incidents` (bugs) or `#customer` (other). GH Issue created for bugs.

## Rules
- Always include what was already tried — prevents duplicate work
- Bug escalations must create a GH Issue with reproduction steps
- P0 escalations must also notify in `#incidents` regardless of type
- Never escalate without attempting resolution first (unless P0)
- Billing escalations always go to humans — never to agents
