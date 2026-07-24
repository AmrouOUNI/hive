---
name: data-conversation-mine
description: Weekly mining of conversational-interface logs for user behavior patterns, friction points, and feature discovery insights.
---

# conversation-mine — Analyze Bot Conversation Patterns

## When to Use
Data Analyst uses this weekly to mine the product's conversational-interface logs (chat/bot channels) for user behavior patterns, friction points, and feature discovery insights.

## Inputs
- Conversation logs via the `customer.activity` adapter — see `.claude/hive/adapters/customer-activity.md`
- Feature usage mapping (which conversation patterns map to which features)

## Procedure

1. Pull all conversations from the past week via the `customer.activity` adapter
2. Analyze conversation patterns:
   - **Most common requests**: What do users ask for most frequently?
   - **Friction points**: Where do users get stuck, retry, or abandon?
   - **Organic discovery**: Which features do users find and use without being prompted?
   - **Ignored features**: Which features are available but never triggered in conversations?
   - **Error encounters**: Where do conversations hit errors or dead ends?
3. Categorize findings by impact:
   - High: Affects >30% of conversations
   - Medium: Affects 10-30% of conversations
   - Low: Affects <10% but still notable
4. Post insights to `#product` + `#research`:

```markdown
---
agent: data-analyst
type: insight
severity: info
tags: [conversation-mine, weekly]
channels: [#product, #research]
---

## Conversation Mining — {week}

### Top Requests ({count} conversations analyzed)
| Request Type | Frequency | % of Total |
|-------------|-----------|------------|
| ...         | ...       | ...        |

### Friction Points
| Where | What Happens | Frequency | Impact |
|-------|-------------|-----------|--------|
| ...   | ...         | ...       | ...    |

### Organic Feature Discovery
- {feature}: discovered by {n} users without prompting

### Ignored Features
- {feature}: available but used by <{n}% of users

### Recommendations
- {recommendation based on patterns}
```

## Output Format
Markdown insight report posted to `#product` and `#research` with categorized conversation patterns and recommendations.

## Rules
- Never include raw conversation content — summarize patterns only
- Respect user privacy — aggregate data, never report individual user conversations
- Friction points are the highest priority finding — they directly affect retention
- Compare with previous week to identify new vs recurring patterns
- Recommendations must be specific and actionable, not vague
