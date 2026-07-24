---
name: infra-ci-monitor
description: Monitor CI pipeline status on push and during regular health checks.
---

# ci-monitor — Monitor CI Pipeline Status

## When to Use
DevOps uses this on every push to the repository or as part of the regular health check.

## Inputs
- GitHub Actions pipeline status
- Recent push/commit metadata
- Branch information (main vs feature)

## Procedure

1. Check CI pipeline status via GitHub Actions
2. Identify any failed builds in recent runs
3. Parse failure reason for each failed build:
   - Test failure
   - Lint error
   - Build error
   - Timeout
4. Categorize by branch:
   - Main branch failure: mark as critical, blocks all deploys
   - Feature branch failure: notify sr-backend only
5. Post CI status to #ops:

```markdown
---
agent: devops
type: report
severity: {info | warning | critical}
tags: [ci]
mentions: [{@sr-backend if failure}]
requires: {ack | action}
---

## CI Status

### Branch: {branch name}
### Status: {passing | failing}
### Failure Type: {test | lint | build | timeout | N/A}
### Failed Step: {step name if applicable}
### Commit: {short SHA} by {author}
### Impact: {deploys blocked | feature branch only | none}
```

## Output Format
CI status report posted to #ops (see template above).

## Rules
- CI failure on main branch is critical — blocks all deploys until resolved
- CI failure on feature branch: notify sr-backend only, do not escalate
- Parse and categorize failure reason — raw logs are not useful for humans
- If main is broken, post to both #ops and tag sr-backend for immediate fix
