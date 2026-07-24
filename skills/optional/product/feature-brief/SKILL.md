# feature-brief — Write a Feature Brief for Candidate Features

## When to Use
Product Chief uses this when a user insight becomes a feature candidate.

## Inputs
- User insight or market signal that triggered the feature idea
- Current product roadmap and maturity stage
- Competitive landscape

## Procedure

1. Define the problem — what user pain does this solve? Include data backing.
2. Propose a high-level solution — not implementation details, just the user experience
3. Define success metrics — how will we know it worked?
4. Calculate RICE score:
   - **Reach**: how many orgs/users benefit?
   - **Impact**: how much does it improve their workflow? (3=massive, 2=high, 1=medium, 0.5=low)
   - **Confidence**: how sure are we? (100%=high, 80%=medium, 50%=low)
   - **Effort**: person-weeks to build
5. Identify the target maturity stage and dependencies
6. Post to `#features`:

```markdown
---
agent: product-chief
type: proposal
severity: info
tags: [feature-brief]
requires: review
---

## Feature Brief: {name}

### Problem
{what user pain, with data}

### Proposed Solution
{high-level approach}

### Success Metrics
- {metric}: {target}

### RICE Score
| Reach | Impact | Confidence | Effort | Score |
|-------|--------|------------|--------|-------|
| X     | X      | X%         | Xw     | X     |

### Target Stage: {maturity stage}
### Dependencies: {list}
```

## Rules
- Every feature must have a measurable success metric — "make it better" is not a metric
- RICE score is a guide, not gospel — use it to compare candidates, not as an absolute
- Brief goes to `#features` for agent review before entering backlog
