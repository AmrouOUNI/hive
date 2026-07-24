# prototype-brief — Write Prototype Brief

## When to Use
Innovator uses this when a feature idea scores high on both feasibility (>= 4) and impact (>= 4). Produces a concise brief for review by Product Chief and Architect before implementation begins.

## Inputs
- Feature idea (from ideation)
- Feasibility assessment (score >= 4)
- Impact estimate (score >= 4)
- Relevant ADRs and tech-stack context

## Procedure

1. Verify prerequisite scores — feasibility >= 4 AND impact >= 4. If not met, do not proceed
2. Write the brief with these sections:
   - **Problem** — the user pain point, grounded in data (customer signal, scout report)
   - **Proposed Solution** — what the feature does, in plain language
   - **User Flow** — 3-5 steps showing how a user interacts with it
   - **Technical Approach** — high-level architecture: which layers, services, and modules are involved
   - **Effort Estimate** — T-shirt size with rough timeline
   - **Success Metric** — one measurable outcome that proves the feature works
3. Post brief to `#features`, tag Product Chief + Architect:

```markdown
---
agent: innovator
type: proposal
severity: info
tags: [prototype-brief]
mentions: [@product-chief, @architect]
requires: approval
---

## Prototype Brief: {feature name}

### Problem
{1-2 sentences, cite the source signal}

### Proposed Solution
{2-3 sentences, what the feature does}

### User Flow
1. {step}
2. {step}
3. {step}
4. {step (optional)}
5. {step (optional)}

### Technical Approach
- Layers affected: {domain / application / infrastructure / presentation}
- Key components: {modules, services, aggregates}
- Dependencies: {external APIs, new libs}

### Effort Estimate
{S/M/L/XL} — approximately {timeframe}

### Success Metric
{one measurable KPI: e.g., "X% of orgs use this within 2 weeks of launch"}

### Scores
- Feasibility: {score}/5
- Impact: {score}/5
```

## Output Format
Single post to `#features` in the template above. Requires approval before any implementation begins.

## Rules
- Never write a brief for a feature with feasibility or impact below 4 — it is not ready
- User flow must be concrete steps, not abstract descriptions
- Technical approach is high-level only — detailed design happens during refinement
- Success metric must be measurable — "users like it" is not a metric
- This brief is a proposal, not a commitment — it requires explicit approval to proceed
