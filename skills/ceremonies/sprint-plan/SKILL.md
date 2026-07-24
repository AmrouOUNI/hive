---
name: ceremonies-sprint-plan
description: Weekly sprint planning — review the previous sprint, set goals, publish the plan.
---

# sprint-plan — Run Sprint Planning

## When to Use
Scrum Master + CTO run this Monday at 9am. Reviews the previous sprint, sets goals for the new sprint, and publishes the plan.

## Inputs
- Previous sprint's completion data (from `#daily-standup` history)
- CTO's priority list from `#roadmap`
- All `agents/*/context.md` for current capacity
- Carry-over items from last sprint (incomplete tasks)

## Procedure

1. Review last sprint completion rate — count planned vs completed tasks from last sprint's plan post
2. Identify carry-over items — any planned tasks that were not completed, with reason for incompletion
3. Read CTO's priority list from `#roadmap` — extract ranked priorities
4. Check agent capacity — read `agents/*/context.md` for availability and current load
5. Select sprint goals — max 3 items tagged "Now", plus carry-overs if still relevant
6. Assign items to agents based on skill match and availability
7. Post sprint plan to `#daily-standup` and `#roadmap`:

```markdown
---
agent: scrum-master
type: ceremony
severity: info
tags: [sprint-plan, weekly]
mentions: [@cto]
requires: read
---

## Sprint Plan — {sprint number} ({start date} - {end date})

### Last Sprint Review
- Planned: {n} | Completed: {n} | Completion rate: {%}
- Carry-overs: {list or "None"}

### Sprint Goals
1. {goal — assigned agent — priority}
2. {goal — assigned agent — priority}
3. {goal — assigned agent — priority}

### Stretch Goals
{items that can be picked up if main goals finish early}

### Capacity Notes
{any agents unavailable, reduced capacity, etc.}
```

## Output Format
Single post to `#daily-standup` and `#roadmap` in the template above.

## Rules
- Max 3 "Now" items per sprint — overloading kills velocity
- Carry-overs must include a reason — "not completed" is not enough
- Never assign work to an agent already at capacity without CTO approval
- Sprint length is always 1 week (Monday to Friday)
