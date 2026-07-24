# Adapter Protocol

## What Are Adapters

Adapters connect project-agnostic agents to project-specific tools. An agent says WHAT to check (e.g., "read production logs"), the adapter says HOW (the concrete CLI command or API call of whatever hosting/monitoring stack the project uses).

## Architecture

```
HIVE (this repo)                          CLIENT PROJECT (.claude/hive/)
━━━━━━━━━━━━━━━━                          ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

agents/core/obs-chief/AGENT.md            .claude/hive/adapters/
  "use adapter:observe.logs"                observe-logs.md
  "use adapter:observe.errors"              observe-errors.md
                                            ...
protocols/adapters.md (this file)
  defines all adapter ports               .claude/hive/config.json
  defines what each must provide            project name, maturity, repo info

skills/setup/SKILL.md
  bootstraps .claude/hive/ in client
```

## Why Adapters Live in the Client Project

1. **Security** — adapters reference env vars, API keys, connection strings. Never in a shared repo.
2. **Project-specific** — one project deploys on Railway, another on Vercel, a third on Kubernetes. Same agent, different adapter.
3. **Gitignored if needed** — adapters with secrets can be gitignored, templates checked in.

## Adapter Port Registry

Each port defines: name, purpose, what the agent expects back, and required fields.

### observe.logs
- **Purpose**: Read application logs from production
- **Used by**: Obs Chief, DevOps
- **Expected output**: Recent log entries with timestamps, levels, messages
- **Required fields**: `tool`, `command` or `api_endpoint`
- **Optional fields**: `env_vars`, `format`

### observe.errors
- **Purpose**: Read runtime errors and exceptions
- **Used by**: Obs Chief, DevOps, Sec Chief
- **Expected output**: Error list with stack traces, frequency, first/last seen
- **Required fields**: `tool`, `dsn` or `api_endpoint`
- **Optional fields**: `project`, `env_vars`

### observe.metrics
- **Purpose**: Read application and database metrics
- **Used by**: Obs Chief, Scale Chief, Data Analyst
- **Expected output**: Key metrics (error rate, latency, connections, table sizes)
- **Required fields**: `tool`, `connection` or `queries`
- **Optional fields**: `env_vars`

### infra.deploy
- **Purpose**: Check deploy status, trigger deploys
- **Used by**: DevOps
- **Expected output**: Deploy status, history, environment info
- **Required fields**: `tool`, `command`
- **Optional fields**: `env_vars`

### infra.db
- **Purpose**: Database management operations
- **Used by**: DevOps, Scale Chief
- **Expected output**: DB health, backup status, connection pool stats
- **Required fields**: `tool`, `project` or `connection`
- **Optional fields**: `env_vars`

### security.deps
- **Purpose**: Dependency vulnerability scanning
- **Used by**: Sec Chief
- **Expected output**: CVE list with severity, package, fix availability
- **Required fields**: `command`
- **Optional fields**: `severity_threshold`

### security.secrets
- **Purpose**: Detect leaked secrets in code
- **Used by**: Sec Chief
- **Expected output**: List of potential secret exposures
- **Required fields**: `command`

### security.auth
- **Purpose**: Auth and RLS policy inspection
- **Used by**: Sec Chief
- **Expected output**: Endpoint auth coverage, RLS policy list
- **Required fields**: `tool`, `command`

### customer.activity
- **Purpose**: Read user activity and engagement data
- **Used by**: Product Chief, CS Lead, Account Mgr, Data Analyst
- **Expected output**: Events per org/user, last active, feature usage
- **Required fields**: `source` (table name or API), `query` or `command`

### customer.feedback
- **Purpose**: Read user feedback scores and comments
- **Used by**: Product Chief, CS Lead
- **Expected output**: Feedback scores, themes, trends
- **Required fields**: `source`, `query` or `command`

### notify.primary
- **Purpose**: Send notifications to the human on their primary chat channel (Slack, Telegram, Discord, …)
- **Used by**: CTO (approvals), Obs Chief (critical alerts), Scrum Master (reminders)
- **Required fields**: `tool`, `command` or `api_endpoint` (credentials via env vars)

### notify.email
- **Purpose**: Send email notifications
- **Used by**: Account Mgr (outreach drafts, with human approval), escalation Level 2+
- **Required fields**: `tool`, `api_key` (env var), `from`, `to`

### notify.urgent
- **Purpose**: Reach the human on a high-urgency channel (SMS, phone, pager) — Level 3 escalations only
- **Used by**: Escalation protocol
- **Required fields**: `tool`, `command` or `api_endpoint`
- **Optional**: may be left unconfigured; escalation then stops at notify.primary + notify.email

### build.*
- **Purpose**: Run build/test/lint commands
- **Used by**: QA Lead, Sr Backend
- **Required fields**: `command`

## Adapter File Format

Each adapter is a markdown file in `.claude/hive/adapters/`:

```markdown
# observe-logs

## Tool
{tool name — e.g. your hosting provider's CLI}

## Command
{command that returns recent logs — e.g. `<hosting-cli> logs --json --last {period}`}

## Environment Variables
- {VAR_NAME} — {what it is}

## Output Format
{what the agent should expect — e.g. JSON lines with timestamp, level, message}

## Notes
{prerequisites: CLI installed, project linked, auth done}
```

Example implementations for the same port across stacks: `railway logs --json` (Railway), `vercel logs` (Vercel), `kubectl logs` (Kubernetes), `heroku logs --tail` (Heroku). The agent never knows which one it is.

## Setup

Use hive's `setup` skill to bootstrap `.claude/hive/` in a new client project.
It will:
1. Ask which adapters are needed
2. Generate adapter files with placeholders
3. Create config.json with project info and maturity stage
4. Verify env vars are set
