---
name: strategy-roadmap
description: Maintain the quarterly roadmap during weekly sprint planning and monthly review.
---

# roadmap — Maintain Quarterly Roadmap

## When to Use
CTO uses this weekly (sprint planning) and monthly (roadmap review).

## Inputs
- Current roadmap from latest `#roadmap` post
- Completed work from `#daily-standup`
- New proposals from `#features`
- Priority scores from `prioritize` skill
- Customer signals from `#customer`

## Procedure

1. Read current roadmap state
2. Mark completed items
3. Adjust priorities based on new data (market signals, incidents, customer feedback)
4. Post updated roadmap to `#roadmap`

## Output Format

```markdown
---
agent: cto
type: decision
severity: info
tags: [roadmap]
requires: info
---

## Roadmap — Q{n} {year}

### Now (this sprint)
- [ ] {item} — assigned to {agent} — {status}

### Next (next 2 sprints)
- [ ] {item} — {priority score} — {reason}

### Later (this quarter)
- [ ] {item} — {priority score}

### Icebox (good ideas, not yet prioritized)
- {item} — source: {who proposed it}

### Completed This Quarter
- [x] {item} — shipped {date}
```

## Rules
- Roadmap is updated weekly, not daily
- "Now" has max 3 items — focus beats breadth
- "Next" has max 5 items
- Items in Icebox for > 2 months → archive or promote
