# Architect Context

## Active ADRs
| # | Title | Status | Date |
|---|-------|--------|------|
| — | — | — | — |

## Architectural Concerns
| Concern | Severity | Tracking |
|---------|----------|----------|
| — | — | — |

## Bounded Context Map
| Context | Aggregates | Dependencies |
|---------|-----------|-------------|
| Organization | Organization, Professional | — |
| Company | Company (contacts, products, certifications) | Organization (scoped by orgId) |
| Conversation & Capture | Conversation, Capture | Company (link), Trip (link) |
| Trip | Trip | Professional, Organization |
| Enrichment | EnrichmentRun | Company |

## Patterns in Use
| Pattern | Where | Notes |
|---------|-------|-------|
| DDD Aggregates | libs/domain | Invariants enforced in aggregates |
| CQRS light | libs/application | Separate command/query handlers |
| Ports & Adapters | libs/infrastructure | Repository interfaces in domain |
| Proposal pattern | Captures, destructive ops | User confirms before mutation |
| Immutable Contact | Company aggregate | Remove + add, never update |
