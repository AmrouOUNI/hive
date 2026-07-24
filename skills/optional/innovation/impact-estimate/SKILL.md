# impact-estimate — Estimate Feature Impact

## When to Use
Innovator uses this after feasibility assessment. Evaluates the potential user and business impact of a proposed feature to produce a priority recommendation.

## Inputs
- Proposed feature idea (from ideation)
- Feasibility assessment (from feasibility skill)
- Customer data from `#customer` (org count, usage patterns)
- Competitive landscape from Scout's reports

## Procedure

1. Read the feature idea and its feasibility score
2. Estimate user reach — how many orgs or users would this affect?
3. Estimate engagement lift — would this increase usage frequency, session depth, or activation?
4. Estimate retention improvement — does this address a churn driver or stickiness gap?
5. Assess competitive differentiation — does this create distance from alternatives or is it table-stakes?
6. Score impact 1-5:
   - **5**: Affects majority of users, strong engagement/retention lift, major differentiator
   - **4**: Affects many users, meaningful lift in one dimension, noticeable differentiator
   - **3**: Affects a segment, moderate lift, useful but not unique
   - **2**: Affects few users, marginal lift, commoditized feature
   - **1**: Niche impact, no measurable lift expected
7. Combine with feasibility score for a priority recommendation:
   - **High priority**: Impact >= 4 AND Feasibility >= 4
   - **Medium priority**: Impact + Feasibility >= 6
   - **Low priority**: Impact + Feasibility < 6
8. Post to `#features` as a reply to the feasibility assessment:

```markdown
---
agent: innovator
type: analysis
severity: info
tags: [impact, priority]
mentions: [@product-chief]
requires: review
---

## Impact Estimate: {feature name}

### User Reach
{estimate — how many orgs/users affected}

### Engagement Lift
{low / moderate / high} — {rationale}

### Retention Improvement
{low / moderate / high} — {rationale}

### Competitive Differentiation
{table-stakes / useful / strong differentiator} — {rationale}

### Impact Score: {1-5}
### Feasibility Score: {1-5} (from assessment)
### Priority Recommendation: {High / Medium / Low}
{one-sentence justification}
```

## Output Format
Single post to `#features` (as reply to feasibility) in the template above.

## Rules
- Impact without feasibility is wishful thinking — always pair them
- Be conservative on estimates — overpromising impact erodes trust
- "Table-stakes" features can still be high priority if they block adoption
- Priority recommendation is a suggestion, not a decision — Product Chief and CTO decide
