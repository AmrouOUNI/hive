---
name: architecture-frontend-boundary-audit
description: Audit the frontend codebase against Feature-Sliced Design — layer structure, import direction, slice public APIs, logic placement
---

# frontend-boundary-audit — Verify FSD Boundaries Hold

## When to Use
Architect runs this weekly (alternating or alongside `bounded-context-audit`), or on demand after a large frontend change. Only relevant from Stage 2 (see `protocols/project-maturity.md` — at Stage 1 a flat structure is acceptable; audit only type safety and logic placement).

## Reference Model — Feature-Sliced Design

Layers, top to bottom. **Imports may only point downward.**

```
app        composition root: providers, router, global styles
pages      route-level composition of widgets/features
widgets    self-contained UI blocks composing features/entities
features   user interactions carrying business value
entities   business domain objects and their UI representations
shared     framework-agnostic kit: ui, lib, api, config — knows NOTHING of the domain
```

Each slice exposes a **public API** (index file); other slices import from that public API only, never from internal files.

## Inputs
- Frontend source tree (locate via repo layout; confirm root in `.claude/hive/config.json` notes if ambiguous)
- `.claude/hive/context/architect.md` — Frontend Slice Map, known violations
- Lint config — are boundary rules enforced mechanically yet? (expected from Stage 3)

## Procedure

1. **Map the layers** — List top-level folders of the frontend source. Match them to FSD layers (names may differ; record the mapping). Flag structures that fit no layer.

2. **Import direction scan** — For each layer, search imports pointing to a HIGHER layer (e.g. `entities/*` importing from `features/*`, `shared/*` importing anything domain). Each hit is a violation.

3. **Deep-import scan** — Find imports that bypass a slice's public API (path reaching into a slice's internals from outside the slice).

4. **Cross-slice coupling** — Within `features/` and `entities/`, find slices importing sibling slices directly. Sideways coupling belongs in a higher layer (widget/page composes both) or lower layer (shared kernel).

5. **Logic placement spot-check** — Sample recently changed UI components (`git log --since="7 days ago"` on the frontend tree): business rules, data mapping, or fetch orchestration living inside render components instead of the slice's model/api segments.

6. **Shared kit hygiene** — Does `shared/` import from any domain layer? Has domain vocabulary leaked into shared component names?

7. **Enforcement check (Stage 3+)** — Are the above rules encoded in lint config? If manual-only, recommend mechanical enforcement as a P2 item.

8. **Classify findings**:
   - **HIGH**: upward imports, domain logic in `shared/`, business rules in render components on hot paths
   - **MEDIUM**: deep imports bypassing public APIs, sibling-slice coupling
   - **LOW**: naming drift, missing public API files, unenforced-but-respected rules

9. **Update context** — Refresh the Frontend Slice Map and violations table in `.claude/hive/context/architect.md`.

## Output

Post to `#architecture`:

```markdown
# Frontend Boundary Audit — {date}

## Layer Map
| FSD layer | Project folder | Slices |

## Violations
| Severity | Rule | Location | Recommendation |

## Trend
{n} violations ({+/-n} vs last audit) · enforcement: {manual | lint-enforced}

## Recommended Actions
| Priority | Action | Trigger/Deadline |
```

## Constraints
- Do NOT modify code — report and recommend only
- Judge against the project's declared stage; do not demand Stage 3 enforcement from a Stage 2 project
- If the project has no frontend, exit silently
- If the frontend predates FSD adoption, propose an incremental migration path (strangler per slice), never a big-bang rewrite
