# perf-audit — Database and API Performance Audit

## When to Use
Scale Chief uses this during the every-4h check or weekly deep dive.

## Inputs
- Access to `pg_stat_statements` and `pg_stat_user_tables`
- Database connection pool stats
- Current query performance baselines

## Procedure

1. Query `pg_stat_statements` for slow queries (> 100ms mean time)
2. Run `EXPLAIN ANALYZE` on the top 5 slowest queries
3. Check for sequential scans on large tables (> 10k rows)
4. Check index usage stats — identify unused indexes (waste write performance)
5. Review connection pool stats (active, idle, waiting)
6. Categorize findings by urgency:
   - Fix now: queries > 500ms
   - Optimize soon: queries 100-500ms
   - Monitor: queries 50-100ms
7. Post findings to #scaling:

```markdown
---
agent: scale-chief
type: report
severity: {info | warning | critical}
tags: [perf-audit]
mentions: [{@sr-backend if fix-now items found}]
requires: {ack | action}
---

## Performance Audit

### Slow Queries (> 100ms): {count}
### Fix Now (> 500ms):
{list with query template, mean time, call count}
### Optimize Soon (100-500ms):
{list with query template, mean time, call count}
### Sequential Scans on Large Tables: {list}
### Unused Indexes: {list}
### Connection Pool: {active}/{max} ({saturation %}%)
```

## Output Format
Performance audit report posted to #scaling (see template above).

## Rules
- N+1 queries are zero tolerance at ANY maturity stage
- Slow queries get categorized: fix now (> 500ms), optimize soon (100-500ms), monitor (50-100ms)
- Unused indexes should be flagged for removal — they slow down writes
- Tag @sr-backend with specific query + table + suggested fix for "fix now" items
