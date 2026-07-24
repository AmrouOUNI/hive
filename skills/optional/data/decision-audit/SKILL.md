# decision-audit — Review Decision Outcomes

## When to Use
Data Analyst uses this monthly to audit past decisions, score their outcomes, and identify decision-making patterns.

## Inputs
- All decisions from `#decisions` channel (labeled approved/rejected)
- Outcome data for each decision (what happened after)
- Timeline of events post-decision

## Procedure

1. Collect all decisions from `#decisions` in the past month
2. For each decision:
   - **What was decided**: Summary of the proposal and outcome (approved/rejected)
   - **What happened after**: Trace the downstream effects
   - **Outcome assessment**: Was the result positive, neutral, or negative?
3. Score decision quality:
   - **Good**: Intended outcome achieved, no significant downsides
   - **Neutral**: No clear positive or negative impact
   - **Poor**: Negative outcome, unintended consequences, or reversal needed
4. Identify patterns:
   - Which types of decisions consistently go well?
   - Which types tend to have poor outcomes?
   - Are there decision-makers or domains with better track records?
5. Post audit to `#research`:

```markdown
---
agent: data-analyst
type: report
severity: info
tags: [decision-audit, monthly]
channel: #research
---

## Decision Audit — {month}

### Decisions Reviewed: {count}

| Decision | Date | Type | Outcome | Score |
|----------|------|------|---------|-------|
| ...      | ...  | ...  | ...     | ...   |

### Quality Summary
- Good: {count} ({%})
- Neutral: {count} ({%})
- Poor: {count} ({%})

### Patterns
- {pattern 1 — e.g., "Architecture decisions made with prototypes score higher"}
- {pattern 2}

### Recommendations
- {recommendation for improving decision quality}
```

## Output Format
Markdown audit report posted to `#research` with decision table, quality scores, patterns, and recommendations.

## Rules
- Score decisions based on outcomes, not intentions
- Some decisions are too recent to evaluate — mark as "pending" and revisit next month
- Never blame agents for poor decisions — focus on improving the process
- Include rejected proposals too — sometimes not doing something was the right call
- Minimum 30-day window before scoring a decision (outcomes need time to materialize)
