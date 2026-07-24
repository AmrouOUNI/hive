---
name: ceremonies-standup-run
description: Daily standup — aggregate all agent activity from the last 24 hours into a unified post.
---

# standup-run — Run Daily Standup

## When to Use
Scrum Master runs this daily at 8:30. Aggregates all agent activity from the last 24 hours into a unified standup post.

## Inputs
- All `agents/*/context.md` files (current status, active assignments, blockers)
- Last 24h of GH Discussions across all channels
- Previous standup post from `#daily-standup`

## Procedure

1. Read every `agents/*/context.md` — extract current status, active task, blockers, and completed items
2. Read GH Discussions from the last 24h — note new threads, resolved threads, pending decisions
3. Aggregate into unified standup with these sections:
   - **Critical Items** — blockers, waiting-human, overdue tasks
   - **In Progress** — what each agent is actively working on
   - **Completed (last 24h)** — finished tasks with links
   - **Proposed** — new discussions or ideas awaiting triage
   - **Metrics** — open threads count, avg resolution time, blocker count
4. Post standup to `#daily-standup`:

```markdown
---
agent: scrum-master
type: ceremony
severity: info
tags: [standup, daily]
mentions: []
requires: read
---

## Daily Standup — {date}

### Critical Items
{list or "None"}

### In Progress
{agent: task summary, priority}

### Completed (last 24h)
{agent: task summary, link}

### Proposed
{new discussion summaries}

### Metrics
- Open threads: {n}
- Blockers: {n}
- Avg resolution time: {duration}
```

5. If any items require human decisions, tag the relevant human in the post

## Output Format
Single post to `#daily-standup` in the template above.

## Rules
- Run every day, no exceptions — even if nothing changed, post a "no updates" standup
- Never fabricate progress — only report what is in context.md or discussions
- Tag human only for items marked `waiting-human` or decisions pending > 24h
- Keep each line item to one sentence max
