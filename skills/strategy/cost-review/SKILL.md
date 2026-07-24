---
name: strategy-cost-review
description: Weekly and monthly review of all service costs — LLM usage, hosting, database, external services — with trend comparison and overrun flags.
---

# cost-review — Review All Costs

## When to Use
CTO runs this weekly (during sprint planning) and monthly (roadmap review).

## Inputs
- `adapter:observe.metrics` — LLM token usage, infra costs
- Previous cost-review output (for trend comparison)

## Procedure

1. Collect cost data:
   - Claude Code scheduled tasks (API usage)
   - LLM APIs consumed by the product (if any)
   - Hosting
   - Database, storage, auth
   - External services (per the service list in `.claude/hive/config.json`)

2. Compare to last period

3. Flag overruns (> $10/day on any service without prior approval)

4. Post to `#daily-standup` (weekly) or `#roadmap` (monthly)

## Output Format

```markdown
---
agent: cto
type: report
severity: info
tags: [costs]
requires: info
---

## Cost Review — {period}

| Service | Daily Avg | Previous | Delta | Status |
|---------|-----------|----------|-------|--------|
| Claude Code (Hive) | ${n} | ${n} | {%} | {emoji} |
| LLM APIs (product) | ${n} | ${n} | {%} | {emoji} |
| Hosting | ${n} | ${n} | {%} | {emoji} |
| Database | ${n} | ${n} | {%} | {emoji} |
| {external service} | ${n} | ${n} | {%} | {emoji} |
| **Total** | **${n}** | **${n}** | **{%}** | |

### Flags
- {any overrun or concerning trend}

### Recommendations
- {cost optimization suggestions}
```
