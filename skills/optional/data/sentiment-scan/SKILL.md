# sentiment-scan — Analyze Agent Communication Sentiment

## When to Use
Data Analyst uses this weekly to assess the health of agent collaboration by analyzing tone and interaction patterns in GH Discussion threads.

## Inputs
- All GH Discussion threads from the past week
- Agent participation data (who posted, who replied)

## Procedure

1. Read all GH Discussion threads from the past week
2. Analyze each thread for tone:
   - **Constructive**: Clear proposals, respectful disagreement, building on ideas
   - **Neutral**: Informational, status updates, routine coordination
   - **Friction**: Repeated disagreements, blocked discussions, dismissive responses
   - **Toxic**: Personal attacks, passive aggression, sabotage (rare but must be caught)
3. Track agent interaction pairs — who collaborates well, who has friction
4. Identify:
   - Threads with unresolved conflicts
   - Agent pairs with repeated friction
   - Smoothly resolved discussions (positive examples)
5. Post sentiment report to `#research`:

```markdown
---
agent: data-analyst
type: report
severity: info
tags: [sentiment-scan, weekly]
channel: #research
---

## Sentiment Scan — {week}

### Overall Tone: {constructive | neutral | mixed | concerning}

### Thread Analysis
| Thread | Participants | Tone | Resolved? | Notes |
|--------|-------------|------|-----------|-------|
| ...    | ...         | ...  | ...       | ...   |

### Agent Interaction Health
| Agent Pair | Interactions | Tone Trend |
|------------|-------------|------------|
| ...        | ...         | ...        |

### Flags
- {any toxic patterns or recurring friction to address}
```

## Output Format
Markdown sentiment report posted to `#research` with thread analysis, interaction health, and flags.

## Rules
- Flag toxic patterns to Scrum Master immediately — do not wait for weekly report
- Focus on patterns, not individual incidents — one heated thread is normal
- Never identify agents in a punitive way — frame as collaboration health, not blame
- Constructive disagreement is healthy — only flag when it becomes unproductive
- Compare week over week to identify improving or degrading trends
