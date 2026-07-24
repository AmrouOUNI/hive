# ideate — Generate Feature Ideas

## When to Use
Innovator runs this weekly on Monday. Synthesizes research, product backlog, and customer data into concrete feature ideas.

## Inputs
- Scout's `last-report` (market signals, competitor moves)
- Product Chief's feature backlog
- Customer data from `#customer` (pain points, requests, churn reasons)
- Current product maturity stage

## Procedure

1. Read Scout's latest report — extract emerging opportunities and threats
2. Read Product Chief's feature backlog — understand current priorities and gaps
3. Read `#customer` channel — identify recurring user pain points, top requests, churn signals
4. Cross-reference: where do market signals, backlog gaps, and user pain overlap?
5. Generate 3-5 feature ideas. For each idea, document:
   - **Problem it solves** — specific user pain point or market gap
   - **Target user** — who benefits most
   - **Rough effort estimate** — T-shirt size (S/M/L/XL)
   - **Competitive advantage** — why this matters vs alternatives
6. Post ideas to `#features`:

```markdown
---
agent: innovator
type: proposal
severity: info
tags: [ideation, weekly]
mentions: [@product-chief]
requires: review
---

## Feature Ideas — Week of {date}

### Idea 1: {name}
- **Problem**: {what pain this solves}
- **Target User**: {persona or segment}
- **Effort**: {S/M/L/XL}
- **Competitive Advantage**: {why this differentiates us}

### Idea 2: {name}
...

### Sources
- Scout report: {link}
- Customer signals: {summary of top 3 pain points}
```

## Output Format
Single post to `#features` in the template above.

## Rules
- Minimum 3, maximum 5 ideas per session — quality over quantity
- Every idea must trace back to a real signal (scout report, customer request, or backlog gap) — no ideas from thin air
- Effort estimates are rough and will be refined by feasibility — don't over-engineer them here
- Avoid duplicating ideas already in the backlog — check before proposing
