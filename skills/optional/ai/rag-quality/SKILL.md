# rag-quality — RAG Retrieval Quality Check

## When to Use
Sr AI uses this at Stage 3+ when RAG is implemented, or during enrichment quality checks.

## Inputs
- Known test queries with expected retrieved documents
- Embedding model configuration
- Index freshness metadata

## Procedure

1. Test retrieval accuracy on known queries:
   - Run test queries against the retrieval system
   - Compare retrieved documents to expected results
   - Score relevance of each retrieved chunk (1-5)
2. Measure chunk relevance scores (average and distribution)
3. Check for retrieval failures:
   - Queries that return no results
   - Queries that return only irrelevant results
4. Evaluate embedding model quality:
   - Semantic similarity between query and top results
   - Compare to alternative embedding models if quality is low
5. Check index freshness:
   - When was the index last updated?
   - Are new documents indexed within acceptable delay?
6. Post quality report to #research:

```markdown
---
agent: sr-ai
type: report
severity: {info | warning}
tags: [rag-quality]
requires: {ack | action}
---

## RAG Quality Report

### Test Queries: {count}
### Retrieval Accuracy: {%} (target: 80%+)
### Average Relevance Score: {score}/5
### Failed Retrievals: {count} ({list of failing queries})
### Index Freshness: last updated {timestamp}, delay: {duration}
### Embedding Model: {model name}
### Status: {healthy | degraded | poor}
```

## Output Format
RAG quality report posted to #research (see template above).

## Rules
- Not applicable at Stage 2 (no RAG) — skip this skill entirely
- At Stage 3+, run weekly
- Retrieval accuracy < 80% requires investigation and fix
- Index freshness > 24h is a warning — new data should be indexed promptly
