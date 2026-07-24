# The Hive

An autonomous AI operations layer for software projects, built entirely on Claude Code primitives — scheduled tasks, skills, subagents — with GitHub Discussions as the communication bus. No external orchestration framework, no server to host.

8 core agents (plus 10 optional ones), a lean skill catalog, and human-in-the-loop for the decisions that matter.

**This is not a SaaS product.** It's personal infrastructure — a force multiplier for solo founders and small teams. The framework is fully project-agnostic: everything specific to *your* product lives in your project, not here.

## How It Works

```
┌─────────────────────────────────────────────────────────────────┐
│                         HIVE REPO                                │
│                   (this repo — the blueprint)                     │
│                                                                  │
│  Never runs. Never has secrets. Never names your stack.          │
│                                                                  │
│  agents/core/      8 default agents (AGENT.md + SKILLs + cron)   │
│  agents/optional/  10 opt-in agents (product, customer, AI, …)   │
│  skills/           hive-specific skills (strategy, ops, QA, …)   │
│  protocols/        communication, escalation, adapters,          │
│                    capabilities, maturity                        │
│  skills/setup/     bootstrap skill for new client projects       │
│                                                                  │
└──────────────────────────────┬──────────────────────────────────┘
                               │
                               │  setup skill reads hive,
                               │  creates .claude/hive/ in client
                               │
┌──────────────────────────────▼──────────────────────────────────┐
│                      CLIENT PROJECT                              │
│                    (your product repo)                            │
│                                                                  │
│  .claude/hive/                                                   │
│    config.json        project info, maturity stage, enabled      │
│                       agents, GH Discussion IDs                  │
│    skills-map.json    capability → installed-skill mapping        │
│    adapters/          HOW agents reach this project's tools       │
│      observe-*        → your hosting logs / metrics / errors     │
│      infra-*          → your deploy tool, your DB                │
│      security-*       → your package audit, secret scanner       │
│      build-*          → your test/lint/build commands             │
│      notify-*         → your chat channel, email                 │
│    context/           per-agent rolling memory                    │
│                                                                  │
│  codebase             the actual product code                    │
│                                                                  │
└──────────────────────────────┬──────────────────────────────────┘
                               │
                               │  scheduled tasks load SKILL.md
                               │  prompts + adapters, run against
                               │  the codebase
                               │
┌──────────────────────────────▼──────────────────────────────────┐
│                        GITHUB                                    │
│                                                                  │
│  GH Discussions (agent reports)    GH Issues (task tracker)      │
│  #daily-standup #security          Prioritized backlog           │
│  #incidents #architecture #ops     P0/P1/P2 · sizes             │
│  #research #features #product      Project board                 │
│  #customer #scaling #decisions                                   │
│  #roadmap                                                        │
└─────────────────────────────────────────────────────────────────┘
```

## Three Layers of Abstraction

Agents are pure logic; everything environment-specific resolves at run time in the client project.

```
Layer 1  AGENTS + HIVE SKILLS + PROTOCOLS      (this repo — who, what, when)
Layer 2  CAPABILITIES                          capability:code-review, capability:incident-response
         → resolved via client skills-map.json to whatever review/incident
           skill is installed in that environment (never duplicated here)
Layer 3  ADAPTERS                              observe.logs, infra.deploy, notify.primary
         → resolved via client .claude/hive/adapters/ to concrete tools
```

- **Adapters** (`protocols/adapters.md`) answer: *how do I reach this project's infrastructure?*
- **Capabilities** (`protocols/capabilities.md`) answer: *how do I perform a generic practice (review, ADR, incident) with the skills installed in this environment?* Hive deliberately ships **no** code-review / security-review / ADR / debugging skills of its own — modern Claude Code environments already have better ones.

## Agents

### Core (enabled by default)

| Role | Codename | Schedule | Writes to |
|------|----------|----------|-----------|
| CTO | `cto` | daily + weekly | #daily-standup, #roadmap, #decisions |
| Architect | `architect` | weekly | #architecture, #decisions |
| Sec Chief | `sec-chief` | daily + monthly | #security, #incidents |
| Obs Chief | `obs-chief` | daily | #daily-standup, #incidents, #ops |
| DevOps | `devops` | weekly | #ops, #incidents |
| QA Lead | `qa-lead` | daily | #daily-standup |
| Scrum Master | `scrum-master` | daily (x2) + weekly | #daily-standup, #decisions |
| Scout | `scout` | daily | #research |

### Optional (enable per project in config.json)

| Role | Codename | Best for |
|------|----------|----------|
| Product Chief | `product-chief` | products with real users to learn from |
| Innovator | `innovator` | feature ideation cadence |
| CS Lead | `cs-lead` | B2B/SaaS customer health |
| Account Mgr | `account-mgr` | per-account care |
| Support | `support` | products with a support inbox |
| DevRel | `devrel` | docs/changelog upkeep |
| Data Analyst | `data-analyst` | cross-agent pattern mining |
| Sr Backend | `sr-backend` | dispatched implementation work |
| Sr AI | `sr-ai` | products with LLM pipelines |
| Scale Chief | `scale-chief` | performance/capacity focus |

Each agent directory contains: `AGENT.md` (persona, authority matrix, knowledge domains), `SKILL-*.md` (one executable prompt per scheduled cycle), `schedule.json` (cron), `context.md` (rolling state template).

## Design Principles

1. **Agents don't code** — they read, analyze, and report. The human decides. (The optional `sr-backend` is the one exception, and only on explicit dispatch.)
2. **Hive is project-agnostic** — zero client-specific content here. Adapters and config live in the client.
3. **Don't duplicate the ecosystem** — generic practices (code review, incident response, ADRs…) are capabilities mapped to the skills already installed in your environment.
4. **Reports are discussions, not files** — GH Discussions is the comms bus.
5. **Maturity-aware** — agents read the project's maturity stage (`protocols/project-maturity.md`) and scale their ambitions accordingly. A POC doesn't need chaos engineering.
6. **Human-in-the-loop** — authority matrices define what's autonomous vs what needs approval (`protocols/escalation.md`).

## Getting Started

1. Clone this repo anywhere (its path is referred to as `{HIVE_ROOT}`).

2. **Bootstrap a client project** — from your product repo, run the setup skill:
   ```bash
   cd /path/to/your-project
   claude "$(cat {HIVE_ROOT}/skills/setup/SKILL.md)"
   ```
   It detects your stack, asks which agent packs to enable, creates `.claude/hive/` (config, adapters, skills-map, context files), sets up the GH Discussion categories, and registers the scheduled tasks.

3. **Let it run.** Agents fire on their cron schedules and post to your repo's Discussions. Read them over coffee, pick what to act on.

## Repo Structure

```
hive/
├── README.md
├── design.md              architecture blueprint
├── agents/
│   ├── core/{8 agents}/
│   └── optional/{10 agents}/
├── skills/
│   ├── setup/             bootstrap a client project
│   ├── dispatch/          reactive GH Discussions dispatcher (cron)
│   ├── strategy/          prioritize, dispatch, decision, roadmap, cost-review
│   ├── ceremonies/        standup, sprint-plan, sprint-review, retro, blockers, velocity
│   ├── observability/     health-check, anomaly-detect, metrics-digest
│   ├── infra/             deploy, rollback, backup-verify, audits, ci-monitor, smoke-test
│   ├── qa/                coverage-audit, acceptance-check, regression-scan
│   ├── research/          market-scan, competitor-track, trend-detect, …
│   ├── architecture/      dependency-map, bounded-context-audit, frontend-boundary-audit
│   └── optional/          product, innovation, customer, account, support, data, ai, performance
└── protocols/
    ├── communication.md   message format, threading, rate limits
    ├── escalation.md      when and how to involve the human
    ├── adapters.md        port registry (infrastructure access)
    ├── capabilities.md    capability registry (generic practices → installed skills)
    ├── knowledge-dispatch.md  who owns which system-design knowledge
    └── project-maturity.md    stage-based decision rules
```
