---
name: infra-smoke-test
description: Post-deploy smoke test verifying the release is healthy.
---

# smoke-test — Post-Deploy Smoke Test

## When to Use
DevOps uses this after every deployment to verify the release is healthy.

## Inputs
- Deployed version and environment
- List of key API endpoints to verify
- Pre-deploy error rate baseline

## Procedure

1. Hit the health endpoint — verify 200 response
2. Verify key API endpoints respond correctly:
   - List companies endpoint (GET, expect 200)
   - Get org endpoint (GET, expect 200)
   - Auth flow (token validation, expect 200)
3. Check error rate in the first 5 minutes post-deploy
4. Compare error rate to pre-deploy baseline
5. If any check fails, recommend rollback immediately
6. Post smoke test result:

```markdown
---
agent: devops
type: report
severity: {info | critical}
tags: [smoke-test, deploy]
requires: {ack | action}
---

## Smoke Test

### Version: {deployed version}
### Health Endpoint: {pass | fail}
### API Endpoints: {all pass | failures listed}
### Error Rate: {post-deploy %} (baseline: {pre-deploy %})
### Result: {PASS | FAIL}
### Action: {none | rollback recommended}
```

## Output Format
Smoke test report posted to #ops (see template above).

## Rules
- Smoke test failure = automatic rollback recommendation (trigger `rollback` skill)
- Must complete within 10 minutes of deploy
- All key endpoints must return expected status codes
- Error rate spike above 2x baseline is a failure
