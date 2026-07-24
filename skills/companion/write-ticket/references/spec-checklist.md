# Spec self-review checklist — "unit tests for the ticket"

Run this on the draft BEFORE showing it to the user. Fix what you can fix; convert
what you can't into `[NEEDS CLARIFICATION]` markers. A draft that fails an item and
ships anyway is a bug you planted in someone's sprint.

## Requirements quality

- [ ] **One behavior per AC.** No "and" chaining two observable behaviors in one
  statement. Split them.
- [ ] **Every AC is testable.** For each AC, you can name the test that would verify
  it (even informally). "The system should be robust" is not an AC.
- [ ] **EARS clauses are well-formed.** WHEN/IF triggers are observable events or
  states, SHALL responses are observable behaviors — not internal design choices.
- [ ] **No implementation leakage in requirements.** Class names, SQL, library
  choices belong in Technical context, never in FR/AC statements. (Test: could a
  different implementation satisfy this AC? If no, it's leaked.)
- [ ] **Error paths covered.** At least one IF/THEN unwanted-behavior AC per FR that
  can fail (invalid input, permission denied, not found, partial failure).

## Measurability

- [ ] **Success criteria are measurable and time-bound.** Each SC names a metric, a
  threshold, a window, and where it's observed. "Works correctly" is not an SC.
- [ ] **Behavior changes show before/after** for a concrete input.

## Honesty

- [ ] **No silent assumptions.** Every guess you made while drafting is either
  confirmed by the user/code or marked `[NEEDS CLARIFICATION: …]`.
- [ ] **Technical examples verified.** Any claim (SQL pattern matching, regex,
  collation, operator behavior) was mentally executed on the exact input shown and
  produces the stated output. If you can't trace it in under a minute, remove the
  claim or state the problem abstractly. A plausible-but-wrong example is worse than
  none — it destroys trust in the whole spec and implementers code against it.
- [ ] **Existing code references are real.** Every `path:line` was actually read this
  session, not recalled.

## Scope & granularity

- [ ] **Out-of-scope items each have a reason**, not just a list.
- [ ] **Split check:** > ~5 FRs or > ~15 foreseeable tasks → propose splitting into
  multiple tickets (P1 slice first) instead of one mega-ticket.
- [ ] **User stories are independently testable** — each could ship alone and be
  verified alone.

## Contract with `implement-ticket`

- [ ] Acceptance Criteria section non-empty, ACs numbered (AC-n.m).
- [ ] Definition of Done checklist present.
- [ ] FR/AC/SC IDs unique — implementation tasks and tests will reference them.
