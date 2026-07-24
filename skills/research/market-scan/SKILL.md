---
name: research-market-scan
description: Scan the market for relevant news and signals on the every-12h cycle.
---

# market-scan — Scan Market for Relevant News and Signals

## When to Use
Scout uses this on the every-12h scan cycle.

## Inputs
- Tracked sources: ProductHunt, HackerNews, industry blogs
- Domain focus: sourcing, CRM, AI-assistant, professional networking

## Procedure

1. Search ProductHunt for new launches in CRM/sourcing/AI-assistant categories
2. Search HackerNews for relevant discussions and launches
3. Scan industry blogs and newsletters for sourcing/CRM news
4. For each finding:
   - Summarize in 1-2 sentences
   - Include source link
   - Rate relevance: high / medium / low
5. Filter — only post high and medium relevance findings
6. Post to `#research`:

```markdown
---
agent: scout
type: intel
severity: info
tags: [market-scan, research]
---

## Market Scan — {date} {AM/PM}

### Findings
1. **[{title}]({link})** — {relevance: high/medium}
   {1-2 sentence summary}

2. ...
```

## Rules
- Quality over quantity — 3 high-relevance findings beat 20 low-relevance ones
- Always include the source link
- Skip anything outside the sourcing/CRM/AI-assistant domain
- If nothing relevant found, don't post — silence is fine
