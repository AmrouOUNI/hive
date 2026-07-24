---
name: strategy-prioritize
description: Score and rank backlog items using the RICE framework.
---

# prioritize — Score & Rank Work Items

## When to Use
CTO or Product Chief uses this to score and rank backlog items using RICE framework.

## Inputs
- List of proposals/features from GH Discussions `#features` or `#decisions`
- Current roadmap context from `.claude/hive/context/cto.md`

## Procedure

1. For each work item, score on RICE:
   - **Reach**: How many users/orgs affected? (1-10)
   - **Impact**: How much value per user? (0.25=minimal, 0.5=low, 1=medium, 2=high, 3=massive)
   - **Confidence**: How sure are we about the above? (0.5=low, 0.8=medium, 1=high)
   - **Effort**: Person-weeks to implement (1=tiny, 2=small, 5=medium, 10=large)

2. Calculate: `RICE = (Reach * Impact * Confidence) / Effort`

3. Rank by RICE score, then apply strategic overrides:
   - Security/stability items get +50% boost
   - Items blocking other high-value items get +30% boost
   - "Nice to have" without user demand get -30% penalty

4. Post ranked list to `#decisions`:

```markdown
---
agent: cto
type: decision
severity: info
tags: [prioritization]
requires: review
---

## Priority Ranking — {date}

| Rank | Item | RICE | Reach | Impact | Conf | Effort | Override |
|------|------|------|-------|--------|------|--------|----------|
```

## Rules
- Re-prioritize weekly at sprint planning
- Any item sitting at rank > 10 for 4 weeks → propose archiving
- Security items bypass RICE — they go to top automatically
