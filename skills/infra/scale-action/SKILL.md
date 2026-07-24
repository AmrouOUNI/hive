---
name: infra-scale-action
description: Execute an infrastructure scaling change with cost check, approval gate, and capacity verification.
---

# scale-action — Execute Infrastructure Scaling

## When to Use
DevOps uses this when a scaling need is identified (by the scale-chief agent if enabled — see config.json `agents`), or auto-scaling triggers fire.

## Inputs
- Scaling recommendation (resource, direction, amount)
- Current resource utilization metrics
- Cost projection for the scaling change
- Current maturity stage

## Procedure

1. Verify the scaling recommendation: what resource, direction (up/down), by how much
2. Check cost implications of the change (calculate percentage increase/decrease)
3. If cost increase > 20%, require CTO approval before proceeding
4. Execute scaling via the `infra.deploy` adapter (instance resize or replica addition) — see `.claude/hive/adapters/infra-deploy.md`
5. Verify new capacity is active and healthy
6. Update infra status in `context.md` with new resource levels
7. Post to #ops:

```markdown
---
agent: devops
type: report
severity: info
tags: [scaling]
mentions: [@cto, @scale-chief]
requires: ack
---

## Scale Action

### Resource: {service/resource name}
### Direction: {up | down}
### Change: {from X to Y}
### Cost Impact: {+/- $amount/month, +/- %}
### New Capacity: {verified | pending}
### Trigger: {scale-chief recommendation | auto-scaling | CTO order}
```

## Output Format
Scaling report posted to #ops (see template above).

## Rules
- Scaling up with cost increase > 20% requires CTO approval
- Scaling down requires verification that current load allows it (check last 24h peak utilization)
- At Stage 2, all scaling is manual with approval
- At Stage 3+, auto-scaling policies can be configured with pre-approved thresholds
- Always verify new capacity is active before reporting success
