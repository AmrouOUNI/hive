# Pull Request template (agnostic) + data collection

## Data to collect before creating the PR

**From the ticket (Phase 1):** `WHY` (business reason, not technical), `TICKET_LINK`,
the numbered ACs.

**From git:**
```bash
git log <base>..HEAD --oneline --no-decorate      # commits
git diff <base>...HEAD --stat                      # files changed
```

**From the review agents (Phases 8–9):** review summary table, security findings
count by severity + resolution status, AC-by-AC coverage table, DoD checklist state.

**From verification runs (Phases 7 & 9):** exact commands with pass/fail and test
counts.

**From the plan:** any deviation between the approved plan and what was actually
built (order changes, added/dropped tasks, stack deviations) — these go in the
Deviations section, with reasons. An undocumented deviation is a silent gap.

## PR body

```markdown
## Why

<2–3 sentences: the user problem or business need. Not a technical description.>

Ticket: [<ID>](<TICKET_LINK>)
<if a Conductor track exists and is committed:> Spec: `conductor/tracks/<id>/`

## Summary

- `<sha>` <commit message>
- ...

**Files:** <N> added, <N> modified | **Interfaces:** <new/changed endpoints, CLI
commands, public APIs — whatever this project exposes>

## Acceptance criteria coverage

| AC | Criterion | Test | Status |
|---|---|---|---|
| AC-1.1 | <text> | <test file :: test name> | covered |
| ... | | | |

## Review summary

| Reviewer | Findings | Resolved |
|---|---|---|
| Code quality | <n> (severity breakdown) | <yes/list> |
| Security | <n> | <yes/list> |
| Spec & DoD | <gaps> | <yes/list> |

## Deviations from plan *(omit if none)*

- <deviation> — <reason> (approved in session / flagged for review)

## Definition of Done

- [<x/ >] <each item from the ticket's DoD, honestly checked>

## Test plan

```
<command> → PASS/FAIL (<N> tests)
...
```
- [ ] CI passes
- [ ] Manual verification per phase checkpoints (see plan.md)
```

## Push & create

1. `git push -u origin HEAD` (HTTPS). If it fails: STOP, ask the user to push;
   still output the full PR body for manual use.
2. `gh pr create --title "<commit-convention-formatted title>" --body-file <tmp>`
   (or the platform's equivalent — GitLab `glab mr create`, etc.). If it fails:
   output title + body for manual creation.
3. Present: PR URL, summary, and any unresolved IMPROVEMENT/NITPICK review items
   that were noted but deliberately not fixed.

**Honesty rule:** the DoD and test plan report what actually ran. A skipped suite
is listed as skipped with the reason — never checked off.
