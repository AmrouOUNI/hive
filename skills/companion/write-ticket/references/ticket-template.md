# Spec-first ticket template

Render in the tracker's native markup (markdown for GitHub/Linear/local, wiki markup
for Jira display drafts). Body language: the user's working language; never translate
code identifiers, paths, API names, or established technical terms.

Sections marked *(when applicable)* are omitted, not left empty. Everything else is
mandatory — `implement-ticket` hard-stops on missing Acceptance Criteria or DoD.

```markdown
## 🎯 Context & Problem

<Current situation, observed pain, triggering signal. WHAT and WHY only — no
implementation here.>

<!-- Behavior-changing tickets: include a before/after comparison for one concrete
input. Reviewers must be able to verify the change solves the problem without
mentally simulating it. -->
**Current behavior:** <input → current output>
**Expected behavior:** <same input → new output>

<!-- Incident/bug tickets: replace user stories with evidence. -->
## 🔬 Observed signal *(bugs/incidents)*
- **Occurrence:** <date, environment>
- **Entities:** <real IDs, counts — synthetic/redacted if sensitive>
- **Volume / impact:** <numbers>
- **Trace / logs:** <link to APM trace or log query>

## 💡 Intent

As a <persona>, I want <capability> so that <measurable value>.

## 📏 Scope

**In:**
- <delivered>

**Out:**
- <explicitly excluded, with one-line reason>

## 👥 User stories *(features with >1 story)*

<!-- Each story independently testable — it can ship and be verified alone.
     P1 = MVP slice. If you can't order them, you don't understand the feature yet. -->
- **US1 (P1):** As a …, I want … — *independent test: <how to verify this slice alone>*
- **US2 (P2):** …

## ✅ Requirements & Acceptance Criteria

<!-- One behavior per statement. EARS for system behavior, Given/When/Then for
     user-facing scenarios. Number everything — implementation tasks and tests will
     reference these IDs. -->

**FR-1 — <requirement name>**
- AC-1.1: WHEN <trigger/condition> THE SYSTEM SHALL <observable behavior>
- AC-1.2: IF <undesired condition> THEN THE SYSTEM SHALL <error behavior>

**FR-2 — <requirement name>**
- AC-2.1 *(scenario)*:
  - Given <initial state>
  - When <action>
  - Then <observable result>

## ⚠️ Edge cases

<!-- Force error-path thinking with the EARS IF/THEN pattern. Null/empty, boundary,
     concurrency, permission-denied, partial failure. -->
- IF <edge condition> THEN THE SYSTEM SHALL <behavior>

## 📐 Success criteria

<!-- Measurable, time-bound, observable in production — CI-green proves it merged,
     these prove it worked. -->
- SC-1: <metric + threshold + window — e.g. "0 occurrences of error X over 7 days
  post-deploy, verified in <monitoring tool>">
- SC-2: <e.g. "p95 latency < N ms sustained over 7 days">

## 🗺️ Technical context *(auto-detected — informative, not normative)*

- **Impacted modules/projects:** <list>
- **Dependency ripple:** <upstream/downstream, flag shared-code blast radius>
- **Relevant existing code:**
  - `path:line` — <description>
- **Spec:** <link to conductor/tracks/<id>/ when a Conductor track was seeded>

## ⚠️ Signals & blockers *(when detected)*

- [ ] <blocker with owner/dependency>

## ❓ Open questions

<!-- Every unresolved [NEEDS CLARIFICATION] marker lands here. The ticket can be
     pushed with open questions ONLY if the user explicitly accepts them as
     questions to the reporter/PO — never as silent assumptions. -->
- [NEEDS CLARIFICATION: <specific question>]

## ✅ Definition of Ready

- [ ] Outcome validated by owner/PO
- [ ] Scope In/Out explicit
- [ ] Acceptance criteria testable (every AC maps to a verifiable behavior)
- [ ] Open questions resolved or explicitly accepted
- [ ] Upstream blockers resolved or assigned

## 🏁 Definition of Done

- [ ] Tests per project convention (see project workflow/conventions)
- [ ] Lint / typecheck / build pass on impacted modules
- [ ] Branch and commit naming per project convention
- [ ] Code review completed
- [ ] SC metrics verified post-deploy (see Success criteria)
```

## Title rules

- Concise, imperative or noun phrase; aim ≤ 70 chars (hard ceiling ~90).
- The title answers "what changes if this ships?" — never opaque codenames.
- No user-story form in the title; that belongs in Intent.
- Preserve the project's existing prefix conventions (`[Front]`, `[API]`, service
  names…) if the tracker history shows them; don't invent new ones.
