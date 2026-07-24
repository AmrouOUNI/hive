# n-plus-one-detect — Detect N+1 Query Patterns

## When to Use
Scale Chief uses this during the every-4h check or during code review.

## Inputs
- Codebase source files (ORM query patterns)
- Recent Railway/application logs
- Recent code changes (for review context)

## Procedure

1. Search codebase for ORM patterns that lazy-load relations:
   - `findMany` without `include` or explicit joins
   - Nested loops with individual queries inside
   - Drizzle queries in loops without batching
2. Check recent application logs for repeated query patterns:
   - Same query template executed N times in rapid succession (< 100ms apart)
   - Sequential identical SELECT statements differing only in WHERE value
3. Scan for missing eager loading in command/query handlers
4. For each finding, document:
   - File path and line number
   - The offending pattern
   - Suggested fix (add include/join or batch query with IN clause)
5. Post findings to #scaling:

```markdown
---
agent: scale-chief
type: report
severity: {warning | critical}
tags: [n-plus-one]
mentions: [@sr-backend]
requires: action
---

## N+1 Detection

### Found: {count} patterns

{For each finding:}
### {number}. {file}:{line}
- **Pattern**: {description of the lazy-load pattern}
- **Impact**: {estimated extra queries per request}
- **Fix**: {specific suggestion — add include, use IN clause, batch query}
```

## Output Format
N+1 detection report posted to #scaling with @sr-backend tagged (see template above).

## Rules
- Zero tolerance — every N+1 found must be fixed
- Always tag @sr-backend with specific file + line + suggested fix
- Prioritize by impact: patterns in hot paths (frequently called endpoints) first
- Check both codebase patterns (static analysis) and runtime logs (dynamic detection)
