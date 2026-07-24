---
name: ceremonies-retrospective
description: Weekly sprint retrospective — reflect on process and propose improvements.
---

# retrospective — Run Sprint Retrospective

## When to Use
Scrum Master runs this Friday at 15:00 (after sprint review). Reflects on the week's process and proposes improvements.

## Inputs
- All GH Discussions from the week
- Sprint review results (just posted)
- Agent context files for blocker history
- Previous retrospective's action items (to check follow-through)

## Procedure

1. Check last retro's action items — were they followed through? Mark each as done/not done
2. Read all GH Discussions from the week — identify patterns in how work flowed
3. Identify **what went well** — fast resolutions, good catches, smooth handoffs, effective patterns
4. Identify **what didn't work** — stale discussions (>48h no resolution), missed deadlines, agent conflicts, communication gaps
5. Identify **surprises** — unexpected blockers, scope changes, incidents
6. Propose 1-3 actionable improvements — each must be specific, assignable, and measurable
7. Post retro to `#decisions`:

```markdown
---
agent: scrum-master
type: ceremony
severity: info
tags: [retrospective, weekly]
mentions: [@cto]
requires: read
---

## Retrospective — Sprint {number}

### Previous Action Items
| Action | Status |
|--------|--------|
| {action from last retro} | {done / not done} |

### What Went Well
- {specific positive with example}

### What Didn't Work
- {specific issue with evidence}

### Surprises
- {unexpected events}

### Action Items
1. {specific improvement — owner — deadline}
2. {specific improvement — owner — deadline}
3. {specific improvement — owner — deadline}
```

## Output Format
Single post to `#decisions` in the template above.

## Rules
- Max 3 action items — focus beats volume
- Every "didn't work" must cite evidence (a thread, a date, a metric) — no vague complaints
- Action items must name an owner — "we should" is not actionable
- Never skip checking last retro's action items — accountability matters
