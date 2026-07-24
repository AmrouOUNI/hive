# prompt-audit — Audit LLM Prompts for Quality and Efficiency

## When to Use
Sr AI uses this during the every-4h check or after prompt changes are merged.

## Inputs
- Prompt registry in codebase (system prompts, few-shot examples, prompt templates)
- Previous audit results (for drift comparison)
- Token cost data per prompt

## Procedure

1. Scan codebase for all prompt definitions:
   - System prompts
   - Few-shot examples
   - Prompt templates and builders
2. Evaluate each prompt for:
   - Token efficiency: can it be shorter without losing quality?
   - Clarity: is the instruction unambiguous?
   - Output format: is it structured for reliable parsing?
   - Few-shot quality: are examples representative and minimal?
3. Compare prompt token costs to last audit (detect cost drift)
4. Flag prompts that have drifted in quality based on output samples
5. Post findings to #research:

```markdown
---
agent: sr-ai
type: report
severity: {info | warning}
tags: [prompt-audit]
requires: {ack | action}
---

## Prompt Audit

### Total Prompts: {count}
### Total Tokens (system prompts): {count}
### Cost Change Since Last Audit: {+/- %}

### Findings:
{For each issue:}
- **{prompt name}** ({file}): {issue description} — {recommendation}

### Prompts Without Golden Tests: {list}
```

## Output Format
Prompt audit report posted to #research (see template above).

## Rules
- Every prompt change should be tracked and compared to previous version
- New prompts must have a golden test case (expected input/output pair)
- Prompts without golden tests are flagged as non-compliant
- Focus on actionable findings — do not audit for style preferences
