---
name: strategy-dispatch
description: Turn an approved proposal into assigned work — pick an agent, set priority, track the assignment.
---

# dispatch — Assign Work to Agents

## When to Use
CTO uses this when an approved proposal needs to become work — assign an agent, create a worktree if needed, set priority.

## Inputs
- Approved GH Discussion (labeled `approved`)
- Available agents (check `agents/*/context.md` for idle agents)
- plan.md if exists

## Procedure

1. Read the approved discussion — extract: what needs to be done, who's best suited, urgency
2. Check target agent's `context.md` — are they idle or busy?
3. If coding work → create worktree: `git worktree add ~/worktrees/{project}/{branch}`
4. Post dispatch order to `#decisions`:

```markdown
---
agent: cto
type: decision
severity: info
tags: [dispatch]
mentions: [@{target-agent}]
requires: action
---

## Dispatch: {task summary}

### Assigned to: {agent}
### Priority: {high | medium | low}
### Source: {link to approved discussion}
### Plan: {link to plan.md if exists}
### Worktree: {path if created}
### Deadline: {if applicable}
```

5. Update `.claude/hive/context/cto.md` Active Assignments table

## Rules
- Never dispatch to an agent already working on a high-priority task
- Coding agents (sr-backend, sr-ai) always get worktrees
- Observation agents (obs-chief, sec-chief) work in-place (read-only)
- If no agent is available, queue the work and flag in next standup
