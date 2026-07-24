# market-size — Estimate Market Size for Features or Segments

## When to Use
Product Chief uses this quarterly or when evaluating major feature decisions.

## Inputs
- Feature or segment to evaluate
- Current user base data
- Market research sources (web search)

## Procedure

1. Define the segment — who are the potential users?
2. Estimate TAM (Total Addressable Market):
   - How many companies/professionals exist in this segment?
   - Use web search for industry data
3. Estimate SAM (Serviceable Addressable Market):
   - How many of those could realistically use gotchi?
   - Filter by geography, company size, tech-savviness
4. Estimate SOM (Serviceable Obtainable Market):
   - How many could we realistically capture in 12-18 months?
   - Factor in competition, distribution, pricing
5. Size by: number of potential users x willingness to pay
6. Compare to current user base — what's the growth multiplier?
7. Post analysis to `#product`:

```markdown
---
agent: product-chief
type: analysis
severity: info
tags: [market, sizing]
---

## Market Size: {feature/segment}

| Metric | Estimate | Basis |
|--------|----------|-------|
| TAM    | {range}  | {source} |
| SAM    | {range}  | {filter} |
| SOM    | {range}  | {assumptions} |

### Verdict: {worth pursuing / marginal / skip}
### Key Assumptions: {list}
```

## Rules
- Quick estimation, not a full market study
- Focus on "is this market worth pursuing?" not exact numbers
- Use ranges, not point estimates — acknowledge uncertainty
- Always state assumptions explicitly — bad assumptions invalidate the whole analysis
