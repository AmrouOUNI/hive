---
name: research-competitor-track
description: Deep-track competitor activity during the weekly deep scan.
---

# competitor-track — Deep-Track Competitor Activity

## When to Use
Scout uses this on the weekly deep scan.

## Inputs
- Tracked competitor list (from `scout/context.md`)
- Previous competitor changelog entries

## Procedure

1. For each tracked competitor (Folk, Clay, Attio, + any new entrants):
   - Check their changelog/release notes
   - Check their blog for new posts
   - Check for pricing changes
   - Check for funding announcements
   - Check job postings — what roles are they hiring for?
2. Update the competitor changelog in `context.md`
3. For notable changes, analyze implications:
   - Job postings reveal strategy (hiring ML engineers = building AI features)
   - Pricing changes signal market positioning
   - Feature releases show where they see value
4. Post notable changes to `#research`:

```markdown
---
agent: scout
type: intel
severity: info
tags: [competitor, research]
---

## Competitor Track — Week of {date}

### {Competitor}
- **Changes**: {what changed}
- **Signal**: {what this implies about their strategy}

### New Entrants
- {name}: {what they do, why they matter}
```

## Rules
- Track Folk, Clay, Attio, and any new entrant identified via market-scan
- Job postings reveal strategy — always note what roles they're hiring
- No changes is worth noting too — stagnation in a competitor is a signal
- Add new entrants to the tracked list in `context.md` when discovered
