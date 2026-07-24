---
name: observability-health-check
description: Hourly full-system health assessment — logs, errors, and metrics compared against rolling baselines.
---

# health-check — Full System Health Assessment

## When to Use
Obs Chief runs this every hour. DevOps can also invoke during deploy verification.

## Inputs
- `.claude/hive/config.json` + `.claude/hive/adapters/observe-*.md` — adapter config for observe.*
- `agents/core/obs-chief/context.md` — baselines for comparison

## Procedure

1. **LOGS** — Check recent production logs
   ```
   adapter:observe.logs --last 1h --severity error,warn
   ```
   - Count errors and warnings
   - Look for new error types (not seen in last 7d)
   - Check for repeated patterns (same error > 5 times)

2. **ERRORS** — Check error tracking
   ```
   adapter:observe.errors
   ```
   - New unresolved errors since last check?
   - Frequency spike on existing errors?
   - Any error affecting > 1 tenant?

3. **METRICS** — Check database and app health
   ```
   adapter:observe.metrics
   ```
   Run the metric queries defined by the adapter:
   - Active DB connections (vs baseline)
   - Error rate (vs baseline)
   - Key product metrics (as defined in config.json, vs baseline)
   - Table sizes (growth check)

4. **COMPARE** — Current vs baselines in context.md
   - Flag anything > 20% deviation
   - Flag any new error type
   - Flag connection count > 80% of pool max

5. **CLASSIFY**
   | Condition | Severity | Action |
   |-----------|----------|--------|
   | All within baseline | info | Brief "all clear" to #daily-standup |
   | One metric 20-50% off | warning | Detailed post to #daily-standup, tag relevant agent |
   | Multiple metrics off OR > 50% deviation | critical | Incident thread in #incidents + `notify.urgent` alert |

6. **OUTPUT** — Post result to appropriate GH Discussion category

7. **UPDATE** — Write latest metrics to `agents/core/obs-chief/context.md` baselines

## Output Format

### All Clear
```markdown
---
agent: obs-chief
type: report
severity: info
requires: info
---

## Health Check — {timestamp} — All Clear

| Metric | Value | Baseline | Delta |
|--------|-------|----------|-------|
| Error rate | 2.1% | 2.0% | +0.1% |
| DB connections | 12 | 11 | +1 |
| {key product metric} | 97.5% | 97.8% | -0.3% |
```

### Warning/Critical — use `capability:incident-response`
