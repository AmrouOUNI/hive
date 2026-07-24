# expansion-detect — Identify Expansion Candidates

## When to Use
CS Lead uses this weekly to flag orgs showing signals of growth readiness — candidates for upsell, plan upgrade, or deeper engagement.

## Inputs
- Per-org feature usage data
- Team size / professional count per org
- Enrichment volume trends (last 4 weeks)
- Feature request history per org

## Procedure

1. Pull all active orgs with usage data from the past 4 weeks
2. Evaluate each org against expansion signals:
   - **Feature breadth**: Using >80% of available features
   - **Growing team**: Increasing professional count over past 4 weeks
   - **Increasing volume**: Enrichment or conversation volume trending up 3+ consecutive weeks
   - **Feature requests**: Submitted 2+ feature requests (signal of investment in the product)
3. Score expansion readiness (count of signals, 0-4)
4. Flag orgs with 2+ signals as expansion candidates
5. Post to `#customer`:

```markdown
---
agent: cs-lead
type: report
severity: info
tags: [expansion-detect, weekly]
channel: #customer
---

## Expansion Candidates — {week}

| Org | Signals | Feature Usage | Team Growth | Volume Trend | Feature Reqs | Score |
|-----|---------|---------------|-------------|--------------|--------------|-------|
| ... | ...     | ...           | ...         | ...          | ...          | ...   |

### Recommended Actions
- {org}: {suggested next step — e.g., schedule success call, propose plan upgrade}
```

## Output Format
Markdown table posted to `#customer` with expansion-candidate orgs, their signals, and recommended next steps.

## Rules
- Only flag orgs that are healthy (Green in health-score) — expanding at-risk orgs is counterproductive
- Never promise features or upgrades — flag candidates for human follow-up
- Cross-reference with health-score to avoid false positives
- Minimum 4 weeks of data required to assess trends
