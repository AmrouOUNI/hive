# Track & plan format (Conductor-compatible)

The track directory is the durable, machine-readable state of the work. Everything
survives session death, is git-versioned, and is what status/review/revert tooling
(Conductor's or a human's) queries later.

## Track directory

```
conductor/tracks/<ticket-id>_<YYYYMMDD>/
├── index.md          # links to spec, plan, metadata (file-resolution indirection)
├── metadata.json     # {track_id, ticket: "<id/URL>", type, status, created_at, updated_at}
├── spec.md           # Phase 3-4 output (normative spec)
├── plan.md           # Phase 5-6 output (approved plan = source of truth)
└── gap-analysis.md   # Phase 6 output
```

`type` ∈ feature | bug | chore | refactor. `status` ∈ new | in_progress | completed
| cancelled (mirrors the registry marker; the registry is authoritative).

**Registry** — `conductor/tracks.md`, one entry per track, `---`-separated:

```markdown
- [ ] **Track: <description> (<ticket-id>)**
  *Link: [./tracks/<track_id>/](./tracks/<track_id>/)*
```

Markers: `[ ]` pending · `[~]` in progress · `[x]` complete — used at track level
(registry) and at phase/task/sub-task level (plan.md).

If `conductor/` doesn't exist and the user declined a full Conductor setup, create
the same structure under `conductor/tracks/` anyway (minimal footprint, no
product/tech-stack docs) — or under a directory the project's conventions designate.
If the project forbids committing spec documents, keep the track directory
gitignored/local and skip the bookkeeping commits below.

## plan.md format

```markdown
# Implementation Plan: <ticket-id> — <title>

## Phase 1: <Name>
- [x] Task: Write failing tests for <unit> [AC-1.1, AC-1.2] (a1b2c3d)
    - [x] <sub-task>
- [~] Task: Implement <unit> [AC-1.1, AC-1.2]
- [ ] Task: Phase Verification & Checkpoint (see workflow)

## Phase 2: <Name>
- [ ] Task: ... [AC-2.1]
```

Rules:

- **Hierarchy**: Phases → Tasks → Sub-tasks. `[ ]` marker on EVERY task and sub-task.
- **AC traceability**: every implementation task carries the `[AC-n.m]` tags it
  satisfies. Every AC appears on ≥ 1 task and ≥ 1 test task — the Phase 5 coverage
  gate enforces this.
- **TDD ordering**: when the resolved policy is TDD (default), each feature task
  decomposes into "write failing tests" before "implement".
- **SHA annotation**: when a task completes, append the short commit SHA to its line
  (`(a1b2c3d)`). The plan becomes the commit ledger that review and revert parse.
- **Checkpoint annotation**: when a phase completes its manual verification, append
  `[checkpoint: <sha7>]` to the phase heading.

## Task lifecycle (per task)

1. Mark `[ ]` → `[~]` in plan.md **before** starting.
2. RED: write failing test(s); run them; do not proceed without a failure.
3. GREEN: minimal implementation; re-run to green.
4. REFACTOR (optional); re-run.
5. Verify: resolved lint/typecheck/targeted-test commands.
6. Commit the code (project commit convention, referencing the ticket id).
7. Mark `[~]` → `[x]`, append the short SHA to the task line.
8. Bookkeeping commit (when artifacts are versioned):
   `conductor(plan): Mark task '<name>' as complete`.

Two commits per task keeps code diffs clean and gives revert a deterministic pair
to unwind. If the project's policy is per-phase commits, batch accordingly but keep
the SHA annotations truthful.

## Phase checkpoint (end of each phase)

1. Run the phase's full test scope; on failure, max 2 debug attempts, then stop and
   ask.
2. Generate a **manual verification plan**: concrete steps with expected outcomes
   ("open <URL>, expect X", "curl <endpoint>, expect 201 with body Y").
3. **PAUSE — explicit user confirmation** that verification passed.
4. Checkpoint commit (empty allowed): `conductor(checkpoint): End of Phase <n>`.
5. Append `[checkpoint: <sha7>]` to the phase heading; bookkeeping commit
   `conductor(plan): Mark phase '<name>' as complete`.

In **quick mode** (trivial fixes) the checkpoint reduces to: full verification
commands + a one-line manual check proposal — no pause unless something failed.

## Reserved commit scopes

`conductor(plan):` (plan bookkeeping) · `conductor(checkpoint):` (phase
checkpoints) · `chore(conductor):` (track lifecycle: initialize / complete /
archive). Code commits never use these scopes.
