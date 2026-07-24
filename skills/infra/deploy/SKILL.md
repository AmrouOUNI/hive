---
name: infra-deploy
description: Deploy to production after CTO approval, with verification steps.
---

# deploy — Deploy to Production

## When to Use
DevOps uses this when CTO approves a deploy to production.

## Inputs
- CTO approval message (Level 3 human approval required)
- Current build status (all tests passing, QA approved)
- Open incident list (no critical incidents allowed)

## Procedure

1. Run pre-deploy checklist:
   - Verify all tests passing on main branch (check CI status)
   - Verify QA has approved the release
   - Verify no critical incidents are currently open
   - If any check fails, abort and report blocker to #ops
2. Execute deploy via `adapter:infra.deploy` with the target version
3. Run `smoke-test` skill immediately after deploy completes
4. Monitor error rate for 30 minutes post-deploy (compare to pre-deploy baseline)
5. Post deploy report to #ops:

```markdown
---
agent: devops
type: report
severity: info
tags: [deploy]
mentions: [@cto]
requires: ack
---

## Deploy Report

### Version: {version}
### Status: {success | failed | rolled-back}
### Smoke Test: {pass | fail}
### Error Rate: {post-deploy rate} (baseline: {pre-deploy rate})
### Duration: {deploy duration}
### Timestamp: {ISO timestamp}
```

6. If error rate spikes above baseline or smoke test fails, trigger `rollback` skill

## Output Format
Deploy report posted to #ops (see template above).

## Rules
- Deploy requires human approval (Level 3) — never deploy without CTO sign-off
- Always run `smoke-test` after deploy — no exceptions
- Keep previous version available for rollback at all times
- CI failure on main blocks all deploys
- If any pre-deploy check fails, do not proceed — report the blocker
