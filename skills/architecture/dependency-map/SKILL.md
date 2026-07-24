---
name: architecture-dependency-map
description: Map and audit module dependencies against the bounded context map, flagging new cross-module couplings.
---

# dependency-map — Map and Audit Module Dependencies

## When to Use
Architect uses this during the weekly BC audit or when reviewing a significant feature that may introduce new cross-module dependencies.

## Inputs
- `.claude/hive/context/architect.md` — bounded context map, known dependency baselines
- Codebase import statements and module configuration
- Nx project graph (if available)

## Procedure

1. **Scan dependencies** — Run `nx graph` or scan import statements across all modules:
   ```
   adapter:code.imports --scope all
   ```
   Map which modules depend on which, organized by bounded context.

2. **Detect circular dependencies** — Identify any A -> B -> A cycles, including transitive cycles (A -> B -> C -> A).

3. **Check layer violations** — Flag imports that break Clean Architecture:
   - Domain importing from Infrastructure (critical)
   - Domain importing from Application (critical)
   - Application importing from Infrastructure (warning — should go through ports)
   - Any layer importing from Presentation (critical)

4. **Check BC boundary violations** — Flag imports that cross bounded context boundaries without going through a port/adapter:
   - Direct repository access from another BC
   - Direct entity imports from another BC's domain
   - Shared mutable state between BCs

5. **Compare to baseline** — Check against last dependency map in `context.md`:
   - New dependencies introduced?
   - Removed dependencies (good)?
   - Any new violations?

6. **Post report** to `#architecture`:

```markdown
---
agent: architect
type: report
severity: {info | warning | critical}
tags: [dependency-map]
requires: {info | action}
---

## Dependency Map — {date}

### Summary
- Total modules: {count}
- Cross-BC dependencies: {count} ({delta from last audit})
- Layer violations: {count} ({delta})
- Circular dependencies: {count} ({delta})

### Violations
| Source | Target | Type | Severity |
|--------|--------|------|----------|
| {module} | {module} | {circular | layer | bc-boundary} | {critical | warning} |

### New Dependencies Since Last Audit
{list of new inter-module dependencies}

### Recommendations
{numbered list of fixes, prioritized by severity}
```

7. **Update baseline** — Write current dependency map to `.claude/hive/context/architect.md`.

## Output Format
- Dependency report posted to `#architecture`
- Updated baselines in `.claude/hive/context/architect.md`

## Rules
- Zero tolerance for domain -> infrastructure imports — always critical
- BC boundary crossings without ports are always flagged
- Circular dependencies are always at least warning severity
- Compare to baseline every time — trend matters as much as current state
