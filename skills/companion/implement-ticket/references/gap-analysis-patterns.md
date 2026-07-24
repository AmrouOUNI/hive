# Gap Analysis Patterns — lessons learned (generalized)

Encoded from real gap-analysis sessions. Project-agnostic: examples name concrete
technologies only as illustrations of the pattern.

## 1. Task ordering is the #1 gap category

Generated artifacts must exist before code that references them. Plans routinely
place codegen/migration tasks after the code that consumes their output, which
blocks compilation.

**Check every task:** does it reference a type, client, schema, or API that another
task generates? Then the generating task must come first. Applies to ORM client
generation, API-type generation from schemas, protobuf/GraphQL codegen, DB
migrations, build steps producing importable artifacts.

## 2. Test fixture names are misleading — trace to the real calls

A fixture or test-setup step named "given an X exists" may not create X where you
think: builders can produce in-memory objects (no DB row), setup helpers can create
several entities at once, and queries filtering on junction/association tables may
not require the primary entity row at all.

**Rule:** read the fixture/step/factory implementation down to the actual
persistence calls. Never reason from names. Distinguish build-in-memory from
persist-to-store variants of the same factory.

**Why it matters:** skipping this produces false-positive gaps ("missing setup for
entity X") that bloat the analysis and erode trust.

## 3. Gap severity follows a power law

A typical honest analysis of ~9 candidate gaps yields 1 blocking, 2–3 important,
rest noise. If more than half your gaps are LOW, the filter is too loose — sharpen
it, don't pad the report.

## 4. "Exhaustive" ≠ "useful"

If a gap's recommendation is "no action needed", it's not a gap — it's a note.
Don't include it. Padding dilutes the signal the user must act on.

## 5. Test-type conventions are project law

Recommending a test type the project doesn't use for that layer is scope creep in
disguise. Before recommending, look at the existing tests for the touched feature
and mirror them.

## Severity filter

| Severity | Definition | Action |
|---|---|---|
| **BLOCKING** | Prevents compilation or breaks the whole suite | Must fix in plan |
| **IMPORTANT** | Breaks specific tests or misses security/business coverage | Must fix in plan |
| **MEDIUM** | Spec ambiguity that could lead to a wrong implementation | Fix or document as TODO |
| **LOW** | Defensive improvement, nice-to-have | Fix only if trivial |

**A gap must answer YES to at least one:** blocks compilation? breaks tests?
causes a production bug? misses security coverage?

**Reject gaps that are:**
- Defensive guards for states invariants already make impossible
- Checks the plan already handles implicitly (e.g. backward compat on an empty store)
- Test types that don't match the project's established patterns
- Scope-creep improvements beyond what the ticket asks

## Common false positives

- **"Missing setup for entity X"** — check whether the query filters on an
  association table; the primary row may be unnecessary. Trace the fixture (see #2).
- **"Backward compatibility verification"** — if the store is empty (new
  migration/new table), there is nothing to migrate.
- **"Add unit tests for the handler"** — if the project covers handlers with
  higher-level tests, adding unit tests mixes conventions.

## Structural checks (run these mechanically)

**Ordering — compilation dependencies:**
```
For each task T referencing generated types/clients/schemas:
  → the generating task must precede T
For each task T importing another task's output:
  → the source task must precede T
```

**Scenario completeness** (for each test scenario the plan adds/modifies):
1. Trace the execution path to identify which validations it exercises.
2. Identify prerequisite fixtures by reading fixture code, not names.
3. Verify payloads satisfy the type contracts, even in scenarios that fail early.
4. Every new validation/error path gets at least one scenario exercising it.

**Test-type verification:**
```
For each test the plan adds:
  → does a test file already exist for this feature/layer?
  → same type (unit/integration/e2e/acceptance) as the existing pattern?
```

**AC coverage (the /analyze gate):**
```
For each AC-n.m in the ticket/spec:
  → ≥ 1 plan task tagged [AC-n.m]
  → ≥ 1 test task covering it
Orphan AC = BLOCKING gap. Orphan task (no AC, not infra) = scope-creep flag.
```
