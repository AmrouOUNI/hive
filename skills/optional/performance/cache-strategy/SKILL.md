# cache-strategy — Recommend Caching Strategy for Hot Queries

## When to Use
Scale Chief uses this when a query is proven slow and frequently accessed, or when Architect requests cache design input.

## Inputs
- Hot query identified by `perf-audit` (query template, frequency, response time)
- Data freshness requirements for the affected entity
- Current infrastructure (available caching layers)

## Procedure

1. Identify the hot query from perf-audit findings
2. Measure current response time and call frequency
3. Assess data freshness requirements:
   - How stale can the data be before it causes problems?
   - What events change this data?
4. Recommend cache approach:
   - Application-level memoization (for single-request dedup)
   - Redis cache-aside (for cross-request caching)
   - Query result caching (for expensive aggregations)
5. Define TTL based on data freshness requirements
6. Define invalidation trigger:
   - Time-based (TTL expiry)
   - Event-based (invalidate on write)
   - Manual (admin flush)
7. Post recommendation to #scaling:

```markdown
---
agent: scale-chief
type: proposal
severity: info
tags: [cache-strategy]
mentions: [@architect]
requires: review
---

## Cache Strategy Recommendation

### Target Query: {query description}
### Current Performance: {response time} avg, {frequency} calls/hour
### Approach: {memoization | Redis cache-aside | query result cache}
### TTL: {duration}
### Invalidation: {time-based | event-based | manual}
### Expected Improvement: {estimated new response time}
### Trade-offs: {staleness window, memory cost, complexity}
```

## Output Format
Cache strategy recommendation posted to #scaling with @architect tagged (see template above).

## Rules
- At Stage 2, only cache if a specific query is proven slow AND frequently accessed
- No speculative caching — every cache must have measured justification
- At Stage 3+, proactive cache strategy is appropriate
- Always document invalidation strategy — a cache without clear invalidation is a bug factory
