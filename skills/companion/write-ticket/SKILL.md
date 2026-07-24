---
name: write-ticket
description: Draft spec-driven tickets for ANY project and ANY tracker (Jira, GitHub Issues, Linear, or plain markdown), with EARS/Given-When-Then acceptance criteria, [NEEDS CLARIFICATION] markers, and Conductor (context-driven development) integration. Use when the user wants to write, draft, enrich, or structure a ticket/issue and no project-specific ticket-writing skill applies — triggers on "write a ticket", "draft an issue", "rédiger un ticket", "spec ticket", "créer une issue", or a feature intent clearly headed for a backlog. If a project-specific ticket skill exists (matching this repo's tracker and conventions), prefer it over this one.
---

# Write Ticket (project-agnostic, spec-driven)

## Purpose

Produce a spec-first ticket with enough verified context that a downstream
implementer — human or the `implement-ticket` skill — can start without a separate
discovery phase. Works in any repo, with any tracker, with or without Conductor.

Two principles govern everything below:

1. **Intent before implementation.** The ticket body specifies WHAT and WHY;
   technical context is present but clearly separated and non-normative.
2. **No silent assumptions.** Anything you guessed and couldn't verify becomes an
   explicit `[NEEDS CLARIFICATION: …]` marker, never prose that sounds confident.

Reference files:
- `references/ticket-template.md` — the ticket structure (EARS + GWT, FR/AC/SC IDs)
- `references/spec-checklist.md` — mandatory self-review before presenting a draft
- `references/tracker-adapters.md` — tracker detection and all read/write operations

---

## Phase 0 — Resolve context (project + tracker)

**Project context — declared workflow first, graceful fallback:**

1. If `.claude/hive/config.json` exists and has a `workflow` block (hive-managed
   project): use `workflow.context_files` for product/tech-stack docs and
   `workflow.tracker` for the registry — whatever the framework is.
2. Else if `conductor/index.md` exists at the repo root: read it and follow its
   links to the Product Definition, Tech Stack, and Workflow docs. Verify each
   linked file exists (health check); if one is missing, say so and continue with
   what's there. These docs answer persona/product questions you'd otherwise ask
   the user, and tech-stack.md scopes the technical context section.
3. Otherwise: run a token-frugal read-only scan — README header, one dependency
   manifest, top-2-level directory tree. Respect `.gitignore`. Do NOT crawl the
   codebase at this stage.
3. If Conductor is absent and the request is clearly the start of a larger body of
   work, mention once that the `conductor-setup` skill (if available) would give
   every future ticket durable project context — then move on. Never block on it.

**Tracker:** resolve the adapter per `references/tracker-adapters.md` (project
tracker skill → Jira → GitHub Issues → Linear → local markdown). All subsequent
tracker reads/writes go through that adapter.

---

## Phase 1 — Detect the mode

- **Enrich mode** — the request contains a ticket identifier (see ID formats in the
  adapter reference) AND the intent is to write/enrich, not just read.
  1. `read(ticket_id)` via the adapter.
  2. If the existing description is empty or < ~3 meaningful lines, treat the title
     as raw intent and run the Create flow.
  3. Preserve an explicit `Scope > Out` the user already wrote. Otherwise regenerate
     cleanly — merging partial content produces worse results than a fresh draft.
  4. Don't silently rewrite the title or change its language; propose changes only.
- **Create mode** — default. The user gave a rough intent in natural language.

---

## Phase 2 — Discovery (max 3 questions, then markers)

Ask at most 3 targeted questions, skipping any the request already answered:

1. **Persona & value** — for which user/role, and what measurable outcome?
2. **Scope** — what is explicitly out of scope?
3. **Success signal** — how will we know it worked (metric, observable behavior)?

Why only 3: the output is a ticket, not a PRD. If you need more than 3 questions to
understand the intent, the user needs a brainstorming/requirements session first —
suggest one and stop.

**Everything still ambiguous after 3 answers becomes a
`[NEEDS CLARIFICATION: specific question]` marker in the draft.** Markers are
first-class output: they surface in the "Open questions" section, and Phase 6
refuses to push while unresolved markers exist unless the user explicitly accepts
them as open questions to the reporter/PO.

---

## Phase 3 — Bounded code scan (read-only, ≤ 3 commands)

Surface implementation context without doing the implementer's discovery for them.
If a command returns > 100 matches, narrow the query instead of dumping results.

1. **Impacted modules** — use whatever workspace tooling the repo has (monorepo
   project listing, workspace manifest, or the directory tree) and match the user's
   domain keywords. List the 1–5 most likely modules; when ambiguous, ask.
2. **Dependency ripple** — if the impacted code is shared (a lib/ package other
   modules import), identify downstream consumers via the workspace graph or grep on
   imports. Shared-code touch = high blast radius; flag it in Signals & blockers.
3. **Existing patterns** — `rg` the domain keywords; report 2–5 references as
   `path:line — description`. Skip for greenfield modules with no precedent.

While scanning, collect two things that are cheap now and expensive to backfill:
- For behavior changes: one **concrete input with its current output**, so the draft
  can show a real before/after.
- For any technical claim you're tempted to cite (SQL patterns, regex, collation,
  operator behavior): **read the actual code path**. The self-review checklist will
  reject unverified claims.

---

## Phase 4 — Draft the ticket

Follow `references/ticket-template.md`. The non-negotiables:

- **WHAT/WHY in the body; technical context separated** and marked informative.
- **Acceptance criteria numbered and atomic**: EARS statements
  (`WHEN <trigger> THE SYSTEM SHALL <behavior>`, `IF <bad condition> THEN …`) for
  system behavior; Given/When/Then for user-facing scenarios. One behavior per
  statement. These IDs (FR-n / AC-n.m) are the traceability contract downstream —
  `implement-ticket` maps every AC to ≥ 1 task and ≥ 1 test.
- **User stories prioritized P1/P2/P3 and independently testable** when the feature
  has more than one story. P1 is the MVP slice.
- **Success criteria SC-n measurable and time-bound** — including a post-deploy
  metric when the change is deployable.
- **Edge cases via EARS IF/THEN** — forces error-path thinking.
- **Bugs/incidents**: evidence over stories — observed signal section with dates,
  entity IDs (synthetic/redacted if sensitive), volume, trace links.
- **Language**: body in the user's working language; never translate identifiers,
  paths, API names, or settled technical terms.

**Granularity rule:** more than ~5 FRs → propose a split (Epic + child tickets, or
sequential tickets with the P1 slice first) before finishing the draft.

---

## Phase 5 — Self-review (mandatory, before presenting)

Run `references/spec-checklist.md` on the draft. Fix violations; convert unfixable
unknowns into markers. Only then show the draft.

---

## Phase 6 — Optional: seed a Conductor track

If `conductor/` exists, offer once: create `conductor/tracks/<slug>_<YYYYMMDD>/`
with a `spec.md` derived from the ticket (Overview, Functional Requirements,
Non-Functional Requirements, Acceptance Criteria, Out of Scope — Conductor's
sections, carrying over the FR/AC IDs) plus `metadata.json` and an entry in the
tracks registry.

Division of truth when the user accepts (the What/How separation):
- The **ticket** carries intent, priority, routing, and a link
  (`Spec: conductor/tracks/<id>/`).
- The **repo spec** is the normative artifact the implementation will follow.
- Never mirror plan tasks into tracker sub-tasks — that's double bookkeeping that
  drifts; the plan lives in the repo.

If the user declines or Conductor is absent, skip silently — `implement-ticket`
can create the track later from the ticket itself.

---

## Phase 7 — Validate & push

1. Present the draft in readable form, with the "Open questions" list surfaced
   prominently, and confirm the tracker-side routing (labels, milestone/epic,
   assignee — whatever the adapter supports). Ask for values you don't know; never
   guess picklists.
2. **Unresolved markers gate:** if `[NEEDS CLARIFICATION]` markers remain, ask the
   user to either resolve them now or explicitly confirm they ship as open
   questions. Never push markers silently converted into assumptions.
3. On approval, push via the adapter (`create` or `update` per mode). Respect the
   single-write-boundary rule: if a project tracker skill exists, delegate to it.
4. Report the ticket URL/ID, and if a Conductor track was seeded, the spec path.

---

## Integration contract with `implement-ticket`

Every ticket this skill produces MUST contain:
- a non-empty, numbered Acceptance Criteria section,
- a Definition of Done checklist,
- an empty-or-resolved Open questions section (no silently dropped markers).

`implement-ticket` Phase 1 hard-stops on missing ACs or DoD and re-opens
unresolved markers as a clarify loop. Producing a draft without these breaks the
handoff.

## What this skill does NOT do

- Implement code or open PRs — that's `implement-ticket`.
- Bypass a project's dedicated tracker skill — single write boundary.
- Exhaustive requirements elicitation — suggest a brainstorming skill instead.
- Invent tracker-specific metadata (custom fields, workflows) — ask or delegate.

Stay light: a straightforward ticket should take under 5 minutes of interactive
time end-to-end. The ceremony above is for correctness, not volume.
