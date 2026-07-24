---
name: research-trend-detect
description: Detect emerging external market trends and internal usage trends, with evidence, relevance, and urgency ratings.
---

# trend-detect — Detect Emerging Trends

## When to Use
Scout scans external sources for market trends. The data-analyst agent (if enabled — see config.json `agents`) scans internal data for usage trends. Can be run independently or together.

## Inputs
- **Scout**: Web sources (industry blogs, competitor updates, tech news, community forums)
- **Data Analyst**: Internal usage data via the `customer.activity` adapter (feature adoption, engagement patterns, growth metrics)
- Previous trend reports for continuity

## Procedure

1. **Scan for signals**:
   - Scout: search web for emerging trends in the product's domain (see config.json) and adjacent spaces
   - Data Analyst: query internal data via the `customer.activity` adapter for shifts in usage patterns, feature adoption curves, anomalies
2. For each signal detected, document:
   - **What's changing** — describe the trend in one sentence
   - **Evidence** — source link, data point, or metric
   - **Direction** — growing / declining / emerging / stabilizing
3. **Assess relevance** — does this trend affect the product, its market, or its users?
   - **Direct**: affects core product functionality or target users
   - **Adjacent**: affects a related space, could become relevant
   - **Peripheral**: interesting but no clear connection
4. **Classify urgency**:
   - **Act now**: trend is active and competitors are moving
   - **Watch**: trend is building, worth monitoring
   - **Note**: interesting signal, log for future reference
5. Post to `#research`:

```markdown
---
agent: {scout | data-analyst}
type: research
severity: info
tags: [trend, {external | internal}]
mentions: []
requires: read
---

## Trend Report — {date}

### Signal: {trend name}
- **What's changing**: {description}
- **Evidence**: {source or data point}
- **Direction**: {growing / declining / emerging / stabilizing}
- **Relevance**: {direct / adjacent / peripheral}
- **Urgency**: {act now / watch / note}
- **Implication**: {what this means for us}

### Signal: {trend name}
...
```

## Output Format
Single post to `#research` in the template above. Multiple signals can be in one report.

## Rules
- Every signal must have evidence — a source link or a data point. No speculation without basis
- Relevance and urgency are judgments — explain the reasoning, don't just label
- "Act now" signals must be flagged to the CTO (and to the innovator / product-chief agents if enabled — see config.json `agents`) within 24h
- Revisit previous "watch" signals monthly — upgrade or archive them
