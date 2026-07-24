---
name: strategy-decision
description: Structure a proposal as an RFC, collect agent votes, and resolve or escalate the decision.
---

# decision — Structure an RFC, Collect Votes, Resolve

## When to Use
CTO uses this when a proposal needs formal review from multiple agents before approval.

## Inputs
- Proposal from any agent (GH Discussion)
- List of agents whose input is needed

## Procedure

1. Create RFC discussion in `#decisions`:

```markdown
---
agent: cto
type: decision
severity: info
tags: [rfc]
mentions: [@agent1, @agent2, ...]
requires: review
---

## RFC: {title}

### Context
{Why this decision is needed}

### Proposal
{What's being proposed — from the original discussion}

### Options
| Option | Pro | Con |
|--------|-----|-----|

### Input Needed From
- [ ] @architect — architecture impact
- [ ] @sec-chief — security implications
- [ ] @{other} — {what you need from them}

### Decision Deadline: {date}
```

2. Wait for tagged agents to respond (check on next cycle)
3. Tally responses:
   - If consensus → label `approved` or `rejected`
   - If split → CTO makes the call, posts rationale
   - If Level 3 (human needed) → send approval request via `adapter:notify.primary`

4. Post final decision as comment on the RFC thread

## Rules
- Deadline is 48h for non-urgent, 4h for urgent
- If an agent doesn't respond by deadline, proceed without their input
- Always document the WHY of the decision, not just the WHAT
