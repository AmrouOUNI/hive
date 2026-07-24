# auto-resolve — Resolve Questions and Known Issues from Knowledge Base

## When to Use
Support uses this after ticket-triage classifies an issue as "question" or matches a known issue. Searches the knowledge base for an answer and drafts a response.

## Inputs
- Triaged ticket (type: question or known-issue)
- Knowledge base directory (local md files)

## Procedure

1. Extract the core question or issue from the ticket
2. Search the knowledge base (KB) for matching entries:
   - Exact match on question/topic
   - Keyword match on related terms
   - Similar past resolutions
3. If match found:
   - Draft a response with the answer
   - Include link to relevant docs if available
   - Post resolution to `#customer`
4. If no match found:
   - Escalate to `sr-backend` (technical) or relevant agent
   - Log the KB gap for `kb-update` skill
5. Post resolution or escalation:

```markdown
---
agent: support
type: resolution
severity: info
tags: [auto-resolve]
channel: #customer
---

## Auto-Resolve — #{ticket-id}

**Status**: {resolved | escalated}
**Question**: {original question}

### Response
{drafted answer if resolved}
{escalation target + reason if not resolved}

### Source
{KB entry reference or "No KB match — escalated"}
```

## Output Format
Markdown resolution posted to `#customer` with the answer (if found) or escalation details (if not).

## Rules
- Only auto-resolve questions and known issues — never auto-resolve bugs or billing
- If confidence in KB match is low, escalate instead of guessing
- Always cite the KB source in the response
- Log every "no match" case — these feed into kb-update
- Draft responses must be accurate — never fabricate answers not in the KB
