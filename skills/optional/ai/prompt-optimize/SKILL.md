# prompt-optimize — Optimize LLM Prompts for Cost and Quality

## When to Use
Sr AI uses this after `prompt-audit` identifies optimization opportunities.

## Inputs
- Prompt to optimize (identified by prompt-audit)
- Golden test dataset for the prompt
- Current performance baseline (quality score, token count, cost)

## Procedure

1. Identify the prompt to optimize (from prompt-audit findings)
2. Measure current performance:
   - Quality score against golden dataset
   - Token count (system prompt + average input + average output)
   - Cost per call
3. Apply optimization techniques:
   - Remove redundant or repeated instructions
   - Compress few-shot examples (fewer examples, more representative)
   - Use structured output format (JSON schema) to reduce output tokens
   - Replace verbose instructions with concise directives
4. Test optimized prompt against the full golden dataset
5. Compare quality before and after:
   - If quality maintained (< 5% regression) and tokens reduced, propose change
   - If quality dropped, revert and try a different optimization
6. Post optimization proposal to #research:

```markdown
---
agent: sr-ai
type: proposal
severity: info
tags: [prompt-optimize]
requires: review
---

## Prompt Optimization Proposal

### Prompt: {prompt name} ({file path})
### Before: {token count} tokens, ${cost}/call, quality: {score}/5
### After: {token count} tokens, ${cost}/call, quality: {score}/5
### Token Reduction: {%}
### Cost Savings: ${amount}/month at current volume
### Quality Change: {unchanged | +/- %}
### Changes Made: {summary of what was changed}
### Rollback: keep old prompt as {fallback file path}
```

## Output Format
Prompt optimization proposal posted to #research (see template above).

## Rules
- Never optimize for cost at the expense of quality below acceptable threshold (quality regression > 5% is rejected)
- Always A/B test against the golden dataset before proposing changes
- Keep the old prompt as a fallback — never delete the previous version
- One optimization at a time — do not bundle multiple prompt changes
