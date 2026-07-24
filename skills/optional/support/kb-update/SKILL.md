# kb-update — Add New Entry to Knowledge Base

## When to Use
Support uses this after resolving a novel issue that has no existing knowledge base entry. Captures the resolution for future auto-resolve use.

## Inputs
- Resolved ticket (question + answer)
- Context of the resolution
- Related links (docs, GH issues, discussions)

## Procedure

1. Verify the resolution is novel — search KB to confirm no existing entry covers it
2. Write a new KB entry:
   - **Question**: The question as a user would ask it
   - **Answer**: Clear, concise resolution
   - **Context**: When this applies, edge cases, prerequisites
   - **Related links**: Docs, GH issues, or discussions
   - **Tags**: For searchability
3. Add the entry to the knowledge base directory
4. Track KB gap metrics (count of new entries per week)
5. Post update to `#customer`:

```markdown
---
agent: support
type: kb-update
severity: info
tags: [kb-update]
channel: #customer
---

## KB Entry Added

**Question**: {question}
**Answer**: {concise answer}
**Source ticket**: #{ticket-id}
**Tags**: {tag1, tag2}

KB gap this week: {count} new entries added
```

## Output Format
New markdown file in the knowledge base directory + notification posted to `#customer`.

## Rules
- Never duplicate an existing KB entry — update the existing one if it needs refinement
- Write answers for a non-technical audience unless the topic is inherently technical
- Keep answers under 200 words — link to detailed docs for depth
- Every auto-resolve "no match" case should eventually result in a kb-update
- Review KB entries monthly to prune outdated content
