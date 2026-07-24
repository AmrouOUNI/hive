# The Hive — Design

## Problem

Solo founders and very small teams running complex software projects hit three ceilings:

1. **Attention ceiling** — can't watch prod health, scan competitors, audit security, AND build features simultaneously. Things fall through cracks.
2. **Continuity ceiling** — AI sessions are stateless between conversations. Operational rhythm dies unless manually re-loaded.
3. **Speed ceiling** — sequential work. While building feature X, nobody researches feature Y, monitors prod, analyzes patterns, or checks deps for CVEs.

The Hive solves this with a persistent, multi-agent "virtual company" that runs continuously, maintains institutional knowledge, parallelizes observation across specialized roles, and keeps a human-in-the-loop for decisions that matter.

## Approach

### Chosen: Claude Code Native + GitHub Discussions

Build entirely on **Claude Code primitives** — scheduled tasks, skills, subagents, worktrees — with **GitHub Discussions** as the communication bus. No external orchestration framework.

### Rejected Alternatives

| Alternative | Why rejected |
|---|---|
| CrewAI / LangGraph | External framework dependency. Can't use the environment's installed skills natively. Agents are ephemeral (run once, die) — no persistent rhythm |
| n8n / Temporal | Good for automation, bad for reasoning. Agents need to think, not just execute steps. Another infra to host |
| Custom Python multi-agent | Reinventing what Claude Code already does. Scheduled tasks = cron. Skills = agent definitions. Subagents = parallel execution |
| Single mega-agent | One agent with 50 responsibilities = context overload, hallucination, no parallelism |

### Two Repos, One Brain

- **hive** (this repo) — project-agnostic infrastructure: agent definitions, skills, protocols. The blueprint. It never runs and never contains secrets or client specifics.
- **client projects** — your product repos. GH Discussions live there because they're about the product. All configuration (`.claude/hive/`) lives there too.

One hive can serve any number of client projects — same agents, same protocols, different adapters underneath.

## Three-Layer Architecture

```
Layer 1: AGENTS + HIVE SKILLS + PROTOCOLS
         (pure logic — who does what, when, how they talk)

Layer 2: CAPABILITIES                       protocols/capabilities.md
         capability:code-review | capability:incident-response | …
         (generic engineering practices — resolved per client via
          skills-map.json to whatever skill that environment has installed)

Layer 3: ADAPTERS                           protocols/adapters.md
         observe.logs | infra.deploy | security.deps | notify.primary | …
         (infrastructure access — resolved per client via
          .claude/hive/adapters/ to concrete tools)
```

Agents never call project tools or name concrete skills. They invoke abstract capabilities and adapter ports; the client project resolves both.

**Why hive ships so few skills**: the Claude Code ecosystem already provides high-quality skills for code review, security review, ADRs, incident response, debugging, testing strategy, documentation… Duplicating them here would mean maintaining worse copies. Hive only keeps what is hive-specific: orchestration (strategy, ceremonies, dispatch), adapter-driven observation (observability, infra, qa) and the optional business packs.

## Agent Registry

### Core — 8 agents, enabled by default

| Role | Codename | Mission | Cadence |
|---|------|----------|---------|
| CTO | `cto` | Strategic decisions, conflict resolution, roadmap, dispatch | daily + weekly |
| Architect | `architect` | Design integrity, bounded contexts, ADR governance | weekly |
| Sec Chief | `sec-chief` | Deps, auth, data exposure, secret scanning | daily + monthly |
| Obs Chief | `obs-chief` | Prod health — errors, usage, anomalies, incident triage | daily |
| DevOps | `devops` | Deploys, backups, CI/CD, uptime, infra health | weekly |
| QA Lead | `qa-lead` | Quality gate — coverage, acceptance, regression | daily |
| Scrum Master | `scrum-master` | Ceremonies, blockers, process | daily (x2) + weekly |
| Scout | `scout` | Competitive intel, market signals | daily |

### Optional — 10 agents, enabled per project

| Role | Codename | Enable when |
|---|------|-------------|
| Product Chief | `product-chief` | you have real users generating signal |
| Innovator | `innovator` | you want a feature-ideation cadence |
| CS Lead | `cs-lead` | B2B/SaaS with customer health to track |
| Account Mgr | `account-mgr` | per-account onboarding/retention matters |
| Support | `support` | there is a support inbox to triage |
| DevRel | `devrel` | docs/changelog freshness matters |
| Data Analyst | `data-analyst` | enough agent output to mine for patterns |
| Sr Backend | `sr-backend` | you want dispatched implementation work (TDD, worktrees) |
| Sr AI | `sr-ai` | the product has LLM pipelines (costs, prompts, evals) |
| Scale Chief | `scale-chief` | performance/capacity deserves a dedicated eye |

### Agent Definition Format

```
agents/{core|optional}/{codename}/
├── AGENT.md          # Persona, mission, responsibilities, authority matrix,
│                     #   knowledge domains, maturity-aware decision rules
├── SKILL-{cycle}.md  # Executable prompt per scheduled cycle (frontmatter: name,
│                     #   description, cron schedule)
├── schedule.json     # Cron expressions
└── context.md        # Rolling state template — the agent's memory between runs
                      #   (instantiated as .claude/hive/context/{codename}.md in the client)
```

## Communication Bus — GitHub Discussions

### Categories (on the client repo)

| Category | Who creates | Purpose |
|----------|-------------|---------|
| `#decisions` | CTO, Architect | RFCs, dispatch orders, approvals |
| `#daily-standup` | Scrum Master | standup, EOD, agent progress |
| `#incidents` | Obs Chief, Sec Chief, Support | production/security incidents |
| `#features` | Product Chief, Innovator | feature ideas and debate |
| `#architecture` | Architect | design reviews, ADR discussions |
| `#security` | Sec Chief | audits, CVE reports |
| `#scaling` | Scale Chief | performance, capacity |
| `#research` | Scout, Sr AI, Data Analyst | market/tech intelligence |
| `#customer` | CS Lead, Account Mgr, Support | customer health |
| `#ops` | DevOps, Obs Chief | infra status |
| `#product` | Product Chief | product pulse |
| `#roadmap` | CTO | sprint goals, roadmap |

Categories used only by optional agents are created only if those agents are enabled.

Message format, threading rules, rate limits, and decision authority levels: see `protocols/communication.md`. Escalation to the human (channels, timelines, what needs approval): see `protocols/escalation.md`.

## Human Loop

Four escalation levels (INFO → NOTIFY → APPROVAL → URGENT) mapped to notification ports (`notify.primary`, `notify.email`, `notify.urgent`). Approvals block the requesting agent's task, never the whole hive; agents always pick up other queued work while waiting. Details in `protocols/escalation.md`.

What always requires human approval, regardless of maturity stage: production deploys, customer-facing communications, breaking architectural changes, new dependencies, spend above the configured threshold, destructive operations.

## Maturity-Aware Decisions

Agents read `config.json.maturity.stage` (1 POC → 4 Scale) before recommending anything. Full stage rules per domain in `protocols/project-maturity.md`. The CTO enforces the filter: proposals from a higher stage get "deferred, with a reassessment trigger" rather than "no".

## Execution Model

| Mode | Mechanism | Purpose |
|------|-----------|---------|
| Scheduled tasks | Claude Code scheduled tasks | Agent cycles fire on cron |
| Reactive dispatcher | `skills/dispatch` on a frequent schedule | Reads new GH Discussion activity, has agents react and turn insights into tracked issues |
| On-demand | Human invokes an agent SKILL directly | Ad-hoc runs, catch-up after downtime |

The dispatcher is state-tracked (`.claude/hive/dispatcher-state.json`) and loop-safe: it never reacts to its own receipts, never touches `#daily-standup`, and comments at most 3 agent perspectives per discussion.

## Client Bootstrap

`skills/setup/SKILL.md` bootstraps a client project:

1. Gather project info (name, repo, maturity stage) and choose agent packs
2. Detect the stack and propose adapters for each needed port
3. Resolve GH repo + Discussion category IDs dynamically (`gh api graphql`), creating missing categories
4. Scan the environment's installed skills to pre-fill `skills-map.json` capabilities
5. Create `.claude/hive/` (config, adapters, context files, dispatcher state)
6. Register scheduled tasks for enabled agents only
7. Verify: env vars set, `gh auth status`, one dry-run per configured adapter

## Out of Scope

- **Frontend/dashboard UI** — agents post to GH Discussions and notify via messaging. No custom web UI.
- **Billing/payments automation** — human-only.
- **Multi-tenant (serving other people's projects)** — this is personal infra, not a SaaS.
- **Agent-to-agent real-time chat** — async via GH Discussions is sufficient.
- **Self-modifying agents** — agents don't rewrite their own AGENT.md. The human adjusts personas.
