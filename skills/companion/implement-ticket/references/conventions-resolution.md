# Conventions resolution — protocol vs policy

The pipeline (SKILL.md) is the **protocol**: phases, gates, artifacts. It contains
zero project methodology on purpose. Everything project-specific is **policy**,
resolved here once in Phase 0 and treated as the single source of truth afterwards.

## Precedence order

For each policy item, take the first source that answers it:

1. **`conductor/workflow.md`** (and `conductor/tech-stack.md`,
   `conductor/code_styleguides/`) — the project's deliberate, versioned policy.
2. **Project agent instructions** — `CLAUDE.md` / `AGENTS.md` / equivalent at the
   repo root (and the user's stored memory for this repo).
3. **Auto-detection** — infer from the repo itself (see below).
4. **Ask the user** — one grouped question, not a drip-feed. Record answers; never
   re-ask within the session.

If sources conflict, `conductor/workflow.md` wins; surface the conflict once.

## Policy items to resolve

| Item | Auto-detection fallback |
|---|---|
| Test / lint / typecheck / build commands | Manifest scripts (`package.json`, `Makefile`, `justfile`, `pyproject.toml`, `Cargo.toml`…), CI config (`.github/workflows/`) |
| Test strategy (which flows get which test type; TDD?) | Mirror what exists: look at test files near the code to be touched and follow their pattern. Never introduce a new test type into a codebase that has an established one for that layer |
| Coverage expectations | CI config thresholds; else don't invent one |
| Branch naming | `git log --oneline -20` + `git branch -r` — infer prefix/slug pattern |
| Commit message format | `git log --oneline -30` — infer (conventional commits, ticket-id prefixes, etc.) |
| Base branch | Repo default branch unless the user says otherwise (stacked branches) |
| Commit granularity (per task vs per phase) | Default: per task |
| Bookkeeping commits (`conductor(plan):` separate from code commits) | Default: yes when `conductor/` is versioned; see `plan-format.md` |
| Spec/plan artifacts committed? | Default: **yes**, Conductor-style (`conductor/tracks/` versioned). A project may override in workflow.md or CLAUDE.md ("do not commit spec documents") — respect it and keep artifacts out of the PR |
| Review agents | Project-declared specialist agents (CLAUDE.md / agent catalog) if any; else generic reviewers |
| Architecture layering (implementation order) | Infer from directory structure (e.g. domain → infrastructure → application; or model → service → route). Follow the codebase's own dependency direction |

## Hard rules (project-independent — always apply)

1. **Never re-ask** questions already answered in the session.
2. **Never change code outside the ticket's scope.**
3. **Verify before each commit** — run the resolved lint/typecheck/test commands
   BEFORE committing, not after.
4. **Validate before fixing** — confirm a root cause with evidence (logs, queries,
   reproduction) before proposing a fix.
5. **Push via HTTPS** and create the PR with the platform CLI (`gh` or equivalent).
6. **Prefer non-interactive commands** — `CI=true`, `--no-watch`, bounded
   parallelism if the user's environment requires it.
7. **Tracker writes go through the resolved adapter only** (see
   `tracker-adapters.md`), and through the project's tracker skill when one exists.

## Deviation brake (Conductor)

If during implementation the plan diverges from the documented tech stack (new
dependency, different storage, new framework usage), **STOP before writing the
code**: propose a dated update to `conductor/tech-stack.md` (or flag the deviation
to the user when Conductor is absent), get approval, then resume. Stack changes are
deliberate, never incidental.
