---
name: ceremonies-sprint-review
description: Compile everything shipped during the sprint and report plan vs actual.
---

# sprint-review — Run Sprint Review

## When to Use
Scrum Master runs this Friday at 14:00. Compiles everything shipped during the sprint and reports on plan vs actual.

## Inputs
- Sprint plan from Monday (the goals that were set)
- All agent progress posts from `#daily-standup` this week
- GH Discussions resolved this sprint
- Test results and build status

## Procedure

1. Read the sprint plan from Monday — extract the committed goals
2. Scan all `#daily-standup` posts from this week — collect completed items per agent
3. For each completed item, gather: description, agent, test results (pass/fail), link to PR or discussion
4. Identify planned items that were NOT completed — note the reason (blocked, deprioritized, underestimated)
5. List any unplanned work that was completed (hotfixes, urgent requests)
6. Post review to `#daily-standup`:

```markdown
---
agent: scrum-master
type: ceremony
severity: info
tags: [sprint-review, weekly]
mentions: []
requires: read
---

## Sprint Review — {sprint number} ({date range})

### Shipped
| Item | Agent | Tests | Link |
|------|-------|-------|------|
| {description} | {agent} | {pass/fail} | {link} |

### Not Completed
| Item | Reason | Carry Over? |
|------|--------|-------------|
| {description} | {reason} | {yes/no} |

### Unplanned Work
{list of hotfixes or urgent items handled outside the plan}

### Summary
- Planned: {n} | Shipped: {n} | Completion rate: {%}
- Unplanned items handled: {n}
```

## Output Format
Single post to `#daily-standup` in the template above.

## Rules
- Every shipped item must have test status — no "assumed passing"
- Unplanned work is not a failure, but it must be visible
- Reasons for incompletion must be specific — "ran out of time" requires what consumed the time
- This is a factual report, not a judgment — save opinions for retrospective
