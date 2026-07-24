---
name: research-partnership-scout
description: Identify and score potential integration partners during deep scan cycles.
---

# partnership-scout — Scout Integration Partners

## When to Use
Scout uses this during deep scan cycles. Identifies and evaluates potential integration partners that could extend the product's capabilities or reach.

## Inputs
- Product roadmap and feature backlog (from `#roadmap`)
- Current integrations list
- Market landscape from previous trend reports
- User requests for integrations (from `#customer`)

## Procedure

1. Identify partner categories relevant to the product (from the roadmap, current integrations, and user integration requests)
2. For each candidate partner, research:
   - **What they do** — core product and API capabilities
   - **User overlap** — do their users match the product's target audience?
   - **Technical compatibility** — REST/GraphQL API, webhook support, auth model, rate limits
   - **Business model alignment** — free tier available, partner program, pricing model
3. Score opportunity 1-5:
   - **5**: Strong user overlap, excellent API, clear mutual value, easy integration
   - **4**: Good overlap, solid API, meaningful value, moderate effort
   - **3**: Some overlap, adequate API, incremental value
   - **2**: Limited overlap or weak API, questionable value
   - **1**: Poor fit on multiple dimensions
4. Post to `#research`:

```markdown
---
agent: scout
type: research
severity: info
tags: [partnership, integration]
mentions: [@product-chief]
requires: review
---

## Partnership Scout — {date}

### Partner: {name}
- **Category**: {partner category}
- **What they do**: {one-sentence summary}
- **User Overlap**: {high / moderate / low} — {explanation}
- **Technical Compatibility**: {API quality, auth model, limitations}
- **Business Alignment**: {pricing, partner program, terms}
- **Opportunity Score**: {1-5}
- **Integration Effort**: {S/M/L/XL}
- **Recommendation**: {pursue / monitor / skip}

### Partner: {name}
...
```

## Output Format
Single post to `#research` in the template above. Multiple partners can be in one report.

## Rules
- Always check if the partner is already integrated or on the backlog before reporting
- Technical compatibility must include API quality — "they have an API" is not enough detail
- Score reflects total opportunity, not just one dimension
- "Pursue" recommendations should include a suggested first step (e.g., "test their API with sample data")
