# weekly-insights — Compile Unsolicited Weekly Insights

## When to Use
Data Analyst uses this Monday morning to surface the top 3-5 insights from the past week that nobody explicitly asked for but the team should know.

## Inputs
- All agent reports from the past week
- KPI dashboard data (7-day window)
- Cross-agent analysis outputs
- Conversation mining outputs
- Incident and deployment history

## Procedure

1. Review all data and reports generated in the past week
2. Identify insights that:
   - Were not explicitly asked for by any agent or human
   - Reveal unexpected correlations or emerging patterns
   - Highlight efficiency opportunities being missed
   - Surface risk signals before they become problems
3. Select the top 3-5 most impactful insights
4. For each insight, provide:
   - What was found
   - Why it matters
   - What to do about it (or what to investigate further)
5. Post to `#research`, tag CTO + Product Chief:

```markdown
---
agent: data-analyst
type: insight
severity: info
tags: [weekly-insights, monday]
channel: #research
mentions: [@cto, @product-chief]
---

## Weekly Insights — {week}

### 1. {Insight title}
**What**: {what the data shows}
**Why it matters**: {impact or risk}
**Suggested action**: {what to do}

### 2. {Insight title}
**What**: {what the data shows}
**Why it matters**: {impact or risk}
**Suggested action**: {what to do}

### 3. {Insight title}
...

---
*These insights were not requested — they emerged from cross-referencing this week's data.*
```

## Output Format
Markdown report posted to `#research` with 3-5 numbered insights, each with what/why/action structure. Tags CTO and Product Chief.

## Rules
- Maximum 5 insights — quality over quantity, do not dilute with noise
- Minimum 3 insights — if the week was quiet, dig deeper
- Insights must be non-obvious — do not restate what agents already reported
- Each insight must have a suggested action — observations without actions are not useful
- Post Monday morning so the team starts the week informed
- Never include insights that are already being actively addressed — focus on blind spots
