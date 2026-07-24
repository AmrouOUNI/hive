---
name: implement-ticket
description: Implement a ticket end-to-end to a pull request in ANY project — tracker-agnostic (Jira, GitHub Issues, Linear, local markdown) and integrated with Conductor context-driven development (conductor/ tracks, plan.md as source of truth) with graceful fallback when Conductor is absent. Spec-driven pipeline - retro-spec, enrichment, plan with AC coverage gate, adversarial gap analysis, TDD implementation with phase checkpoints, parallel review, PR. Use when the user wants to take a ticket/issue to a finished PR ("implement this ticket", "implémente ce ticket", "ticket to PR", "take issue #123 to a PR") and no project-specific implementation skill applies; prefer a project-specific skill when one matches the repo.
---

# /implement-ticket — Ticket to Pull Request pipeline (project-agnostic)

> **Usage:** `/implement-ticket <ticket-id-or-URL>`
> Works with or without Conductor; with any tracker via the adapter layer.

This skill encodes the full pipeline from ticket to merge-ready PR with a
**3-step spec approach** (Code → Spec → Code), interactive gap analysis, TDD with
Conductor task lifecycle, parallel reviews, and DoD compliance.

**Phases:** 0 Resolve context → 1 Fetch & validate (+clarify) → 2 Branch & track →
3 Code→Spec (retro-doc) → 4 Spec→Spec (enrichment) → 5 Spec→Code (plan + AC
coverage gate) → 6 Gap analysis (HARD STOP) → 7 TDD implementation → 8 Parallel
review → 9 Synthesize & fix → 10 PR & track closure

**Reference files:**
- `references/conventions-resolution.md` — protocol/policy split; how every
  project-specific rule (commands, naming, test strategy) is resolved
- `references/tracker-adapters.md` — all tracker reads/writes
- `references/plan-format.md` — track directory, plan.md markers/SHAs/checkpoints,
  task lifecycle
- `references/gap-analysis-patterns.md` — severity filter, structural checks,
  false positives
- `references/pr-template.md` — PR body and data collection

<HARD-GATE>
ZERO CODE BEFORE PLAN APPROVAL.

You MUST NOT take ANY of these actions until the user has EXPLICITLY approved the
plan in Phase 6:
- Write or create any source file
- Run any implementation command
- Create any commit other than the track-initialization bookkeeping commit
- Modify any existing file outside the track's spec documents

The ONLY files you may create before approval are the track artifacts
(spec.md, plan.md, gap-analysis.md, metadata.json, index.md, registry entry).

Phase 6 MUST end with an AskUserQuestion call. You MUST wait for the response.
If the user says "no" or requests changes → revise and re-ask.
If the user says nothing → you are STILL blocked. Do not proceed.

Violating this gate means ALL implementation work must be deleted and restarted
from Phase 6.
</HARD-GATE>

**Key decision points:**
- **Phase 1:** blocks if ACs or DoD are missing; clarify loop on unresolved
  `[NEEDS CLARIFICATION]` markers
- **Phase 1.5:** triviality detector may switch to quick mode (user confirms)
- **Phase 4:** user reviews gap decisions and out-of-scope items
- **Phase 6 (HARD STOP):** user approves the corrected plan via AskUserQuestion
  before ANY implementation
- **Phase 7:** pre-check verifies approval exists; 3-retry limit on test failures;
  phase checkpoints pause for manual verification

---

## Phase 0 — Resolve context (Conductor handshake + conventions)

1. **Workflow handshake:** if `.claude/hive/config.json` exists with a `workflow`
   block (hive-managed project), resolve product/tech-stack docs from
   `workflow.context_files` and the registry from `workflow.tracker` — whatever
   the framework. Else if `conductor/index.md` exists, read it and resolve the
   Product Definition, Tech Stack, and Workflow docs via its links; verify each
   file exists (health check — a missing core file is worth flagging, not fatal).
   If neither is present: run a token-frugal read-only scan (README, one
   manifest, top-2-level tree), and offer once to initialize Conductor via the
   `conductor-setup` skill if available. Never block on it — the pipeline creates
   its own track structure either way (see `plan-format.md`).
2. **Resolve conventions** per `references/conventions-resolution.md`: test/lint/
   typecheck/build commands, test strategy, branch & commit naming, base branch,
   commit granularity, whether track artifacts are committed, review agents,
   layering order. The resolved policy is the single source of truth for
   Phases 2–10 — this SKILL.md deliberately contains no project methodology.
3. **Resolve the tracker adapter** per `references/tracker-adapters.md`.

---

## Phase 1 — Fetch & validate the ticket

Fetch via the adapter (`read`). Extract: title, description, acceptance criteria,
Definition of Done, parent/epic link, labels, status.

| Check | Blocking? | If missing |
|---|---|---|
| Acceptance Criteria (testable) | **YES** | STOP — ask the user to complete them, or propose ACs and get approval |
| Definition of Done | **YES** | STOP — signal absence, propose one from project conventions |
| `[NEEDS CLARIFICATION]` markers unresolved | **YES** | Clarify loop (below) |
| Parent/epic link | no | WARNING only |
| Labels/components | no | WARNING only |
| Status not yet "in progress" | no | Propose the transition (done in Phase 2) |

**Clarify loop:** for each unresolved marker or materially ambiguous AC, ask the
user (grouped, with options where possible). Write the answers back into the
working spec — and offer to update the ticket via the adapter so the tracker
reflects the decisions. Do not carry ambiguity into Phase 3: an ambiguous spec
produces a confident-looking wrong plan.

### Phase 1.5 — Triviality detector (quick mode)

If ALL of: the fix is ≤ ~2 files, the root cause is already evidenced, no schema/
API/contract change, no new dependency — propose **quick mode**:

- Phases 3–6 collapse into a single condensed document (mini-spec: current vs
  expected behavior + root cause; mini-plan: 1–3 tasks with `[AC]` tags).
- The HARD-GATE still applies: present the mini-plan, AskUserQuestion, wait.
- Phases 7–10 run normally (TDD, verification, review may drop to a single
  reviewer, PR).

Full ceremony on a trivial fix produces overengineering; a skipped gate on a
"trivial" fix produces regressions. Quick mode removes ceremony, never gates.

---

## Phase 2 — Branch & track initialization

1. Create the branch per resolved naming policy from the resolved base branch
   (`git fetch` first).
2. **Ticket → in progress** via the adapter (`transition`), for the ticket and any
   sub-items being implemented, within what the tracker's workflow allows.
3. **Initialize the Conductor track** per `references/plan-format.md`: track
   directory, `metadata.json`, `index.md`, registry entry marked `[~]`. If
   artifacts are committed (default): commit
   `chore(conductor): initialize track '<track_id>'`.

---

## Phase 3 — Code → Spec (retro-documentation)

Explore the existing codebase **scoped to the files this ticket will touch** and
produce a formal retro-spec. This is a structured analysis that catches gaps and
risks before any code — not casual exploration.

- Identify the architecture layers actually present (from directory structure and
  imports — don't assume a canonical architecture) and inventory, per layer, the
  **existing rules**: validations, invariants, behaviors/side effects, error
  handling, with `file:line` sources:

  | ID | Rule | Source `file:line` | Type (VALIDATION / INVARIANT / BEHAVIOR) |

- **Error mapping** (API projects): error class → status code table.
- **Trace test fixtures to real calls** — when the ticket touches existing tests,
  read fixture/step/factory implementations down to the persistence calls. Never
  reason from names (see `gap-analysis-patterns.md` #2); this is the top source of
  false gaps downstream.
- **Gaps identified**: | Gap | Detail | Risk (Low/Medium/High) | — missing
  validations, security holes, undocumented behaviors. This table is the most
  valuable output of the phase.

Save as the track's `spec.md` (Conductor sections: Overview, Functional
Requirements, Non-Functional Requirements, Acceptance Criteria carried over with
their AC-n IDs, Out of Scope) + the retro-doc tables.

---

## Phase 4 — Spec → Spec (enrichment)

Enrich the spec with external context. **This phase must fetch parent/epic context**
via the adapter (`parent_context`) — sibling tickets, evolution history, upcoming
dependencies. If the adapter can't provide it, ask the user; do not skip.

For each **gap from Phase 3**, decide:

| Gap | Risk | Decision (FIX here / Out of scope / Accepted) | Justification |

Then add: architecture decisions (with rationale), security analysis (input
validation, authorization coverage, data leakage across tenants/boundaries),
impact on future/sibling work, NFRs (performance, observability, backward
compatibility), and the justified out-of-scope list.

**User review gate:** present the enriched spec (Approve / Revise loop). Gap
decisions and out-of-scope items need explicit approval. An enrichment that merely
restates the ticket is a failed enrichment — redo it.

---

## Phase 5 — Spec → Code (generate the plan)

Generate the track's `plan.md` in the format defined by
`references/plan-format.md`:

- Phases → Tasks → Sub-tasks, `[ ]` markers everywhere.
- Ordering follows the resolved layering and the resolved test strategy — when
  policy is TDD (default), "write failing tests" sub-tasks precede "implement".
- Codegen/migration/schema tasks placed **before** any task consuming their output.
- Module wiring included in each feature task, not deferred to a final phase.
- A checkpoint meta-task ends every phase.
- **Every task carries its `[AC-n.m]` tags.**

**AC coverage gate (blocking):** before Phase 6, verify mechanically that every AC
maps to ≥ 1 task and ≥ 1 test task, and every non-infra task maps to ≥ 1 AC.
Orphan AC = the plan is incomplete. Orphan task = scope creep to justify or cut.

If a dedicated plan-writing skill is available in the session, you may use it to
draft the plan — but the output must land in the track's `plan.md` in the format
above, and it still goes through Phase 6. The plan is NOT approved yet.

---

## Phase 6 — Interactive gap analysis (ends in HARD STOP)

> Catches structural problems in the plan before any code is written, by comparing
> the plan against the spec, the ticket, and the actual codebase state.

### Step 1 — Automated exploration (parallel agents)

Dispatch 2–4 read-only exploration agents in parallel, scoped to the areas the
plan touches — typically: domain/business rules, application/wiring,
infrastructure/persistence & generated artifacts, tests & fixtures. Each verifies:
does the plan match what actually exists? Are dependencies and orderings correct?
What do fixtures actually create?

### Step 2 — Identify & filter gaps

Classify with the severity filter from `references/gap-analysis-patterns.md`
(BLOCKING / IMPORTANT / MEDIUM / LOW) and apply its rejection rules aggressively.
A gap must block compilation, break tests, cause a production bug, or miss
security coverage — otherwise it's a note, not a gap.

### Step 3 — Structural checks

Run the mechanical checks from `gap-analysis-patterns.md`: compilation-dependency
ordering, scenario completeness (trace execution paths and payload contracts),
test-type conformity with existing patterns, AC coverage re-check.

### Step 4 — Gap document

Save `gap-analysis.md` in the track: per-gap sections (severity, source task,
problem, impact, recommendation) + summary table.

### Step 5 — Interactive review

- BLOCKING/IMPORTANT with an unambiguous fix: present the fix and apply it — don't
  ask permission for obvious corrections (e.g. task reordering).
- MEDIUM (spec ambiguity): present options via AskUserQuestion.
- LOW: fix if trivial, otherwise note and defer.

### Step 6 — Correct the plan

Apply corrections to `plan.md`: reorder, detail under-specified steps, add missing
test scenarios, fix test types, remove unnecessary steps, add TODOs for deferrals.

### Step 7 — Final plan approval (HARD STOP — mandatory AskUserQuestion)

The most critical gate in the pipeline. **Implementation is BLOCKED until this
completes.**

1. **Present** the corrected plan as a summary: tasks in order, test types, files
   to create/modify, AC coverage.
2. **Call AskUserQuestion** with exactly these options:
   - "Approve the plan — start TDD implementation"
   - "Request changes — I want to modify the plan"
   - "Abort — stop the pipeline here"
3. **STOP. Nothing else in this message.** No code, no file creation, no "let me
   start while you review".
4. On "changes" → revise, re-present, re-ask. On "abort" → stop, keep all track
   artifacts. On "approve" → and ONLY then → Phase 7.

**Rationalizations that mean you are violating this gate:**

| Excuse | Reality |
|---|---|
| "The plan is obvious, the user will approve" | You don't know that. ASK. |
| "I'll present and start implementing in the same message" | NEVER. Present → stop → wait. |
| "The user already approved the spec" | Spec ≠ plan. Both need explicit approval. |
| "AskUserQuestion is too formal here" | It's the only mechanism that forces a real stop. |
| "This is simple enough to skip approval" | Quick mode still gates. No exceptions. |
| "The plan is a formality" | Plans have caught critical issues every time. |

If you catch yourself thinking any of these: call AskUserQuestion. Wait. Do
nothing else.

---

## Phase 7 — TDD implementation

**Pre-check (mandatory):** the plan file exists in the track; the user EXPLICITLY
approved via AskUserQuestion in Phase 6 Step 7; the approval is in the
conversation history. If ANY is false → STOP, return to Phase 6 Step 7.

Execute the plan task by task following the **task lifecycle** in
`references/plan-format.md`:

- `[ ]`→`[~]` before starting · RED (failing test first — do not proceed without a
  failure) · GREEN (minimal code) · REFACTOR · verify (resolved lint/typecheck/
  targeted tests) · code commit per project convention · `[~]`→`[x]` + short SHA on
  the task line · bookkeeping commit `conductor(plan): …` when artifacts are
  versioned.
- **Deviation brake:** implementation diverging from the documented tech stack →
  STOP, propose the dated tech-stack update, get approval, resume.
- **3-retry rule:** a failing verification gets at most 3 fix attempts, then STOP,
  present the error, ask the user.
- **Phase checkpoints:** at each phase end, run the phase's full test scope,
  generate the manual verification plan, **pause for the user's confirmation**,
  then checkpoint commit + `[checkpoint: sha]` annotation.

After the last task: full regression per resolved policy (all test suites, lint,
typecheck, format check). Fix and commit formatting if needed.

---

## Phase 8 — Parallel review (3 agents)

Dispatch 3 review agents simultaneously (single message, 3 Agent calls):

1. **Code quality** — use the project's declared specialist review agent if one
   matches the touched area (check the project's agent catalog/CLAUDE.md);
   otherwise a generic reviewer. Reviews the full branch diff: correctness, error
   handling, dead code, wiring completeness, convention adherence. If
   `conductor/code_styleguides/` exists, those styleguides are **law** — a
   violation is at least High. Output: CRITICAL / IMPROVEMENT / NITPICK with
   `file:line`.
2. **Security** — auth/authz coverage on new surfaces, input validation, injection
   vectors, secrets/PII in code or fixtures, internal errors leaking to clients,
   cross-tenant/boundary data leakage. Output: CRITICAL / MEDIUM / LOW with
   `file:line`.
3. **Spec & DoD validator** — AC-by-AC coverage table (each AC → its test(s),
   covered/not); edge cases (error scenarios, null/empty, boundaries); interface
   contracts match the spec; the ticket's DoD checklist item by item, honestly.

If one agent fails, re-dispatch it once, then proceed with the available reviews
and say so.

---

## Phase 9 — Synthesize & fix

1. CRITICAL security findings → dedicated commits.
2. Spec gaps (uncovered ACs) → dedicated commits.
3. Code-review CRITICALs → dedicated commits.
4. IMPROVEMENT/NITPICK → fix if trivial, otherwise list in the PR description.
5. Re-run the full verification suite; nothing unresolved-critical may reach
   Phase 10.

---

## Phase 10 — Create the PR & close the track

1. Collect data and build the PR per `references/pr-template.md` (AC coverage
   table, review summary, deviations from plan, honest DoD, test plan).
2. Push (HTTPS) and create the PR via the platform CLI.
3. **Ticket → in review** via the adapter, within the workflow's limits.
4. **Track closure:** registry `[~]`→`[x]`, commit
   `chore(conductor): Mark track '<id>' as complete`.
5. **Conductor doc sync** (only when `conductor/` core docs exist): if the change
   materially affects the product description or the tech stack, propose diff
   -formatted updates to `product.md` / `tech-stack.md` — apply only on explicit
   approval, commit `docs(conductor): Synchronize docs for track '<id>'`.
   Product guidelines are near-immutable: propose changes only for explicit
   strategic shifts, with a warning.
6. Offer to archive the track (`conductor/archive/`) or leave it active.
7. Present: PR URL, unresolved review items, follow-ups worth their own tickets
   (deliberately unimplemented items become proposed tickets, never silent gaps).

---

## Failure modes

| Phase | Failure | Action |
|---|---|---|
| 0 | No conventions resolvable and user unavailable | Use conservative defaults; list every assumption in the plan for approval |
| 1 | Adapter fails | Retry once → ask the user to paste the ticket |
| 1 | Missing ACs or DoD | STOP; propose them; wait for approval |
| 3 | Exploration too broad | Re-scope to files that will be modified |
| 3 | Fixture behavior assumed from names | REDO — trace to real persistence calls |
| 4 | Parent context unavailable | Ask the user; do NOT skip the phase |
| 4 | Enrichment is a copy of the ticket | REDO — enrichment must add decisions, security analysis, NFRs |
| 5 | An AC has no covering task/test | BLOCKING — fix the plan before Phase 6 |
| 6 | > half the gaps are LOW | Over-analysis — re-filter with the 4-question test |
| 6 | Any implementation before AskUserQuestion approval | VIOLATION — delete the work, return to Phase 6 Step 7 |
| 7 | Pre-check fails (no approval in history) | STOP — return to Phase 6 Step 7 |
| 7 | Test failure after 3 retries | STOP — present the error, ask |
| 7 | Test infra unavailable (DB, services) | Run what's runnable; state precisely what was skipped in the PR |
| 8 | A review agent fails twice | Proceed with available reviews; disclose |
| 10 | Push fails | STOP — ask the user to push; output the PR body anyway |
| 10 | PR creation fails | Output title + body for manual creation |

---

## What this skill does NOT do

- Write the ticket — that's `write-ticket` (its output contract: numbered ACs +
  DoD, which Phase 1 depends on).
- Bypass a project's dedicated tracker or implementation skill.
- Mirror plan tasks into tracker sub-tasks — the plan lives in the repo; the
  ticket links to it.
- Merge the PR or deploy.
