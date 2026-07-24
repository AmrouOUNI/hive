---
name: qa-regression-scan
description: Detect regressions across all libs on push or before deploy.
---

# regression-scan — Detect Regressions Across All Libs

## When to Use
QA Lead uses this on push or before deploy.

## Inputs
- Full test suite across all libs
- Last green run results (for comparison)

## Procedure

1. Run the full test suite across all libs
2. Compare results to the last green run:
   - Identify newly failing tests (passed before, failing now)
   - Identify flaky tests (passed last time but fail now, or vice versa)
3. For each failure, note the lib, test name, and error message
4. Post results to `#daily-standup`:

```markdown
---
agent: qa-lead
type: report
severity: info | critical
tags: [regression, quality]
---

## Regression Scan — {date/commit}

**Status: GREEN / RED**

### New Failures
- {lib}: {test name} — {error summary}

### Flaky Tests
- {lib}: {test name} — {pattern: passed→failed or failed→passed}

### Summary
- Total tests: X
- Passing: X
- Failing: X (Y new)
- Flaky: X
```

5. If any test fails on `main` branch, post alert to `#incidents`

## Rules
- No deploy with failing tests
- Flaky tests get 3 strikes then must be fixed or removed
- Failures on `main` are always `severity: critical` and go to `#incidents`
- Include the specific error message — don't just say "test failed"
