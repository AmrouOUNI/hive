---
name: research-source-evaluate
description: Rate a new information source on reliability and relevance, then track, watch, skip, or blacklist it.
---

# source-evaluate — Evaluate Information Sources

## When to Use
Scout uses this when encountering a new information source (blog, API, newsletter, database, community forum). Determines if it should be tracked, ignored, or blacklisted.

## Inputs
- The source URL or reference
- Sample content from the source
- Scout's existing source registry (in `agents/core/scout/context.md`)

## Procedure

1. Access the source and review a sample of its content
2. Rate **reliability** (1-5):
   - 5: Primary source, verified data, consistent accuracy
   - 4: Reputable secondary source, mostly accurate
   - 3: Mixed quality, useful but needs cross-referencing
   - 2: Frequent inaccuracies or heavy bias
   - 1: Unreliable, misleading, or spam
3. Rate **relevance** to the product's domain (1-5):
   - 5: Directly covers the product's domain or core workflows (as described in config.json)
   - 4: Adjacent space with frequent relevant insights
   - 3: Occasionally relevant content mixed with unrelated topics
   - 2: Rarely relevant
   - 1: No relevance to the product's domain
4. Assess **update frequency**: daily / weekly / monthly / irregular / stale
5. Assess **accessibility**: open / free-with-signup / paid / API-available / restricted
6. Determine action:
   - **Track**: reliability >= 3 AND relevance >= 3 — add to source registry
   - **Watch**: reliability >= 3 OR relevance >= 4 — check periodically
   - **Skip**: low scores, not worth monitoring
   - **Blacklist**: reliability = 1 or known misinformation — never use again
7. Update `agents/core/scout/context.md` source registry accordingly

```markdown
---
agent: scout
type: research
severity: info
tags: [source-eval]
mentions: []
requires: read
---

## Source Evaluation: {source name}

- **URL**: {url}
- **Type**: {blog / newsletter / API / database / forum / social}
- **Reliability**: {1-5} — {rationale}
- **Relevance**: {1-5} — {rationale}
- **Update Frequency**: {daily / weekly / monthly / irregular / stale}
- **Accessibility**: {open / free-with-signup / paid / API / restricted}
- **Action**: {track / watch / skip / blacklist}
- **Notes**: {any additional context}
```

## Output Format
Update to `agents/core/scout/context.md` source registry. Optionally post to `#research` if the source is notable (score >= 4 on both dimensions or blacklisted).

## Rules
- Every new source must be evaluated before being used in reports
- Blacklisted sources must include the reason — this protects future scouts
- Reliability must be assessed on content accuracy, not just reputation
- Re-evaluate tracked sources quarterly — quality changes over time
