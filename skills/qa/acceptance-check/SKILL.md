---
name: qa-acceptance-check
description: Verify a claimed-complete feature against its acceptance criteria.
---

# acceptance-check — Verify Feature Meets Acceptance Criteria

## When to Use
QA Lead uses this when Sr Backend claims a feature is complete.

## Inputs
- Feature `spec.md` with acceptance criteria
- Test suite for the feature
- Implementation code (to verify tests target the right behavior)

## Procedure

1. Read the feature's `spec.md` — extract every acceptance criterion
2. For each criterion, verify a corresponding test exists:
   - Search test files for assertions that map to the criterion
   - If no test found, mark criterion as **untested**
3. Run the tests — all must pass
4. Review each test to confirm it actually validates the criterion's behavior, not just coverage:
   - Does the test assert the right outcome?
   - Does it cover the happy path AND the described edge case?
5. Check spec for mentioned edge cases — verify each has a test
6. Post verdict to `#daily-standup`:

```markdown
---
agent: qa-lead
type: verdict
severity: info | warn
tags: [acceptance, quality]
mentions: [@sr-backend]
---

## Acceptance Check: {feature name}

**Verdict: PASS / FAIL**

| Criterion | Test Exists | Test Passes | Validates Behavior |
|-----------|-------------|-------------|--------------------|
| {AC1}     | yes/no      | yes/no      | yes/no             |

### Issues
- {criterion}: {what's missing or wrong}
```

## Rules
- Every acceptance criterion must have at least one test
- "It works when I try it" is not acceptance — automated tests required
- A test that passes but doesn't actually validate the criterion counts as **untested**
- Feature cannot ship until verdict is PASS
