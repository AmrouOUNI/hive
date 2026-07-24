---
name: infra-rollback
description: Roll back a production deployment when post-deploy errors are detected or CTO orders it.
---

# rollback — Rollback Production Deployment

## When to Use
DevOps uses this when post-deploy errors are detected or CTO orders a rollback.

## Inputs
- Current deployed version
- Previous stable version
- Error rate data or CTO rollback order
- Incident severity (P0-P3)

## Procedure

1. Identify current version and previous stable version
2. Execute rollback via `adapter:infra.deploy` targeting the previous version
3. Verify rollback success by running health-check on all endpoints
4. Post rollback report to #ops and #incidents:

```markdown
---
agent: devops
type: report
severity: warning
tags: [rollback, incident]
mentions: [@cto]
requires: ack
---

## Rollback Report

### Rolled Back From: {current version}
### Rolled Back To: {previous version}
### Trigger: {error spike | smoke-test failure | CTO order}
### Health Check: {pass | fail}
### Error Rate After Rollback: {rate}
### Timestamp: {ISO timestamp}
```

5. Notify CTO that rollback is complete and service is stable

## Output Format
Rollback report posted to #ops and #incidents (see template above).

## Rules
- Rollback is pre-authorized for P0 incidents (no human approval needed if error rate > 10%)
- For non-critical incidents, CTO approval is required before rollback
- Health-check must pass after rollback — if it fails, escalate immediately to CTO
- Always post to both #ops and #incidents channels
