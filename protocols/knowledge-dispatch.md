# Knowledge Dispatch — System Design Report

Maps the 10 system design domains to hive agents. Each agent absorbs the concepts relevant to their role, filtered through the project's maturity stage.

## Dispatch Matrix

### Domain 1: Scalability & Load Balancing

| Concept | Primary owner | Secondary | Why |
|---------|--------------|-----------|-----|
| Horizontal vs vertical scaling decisions | **CTO** | Architect | Strategic decision — CTO decides when to scale up vs out based on maturity stage |
| Load balancing algorithms (round robin, least connections, power-of-two) | **DevOps** | Scale Chief | DevOps configures, Scale Chief validates the choice |
| Auto-scaling strategies (reactive, predictive, scale-to-zero) | **DevOps** | CTO | DevOps implements, CTO approves spend implications |
| CDN configuration and cache rules | **DevOps** | — | Pure infra concern |
| Consistent hashing | **Architect** | Scale Chief | Architectural pattern — Architect decides if/when to introduce |
| Database read replicas | **Architect** | Scale Chief | Architectural pattern with consistency trade-offs |
| Sharding strategy | **Architect** | CTO | Major architectural decision — CTO validates business alignment |
| CAP/PACELC trade-offs | **Architect** | CTO | Architect explains trade-offs, CTO decides which side to optimize |
| N+1 query anti-pattern | **Scale Chief** | Sr Backend, QA Lead | Scale Chief detects, Sr Backend fixes, QA Lead adds tests |
| Capacity estimation | **Scale Chief** | DevOps | Scale Chief models, DevOps provisions |

### Domain 2: Databases & Storage

| Concept | Primary owner | Secondary | Why |
|---------|--------------|-----------|-----|
| PostgreSQL internals (query execution, MVCC, WAL) | **Scale Chief** | Sr Backend | Scale Chief understands for perf, Sr Backend applies in code |
| ACID properties and isolation levels | **Architect** | Sr Backend | Architect sets policy, Sr Backend follows |
| Index design (B-Tree, GIN, BRIN) | **Scale Chief** | Sr Backend | Scale Chief audits, Sr Backend creates |
| EXPLAIN ANALYZE interpretation | **Scale Chief** | Obs Chief | Scale Chief optimizes, Obs Chief monitors |
| NoSQL paradigm selection | **Architect** | CTO | Architectural decision |
| Data modeling (normalization vs denormalization) | **Architect** | Sr Backend | Architect decides, Sr Backend implements |
| Replication topology | **DevOps** | Architect | DevOps configures, Architect validates consistency model |
| Backup and recovery | **DevOps** | — | Pure ops concern |
| Connection pooling (PgBouncer and equivalents) | **Scale Chief** | DevOps | Scale Chief tunes, DevOps deploys |
| Data lifecycle management | **CTO** | DevOps | Business decision on retention, DevOps automates |

### Domain 3: Caching Strategies

| Concept | Primary owner | Secondary | Why |
|---------|--------------|-----------|-----|
| Cache-aside vs write-through vs write-behind | **Architect** | Sr Backend | Architectural pattern choice |
| Redis architecture and data structures | **Sr Backend** | Scale Chief | Sr Backend implements, Scale Chief monitors |
| Cache invalidation strategies | **Architect** | Sr Backend | Hardest problem in CS — Architect designs, Sr Backend implements |
| CDN caching headers | **DevOps** | — | Infra configuration |
| Cache stampede / thundering herd prevention | **Scale Chief** | Sr Backend | Scale Chief detects risk, Sr Backend implements locks/coalescing |
| Eviction policies (LRU, LFU, TTL) | **Scale Chief** | Sr Backend | Scale Chief tunes, Sr Backend configures |
| Multi-tier caching (L1 app / L2 Redis / L3 CDN) | **Architect** | — | Architectural decision |

### Domain 4: Networking & APIs

| Concept | Primary owner | Secondary | Why |
|---------|--------------|-----------|-----|
| REST API design (resource naming, versioning, pagination) | **Architect** | Sr Backend | Architect sets standards, Sr Backend follows |
| GraphQL vs REST vs gRPC selection | **Architect** | CTO | Architectural choice with business implications |
| Rate limiting and throttling | **Scale Chief** | Sec Chief | Scale Chief implements, Sec Chief reviews for abuse prevention |
| API gateway patterns | **DevOps** | Architect | DevOps deploys, Architect designs routing rules |
| WebSocket / SSE for real-time | **Architect** | Sr Backend | Architect decides when to use, Sr Backend implements |
| DNS and TCP/TLS fundamentals | **DevOps** | Sec Chief | DevOps manages, Sec Chief audits TLS config |
| HTTP/2, HTTP/3 / QUIC | **DevOps** | — | Protocol-level optimization |
| API authentication (JWT, OAuth, API keys) | **Sec Chief** | Architect | Sec Chief owns auth security, Architect validates flow |

### Domain 5: Distributed Systems

| Concept | Primary owner | Secondary | Why |
|---------|--------------|-----------|-----|
| Consensus protocols (Raft, Paxos) | **Architect** | — | Theoretical foundation — Architect understands for design decisions |
| Distributed transactions (2PC, Saga) | **Architect** | Sr Backend | Architect chooses pattern, Sr Backend implements |
| Leader election | **Architect** | DevOps | Architect designs, DevOps configures |
| Clock synchronization and ordering | **Architect** | — | Theoretical but impacts event sourcing design |
| Idempotency | **Architect** | Sr Backend | Critical pattern — Architect mandates, Sr Backend implements |
| Eventual consistency patterns | **Architect** | Sr Backend | Architect designs, Sr Backend codes |
| Distributed locking | **Scale Chief** | Sr Backend | Scale Chief audits lock contention, Sr Backend implements |
| Partition tolerance design | **Architect** | CTO | Architect proposes, CTO decides business tolerance |

### Domain 6: Message Queues & Event Streaming

| Concept | Primary owner | Secondary | Why |
|---------|--------------|-----------|-----|
| Pub/Sub vs point-to-point messaging | **Architect** | — | Architectural pattern choice |
| Kafka / event streaming architecture | **Architect** | Sr Backend | Architect designs topology, Sr Backend implements producers/consumers |
| Event sourcing and CQRS | **Architect** | Sr Backend | Deeply architectural — Architect owns the decision |
| Dead letter queues and error handling | **Sr Backend** | Obs Chief | Sr Backend implements, Obs Chief monitors DLQ depth |
| Message ordering and exactly-once semantics | **Architect** | Sr Backend | Architect defines guarantees needed, Sr Backend implements |
| Backpressure handling | **Scale Chief** | Sr Backend | Scale Chief monitors queue depth, Sr Backend implements backpressure |
| Fan-out patterns | **Architect** | — | Architectural pattern (see Twitter timeline example) |

### Domain 7: Microservices & Service Mesh

| Concept | Primary owner | Secondary | Why |
|---------|--------------|-----------|-----|
| Monolith vs microservices decision | **CTO** | Architect | Strategic business + technical decision |
| Service decomposition and bounded contexts | **Architect** | CTO | DDD territory — Architect owns, CTO validates business alignment |
| Service mesh (Istio, Linkerd) | **DevOps** | Architect | DevOps deploys, Architect designs service communication |
| Service discovery | **DevOps** | — | Pure infra |
| API contracts and versioning between services | **Architect** | Sr Backend | Architect sets contract standards |
| Circuit breaker pattern | **Sr Backend** | Scale Chief | Sr Backend implements, Scale Chief monitors |
| Sidecar and ambassador patterns | **DevOps** | Architect | Infra pattern with architectural implications |
| Database-per-service pattern | **Architect** | CTO | Major data ownership decision |

### Domain 8: Reliability & Fault Tolerance

| Concept | Primary owner | Secondary | Why |
|---------|--------------|-----------|-----|
| SLIs, SLOs, SLAs definition | **CTO** | Obs Chief | CTO sets business targets, Obs Chief monitors |
| Error budgets | **CTO** | Obs Chief, Scrum Master | CTO manages budget, Obs Chief tracks burn rate |
| Circuit breakers | **Sr Backend** | Obs Chief | Sr Backend codes, Obs Chief monitors trip frequency |
| Retry policies (exponential backoff, jitter) | **Sr Backend** | Scale Chief | Sr Backend implements, Scale Chief validates no retry storms |
| Graceful degradation | **Architect** | Sr Backend | Architect designs degradation modes, Sr Backend implements |
| Chaos engineering | **Obs Chief** | DevOps | Obs Chief runs chaos experiments, DevOps ensures infra recovery |
| Bulkhead pattern | **Architect** | Sr Backend | Isolate failure domains — architectural decision |
| Disaster recovery and backup | **DevOps** | CTO | DevOps implements, CTO sets RPO/RTO targets |
| Blue/green and canary deployments | **DevOps** | Obs Chief | DevOps orchestrates, Obs Chief validates metrics post-deploy |
| Feature flags | **Sr Backend** | Product Chief | Sr Backend implements, Product Chief decides rollout strategy |

### Domain 9: Observability & Monitoring

| Concept | Primary owner | Secondary | Why |
|---------|--------------|-----------|-----|
| Three pillars: logs, metrics, traces | **Obs Chief** | — | This IS the Obs Chief's domain |
| Structured logging | **Obs Chief** | Sr Backend | Obs Chief sets standards, Sr Backend follows in code |
| Metrics collection (Prometheus, StatsD) | **Obs Chief** | DevOps | Obs Chief designs metrics, DevOps deploys collectors |
| Distributed tracing (Jaeger, OpenTelemetry) | **Obs Chief** | Sr Backend | Obs Chief configures, Sr Backend adds trace context |
| Alerting design (signal vs noise) | **Obs Chief** | — | Core skill — alert fatigue prevention |
| Dashboards and visualization | **Obs Chief** | Data Analyst | Obs Chief builds ops dashboards, Data Analyst builds business dashboards |
| Anomaly detection | **Obs Chief** | Data Analyst | Both detect — different domains (ops vs business) |
| SLO monitoring and burn rate | **Obs Chief** | CTO | Obs Chief monitors, CTO decides on error budget responses |
| Incident management process | **Obs Chief** | Scrum Master | Obs Chief owns incidents, Scrum Master tracks process health |
| Postmortem culture | **Obs Chief** | CTO | Obs Chief writes, CTO enforces blameless culture |

### Domain 10: Security Architecture

| Concept | Primary owner | Secondary | Why |
|---------|--------------|-----------|-----|
| Authentication (OAuth2, OIDC, JWT, SAML) | **Sec Chief** | Architect | Sec Chief owns auth security, Architect validates flow design |
| Authorization (RBAC, ABAC, ReBAC) | **Sec Chief** | Architect | Sec Chief audits, Architect designs the model |
| Zero trust architecture | **Sec Chief** | DevOps | Sec Chief designs, DevOps implements network policies |
| Encryption (at rest, in transit, end-to-end) | **Sec Chief** | DevOps | Sec Chief mandates, DevOps configures |
| Secret management (Vault, KMS) | **Sec Chief** | DevOps | Sec Chief audits, DevOps manages infrastructure |
| OWASP Top 10 | **Sec Chief** | Sr Backend | Sec Chief audits, Sr Backend prevents in code |
| Supply chain security (deps, CI/CD) | **Sec Chief** | DevOps | Sec Chief scans, DevOps hardens pipeline |
| API security (rate limiting, input validation, CORS) | **Sec Chief** | Sr Backend | Sec Chief reviews, Sr Backend implements |
| Data privacy (GDPR, encryption, PII handling) | **Sec Chief** | CTO | Sec Chief enforces, CTO decides business compliance level |
| Penetration testing and red teaming | **Sec Chief** | — | Core sec-chief skill |

## Summary: Who Owns What

| Agent | Primary domains | Key concepts |
|-------|----------------|-------------|
| **CTO** | 1 (strategy), 7 (monolith decision), 8 (SLOs/error budgets) | Scale-up vs scale-out timing, microservice decision, SLA targets, error budget management, data lifecycle, compliance level |
| **Architect** | 1 (patterns), 2 (modeling), 3 (caching), 4 (APIs), 5 (distributed), 6 (messaging), 7 (decomposition), 8 (degradation) | The heaviest knowledge load — Architect owns architectural patterns across 8 of 10 domains |
| **Sec Chief** | 10 (full ownership), 4 (auth) | Full ownership of security domain + auth/API security from networking |
| **Obs Chief** | 9 (full ownership), 8 (chaos/deploy validation) | Full ownership of observability + reliability monitoring |
| **DevOps** | 1 (load balancing, CDN, auto-scaling), 2 (replication, backups), 4 (gateway, DNS), 7 (service mesh), 8 (deployments, DR) | Infrastructure execution across most domains |
| **Scale Chief** | 1 (capacity), 2 (indexes, pooling), 3 (eviction, stampede), 6 (backpressure) | Performance-specific slices of multiple domains |
| **Sr Backend** | 2 (implementation), 3 (Redis), 4 (API code), 6 (producers/consumers), 7 (circuit breakers), 8 (retries, feature flags) | Implementation of patterns decided by Architect |
| **Sr AI** | — | Not directly addressed by this report (AI/LLM architecture is a different domain) |
| **QA Lead** | 8 (test reliability patterns) | Testing for failure modes, chaos experiment validation |
| **Product Chief** | 8 (feature flags rollout) | Product-level degradation decisions |
| **Data Analyst** | 9 (business dashboards, anomaly correlation) | Analytics layer of observability |
| **Scrum Master** | 8 (incident process tracking) | Process health around incidents |
| **Scout / Innovator / CS Lead / Account Mgr / Support / DevRel** | — | Not directly relevant — these are product/business roles |

## Key Insight: The Architect Is the Knowledge Hub

The Architect touches 8 of 10 domains. This confirms why the Architect agent needs deep capability access (`capability:design-review`, `capability:architecture-decision`, `capability:feature-development` in review mode) — every design decision passes through architectural judgment.

But the **CTO filters through maturity stage**. The Architect might say "we need sharding." The CTO says "we have a handful of tenants — we need sharding in 2 years, not now."
