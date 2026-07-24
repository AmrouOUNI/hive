# Project Maturity Model

Every hive decision is filtered through the project's maturity stage. A CTO managing a POC accepts trade-offs that would be unacceptable at scale.

## Maturity Stages

### Stage 1: POC / Prototype (0-100 users)

**Goal:** Validate the idea. Ship fast. Learn.

| Domain | Acceptable trade-offs | NOT acceptable |
|--------|----------------------|----------------|
| Scalability | Single instance, vertical only. No load balancer. | — |
| Database | Single DB, no replicas, no sharding. Denormalize freely. | Losing data (backups still required) |
| Caching | No caching layer. DB handles everything. | — |
| APIs | REST only, no versioning, breaking changes OK. | No auth at all |
| Distributed | Monolith. No microservices. No event streaming. | — |
| Messaging | Direct function calls. No queues. | — |
| Reliability | No SLOs. Manual recovery. Minutes of downtime OK. | No backups |
| Observability | Console logs + a basic error tracker. No dashboards. | No error tracking at all |
| Security | Basic auth. Tenant-level data isolation on key tables. | Exposed secrets, no auth, SQL injection |

**CTO rule:** Approve anything that ships faster. Block only security basics and data safety.

### Stage 2: Early Product (100-1,000 users)

**Goal:** Product-market fit. Stability matters. Users are real.

| Domain | Acceptable trade-offs | NOT acceptable |
|--------|----------------------|----------------|
| Scalability | Still single instance but right-sized. Monitor resource usage. | Ignoring saturation signals |
| Database | Read replica for analytics. Proper indexes. Connection pooling. | Missing indexes on hot queries |
| Caching | Application-level caching for expensive queries. | Over-engineering a Redis layer |
| APIs | Versioned API. Rate limiting on public endpoints. | Breaking changes without notice |
| Distributed | Still monolith. Extract first bounded context only if painful. | Premature microservices |
| Messaging | Background jobs for async work (email, enrichment). Simple queue. | Kafka for 1,000 users |
| Reliability | Basic SLOs (99.5% uptime). Automated backups. Health checks. | No monitoring, no backup verification |
| Observability | Structured logging. Error tracking. Basic metrics dashboard. | Alert fatigue (too many alerts) |
| Security | Full auth flow. Tenant isolation on all data access. Dependency audits. Secret scanning. | Unaudited dependencies |

**CTO rule:** Balance speed with stability. Start measuring. Fix what breaks users.

### Stage 3: Growth (1,000-10,000 users)

**Goal:** Scale without re-architecture. Performance matters.

| Domain | Acceptable trade-offs | NOT acceptable |
|--------|----------------------|----------------|
| Scalability | Horizontal scaling for stateless services. CDN for static. Auto-scaling. | Manual scaling during spikes |
| Database | Sharding plan designed (not necessarily implemented). Query optimization. BRIN indexes. | N+1 queries in hot paths |
| Caching | Redis for sessions, hot data. Cache invalidation strategy documented. | No cache invalidation strategy |
| APIs | API gateway. GraphQL for complex queries if needed. | Chatty APIs (N+1 at API level) |
| Distributed | Service extraction where bounded contexts demand it. Event-driven for cross-domain. | Big bang microservice migration |
| Messaging | Proper message queue (SQS/RabbitMQ). Dead letter queues. | Losing messages silently |
| Reliability | 99.9% SLO. Error budgets. Canary deployments. Feature flags. | Deploying to all users simultaneously |
| Observability | Full three pillars (logs, metrics, traces). Anomaly detection. Runbooks. | Flying blind on any dimension |
| Security | OWASP Top 10 compliance. Pentest-light. Zero trust principles started. | Known CVEs unpatched > 7 days |

**CTO rule:** Invest in foundations. Technical debt from Stage 1-2 must be paid now.

### Stage 4: Scale (10,000+ users)

**Goal:** Reliability IS the product. Every decision is measured.

| Domain | Acceptable trade-offs | NOT acceptable |
|--------|----------------------|----------------|
| Scalability | Multi-region considered. Consistent hashing for data distribution. | Single region for global users |
| Database | Sharding implemented. Read replicas per region. Automated failover. | Manual DB failover |
| Caching | Multi-tier caching (app/Redis/CDN). Cache stampede prevention. | Cache as single point of failure |
| APIs | gRPC for internal services. API contracts with backward compatibility. | Breaking internal contracts |
| Distributed | Full microservices where warranted. Saga patterns. Idempotency everywhere. | Distributed transactions (2PC) |
| Messaging | Event streaming (Kafka/Pulsar). Event sourcing for audit domains. | Point-to-point for cross-service |
| Reliability | 99.99% SLO. Chaos engineering. Disaster recovery tested quarterly. | Untested DR plans |
| Observability | SLO-based alerting. Distributed tracing mandatory. Automated anomaly detection. | Alert noise > 10% false positives |
| Security | Full zero trust. Regular pentests. SOC 2 compliance. Supply chain security. | Any unpatched critical CVE |

**CTO rule:** Every decision has a business case. No over-engineering, no under-engineering.

## How Agents Use Maturity

### In `.claude/hive/config.json` (client project)

```json
{
  "project": "{name}",
  "maturity": {
    "stage": 2,
    "label": "early-product",
    "users": "~{n}",
    "last_assessed": "{date}",
    "notes": "{free-form context: user counts, channels, PMF status}"
  }
}
```

### Agent Decision Filter

Every agent reads the maturity stage before making recommendations:

**CTO:** "Scale Chief recommends sharding. We're Stage 2 with a handful of tenants. Deferred to Stage 3 assessment."

**Architect:** "Event sourcing for follow-ups is the right long-term pattern. At Stage 2, a simple state machine is sufficient. ADR: revisit at Stage 3."

**Sec Chief:** "Found 2 medium CVEs. At Stage 4 this blocks deploy. At Stage 2, patch within 7 days."

**Scale Chief:** "N+1 on company list query. Fix regardless of stage — this is never acceptable."

**DevOps:** "Recommending auto-scaling. At Stage 2, manual scaling with monitoring is sufficient. Set alert at 80% CPU."

### Maturity Transition Triggers

| From → To | Triggered by |
|-----------|-------------|
| Stage 1 → 2 | First paying customer OR first user complaint about reliability |
| Stage 2 → 3 | > 500 active users OR first SLO violation that loses a customer |
| Stage 3 → 4 | > 5,000 active users OR enterprise customer with SLA requirement |

CTO reassesses maturity quarterly during roadmap review. Maturity NEVER goes backward.
