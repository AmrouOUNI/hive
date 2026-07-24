---
name: qa-coverage-audit
description: Audit test coverage against thresholds at the daily checkpoint or after a sprint.
---

# coverage-audit — Audit Test Coverage Against Thresholds

## When to Use
QA Lead uses this when running the daily checkpoint or after a sprint ends.

## Inputs
- Test suite configuration and coverage tooling
- Previous coverage report (for regression comparison)
- Threshold targets: 80% domain, 70% application, 50% infrastructure

## Procedure

1. Run the full test suite with coverage enabled across all libs
2. Parse the coverage report — extract per-lib line/branch/function coverage
3. Compare each lib's coverage to its threshold:
   - `libs/domain/` — 80% minimum
   - `libs/application/` — 70% minimum
   - `libs/infrastructure/` — 50% minimum
4. Identify uncovered critical paths — focus on aggregates, command handlers, and domain services
5. Compare to the last audit's numbers — flag any lib where coverage dropped
6. Post coverage report to `#daily-standup`:

```markdown
---
agent: qa-lead
type: report
severity: info | warn
tags: [coverage, quality]
---

## Coverage Audit — {date}

| Lib            | Current | Threshold | Delta | Status |
|----------------|---------|-----------|-------|--------|
| domain         | X%      | 80%       | +/-   | pass/fail |
| application    | X%      | 70%       | +/-   | pass/fail |
| infrastructure | X%      | 50%       | +/-   | pass/fail |

### Uncovered Critical Paths
- {aggregate or handler with low coverage}

### Regressions
- {lib}: dropped from X% to Y%
```

## Rules
- Coverage drop > 5% in any lib blocks merge until addressed
- Uncovered aggregates and command handlers are always flagged, regardless of overall percentage
- Report even when everything passes — the trend data matters
