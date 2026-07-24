# cross-agent-analysis — Detect Cross-Domain Correlations

## When to Use
Data Analyst uses this every 6 hours to find correlations across agent domains that no single agent would see on their own.

## Inputs
- All `agents/*/context.md` files (current state of each agent)
- All `agents/*/last-report.md` files (latest output from each agent)
- Deployment logs
- Incident history

## Procedure

1. Read all agent context and last-report files
2. Build a timeline of recent events across all domains:
   - Deployments (from devops/sr-backend)
   - Error spikes (from observability)
   - Churn signals (from CS Lead)
   - Feature gaps (from product)
   - Velocity changes (from scrum/architecture)
3. Look for correlations:
   - Error spikes coinciding with deployments
   - Churn signals coinciding with feature gaps or bugs
   - Velocity drops following architectural changes
   - Support ticket spikes after releases
   - Customer health changes after incidents
4. Score each correlation by confidence (high/medium/low) and impact
5. Post correlations to `#research`:

```markdown
---
agent: data-analyst
type: insight
severity: info
tags: [cross-agent-analysis]
channel: #research
---

## Cross-Agent Correlations — {timestamp}

### Correlations Found ({count})

#### {Correlation 1 — e.g., "Churn spike follows enrichment errors"}
- **Domains**: {agent A} + {agent B}
- **Evidence**: {specific data points}
- **Confidence**: {high | medium | low}
- **Impact**: {description}
- **Suggested action**: {what to investigate or do}

#### {Correlation 2}
...
```

## Output Format
Markdown insight report posted to `#research` with each correlation detailed with evidence, confidence, and suggested action.

## Rules
- Report correlations, not causations — let humans determine causality
- Minimum two data points from different agents to claim a correlation
- Low-confidence correlations are still worth reporting — flag them as such
- Run every 6 hours to catch time-sensitive patterns
- Never modify agent context files — read only
