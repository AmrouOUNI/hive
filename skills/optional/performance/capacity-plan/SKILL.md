# capacity-plan — Infrastructure Capacity Planning

## When to Use
Scale Chief uses this during monthly review or when growth signals appear.

## Inputs
- Current resource utilization (CPU, memory, DB size, connection count)
- User growth rate (historical and projected)
- Current infrastructure limits and pricing tiers

## Procedure

1. Collect current resource utilization:
   - CPU usage (average and peak)
   - Memory usage (average and peak)
   - Database size and growth rate
   - Connection count (average and peak vs max)
2. Calculate growth rate from historical data (last 3 months minimum)
3. Project when each resource will hit saturation:
   - DB connections: current / max, growth rate, months until limit
   - Disk space: current / max, growth rate, months until limit
   - CPU/Memory: current / max, growth rate, months until limit
4. Identify which bottleneck will hit first
5. Recommend action and timeline
6. Post to #scaling:

```markdown
---
agent: scale-chief
type: report
severity: {info | warning}
tags: [capacity-plan]
mentions: [@cto]
requires: ack
---

## Capacity Plan

### Current Utilization:
- CPU: {avg}% (peak: {peak}%)
- Memory: {avg}% (peak: {peak}%)
- DB Size: {size} / {limit}
- Connections: {avg} / {max} (peak: {peak})

### Growth Rate: {%/month}

### Projections:
- {resource 1}: saturates in {X months} at current growth
- {resource 2}: saturates in {X months} at current growth

### First Bottleneck: {resource} in {timeframe}
### Recommended Action: {what to do and when}
```

## Output Format
Capacity plan posted to #scaling (see template above).

## Rules
- At Stage 2, capacity plan is informational — give CTO time to plan
- At Stage 3+, capacity plan becomes actionable with budget thresholds
- Always present as "we have X months before Y becomes a problem"
- Never alarm without data — projections must be based on measured growth, not speculation
