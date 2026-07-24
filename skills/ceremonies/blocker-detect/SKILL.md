---
name: ceremonies-blocker-detect
description: Proactively scan for stuck work and escalate blockers before they stall the sprint.
---

# blocker-detect — Detect and Escalate Blockers

## When to Use
Scrum Master runs this as part of standup and at end-of-day. Proactively scans for stuck work and escalates before it stalls the sprint.

## Inputs
- All `agents/*/context.md` (status fields)
- GH Discussions (all channels)
- Approval queue

## Procedure

1. Scan every `agents/*/context.md` — flag any agent with status `blocked` or `waiting-human`
2. Scan GH Discussions — flag any thread older than 48h with no resolution or response
3. Scan approval queue — flag any item pending longer than 2h
4. For each blocker found, classify:
   - **Agent-blocked** — agent cannot proceed, needs input or decision
   - **Stale discussion** — thread went cold, needs a nudge or escalation
   - **Approval bottleneck** — work queued waiting for sign-off
5. Post blocker list to `#daily-standup`, tag CTO for resolution:

```markdown
---
agent: scrum-master
type: alert
severity: warning
tags: [blocker, escalation]
mentions: [@cto]
requires: action
---

## Blocker Report — {date} {time}

### Agent Blockers
| Agent | Task | Blocked Since | Reason |
|-------|------|---------------|--------|
| {agent} | {task} | {timestamp} | {reason} |

### Stale Discussions (>48h)
| Thread | Channel | Last Activity | Topic |
|--------|---------|---------------|-------|
| {link} | {channel} | {timestamp} | {summary} |

### Approval Queue (>2h)
| Item | Submitted By | Waiting Since |
|------|-------------|---------------|
| {item} | {agent} | {timestamp} |

### Recommended Actions
1. {specific suggestion for resolving each blocker}
```

## Output Format
Single post to `#daily-standup` in the template above. If no blockers found, post a one-line "No blockers detected" update.

## Rules
- Run at minimum twice daily — during standup and at EOD
- Always tag CTO when blockers exist — silent blockers are the worst kind
- Include recommended actions — don't just report problems, suggest solutions
- 48h stale threshold and 2h approval threshold are defaults — adjust if CTO overrides
