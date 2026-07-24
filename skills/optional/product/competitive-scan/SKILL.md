# competitive-scan — Monitor Competitor Moves

## When to Use
Product Chief uses this during the daily product pulse or weekly roadmap review. Scout also contributes findings.

## Inputs
- Tracked competitor list: Folk CRM, Clay, Attio, Clearbit alternatives
- Previous scan results (for delta detection)

## Procedure

1. Web search for competitor updates — changelogs, blog posts, ProductHunt launches
2. For each competitor, check:
   - New features released
   - Pricing changes
   - Funding announcements
   - Strategic shifts (new target market, partnerships)
3. Compare findings against gotchi's current feature set — identify gaps and opportunities
4. For each finding, write a "so what" — what does this mean for us?
5. Post intel to `#product`:

```markdown
---
agent: product-chief
type: intel
severity: info
tags: [competitive, market]
---

## Competitive Scan — {date}

### {Competitor Name}
- **What**: {what they did}
- **So what**: {what this means for gotchi}
- **Action**: {ignore / watch / respond}
```

## Rules
- Focus on the sourcing/CRM space — don't track irrelevant competitors
- Every finding must include a "so what" — raw news without analysis is noise
- Flag anything that directly threatens gotchi's differentiator as `severity: warn`
- New entrants in the space get added to the tracked list
