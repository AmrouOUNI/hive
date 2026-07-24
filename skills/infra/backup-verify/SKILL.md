---
name: infra-backup-verify
description: Verify database backup integrity during health checks and the weekly audit.
---

# backup-verify — Verify Database Backup Integrity

## When to Use
DevOps uses this when running the every-4h health check or during weekly audit.

## Inputs
- Backup metadata from `adapter:infra.db`
- Historical backup size data (for trend comparison)
- Current maturity stage (affects restore test frequency)

## Procedure

1. Check last backup timestamp via `adapter:infra.db`
2. Verify backup age is < 24 hours
3. Check backup size and compare to previous backups:
   - Size should be growing over time (or stable)
   - Shrinking size is suspicious — flag for investigation
4. At Stage 3+, run a test restore monthly to verify backup is restorable
5. Post verification to #ops:

```markdown
---
agent: devops
type: report
severity: {info | warning | critical}
tags: [backup]
requires: {ack | action}
---

## Backup Verification

### Last Backup: {ISO timestamp}
### Age: {hours since last backup}
### Size: {current size} (previous: {previous size})
### Trend: {growing | stable | shrinking}
### Restorable: {verified | not tested}
### Status: {healthy | warning | critical}
```

## Output Format
Backup verification report posted to #ops (see template above).

## Rules
- Backup > 24h old is a warning
- Backup > 48h old is critical — escalate immediately
- Shrinking backup size is suspicious — investigate and report
- At Stage 2, restore tests are manual. At Stage 3+, automate monthly test restores
