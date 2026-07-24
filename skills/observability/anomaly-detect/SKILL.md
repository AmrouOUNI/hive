---
name: observability-anomaly-detect
description: Compare current metrics to rolling baselines and flag deviations.
---

# anomaly-detect — Compare Metrics to Baseline, Flag Deviations

## When to Use
Obs Chief uses this as part of every health-check cycle. Can also be invoked standalone.

## Inputs
- Current metrics (from health-check)
- `.claude/hive/context/obs-chief.md` — 7d rolling baselines

## Procedure

1. For each tracked metric:
   ```
   deviation = abs(current - baseline) / baseline * 100
   ```

2. Classify:
   | Deviation | Classification |
   |-----------|---------------|
   | < 10% | Normal — no action |
   | 10-20% | Watch — note in context.md, check next cycle |
   | 20-50% | Warning — post to #daily-standup, tag relevant agent |
   | > 50% | Critical — trigger incident-triage |

3. Check for compound anomalies:
   - Multiple metrics off simultaneously → likely systemic issue → escalate immediately
   - Single metric off → likely isolated → investigate before escalating

4. Update context.md baselines:
   - Rolling 7d average (exclude incident periods)
   - Don't let a single spike corrupt the baseline

## Rules
- Never ignore a > 20% deviation, even if it "looks like nothing"
- If a metric stays 10-20% off for 3+ consecutive checks → promote to warning
- After an incident, reset baseline after 24h of stability
