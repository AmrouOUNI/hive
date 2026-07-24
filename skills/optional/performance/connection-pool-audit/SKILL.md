# connection-pool-audit — Database Connection Pool Audit

## When to Use
Scale Chief uses this during the every-4h check or when connection errors appear in logs.

## Inputs
- `pg_stat_activity` data
- Connection pool configuration (max connections, idle timeout)
- Recent error logs (connection refused, pool exhausted)

## Procedure

1. Query `pg_stat_activity` for active and idle connections
2. Check pool configuration:
   - Max connections setting
   - Idle timeout setting
   - Connection lifetime setting
3. Calculate saturation percentage: active / max
4. Check for connection leaks:
   - Connections idle for > 10 minutes in application context
   - Connections in "idle in transaction" state for > 5 minutes
5. Post findings to #scaling:

```markdown
---
agent: scale-chief
type: report
severity: {info | warning | critical}
tags: [connection-pool]
requires: {ack | action}
---

## Connection Pool Audit

### Pool Config: max={max}, idle_timeout={timeout}
### Active Connections: {count}
### Idle Connections: {count}
### Saturation: {%}
### Idle in Transaction: {count} (longest: {duration})
### Suspected Leaks: {count} (idle > 10min)
### Status: {healthy | warning | critical}
```

## Output Format
Connection pool audit report posted to #scaling (see template above).

## Rules
- Pool saturation > 70% is a warning
- Pool saturation > 85% is critical — recommend scaling or leak investigation
- Connections idle > 10 minutes in application context are suspected leaks — investigate
- "Idle in transaction" > 5 minutes is always a bug — tag @sr-backend
