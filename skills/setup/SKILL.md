---
name: hive-setup
description: Bootstrap the hive in a client project — config, adapters, skills-map, context files, GH Discussions, scheduled tasks
---

# setup — Bootstrap Hive in a Client Project

## When to Use
First time connecting the hive to a new project (or re-running to repair/extend an existing setup — the skill is idempotent: it never overwrites existing files without asking).

Run from the client project root. `{HIVE_ROOT}` below means the local clone of the hive repo; ask the user for its path if unknown.

## Procedure

### Step 1: Gather Project Info

Ask the user:
1. Project name?
2. GitHub repo (owner/name)? (default: infer from `git remote get-url origin`)
3. Maturity stage? (1=POC, 2=Early Product, 3=Growth, 4=Scale — see `{HIVE_ROOT}/protocols/project-maturity.md`)
4. Which agent packs to enable?
   - **Core** (always on): cto, architect, sec-chief, obs-chief, devops, qa-lead, scrum-master, scout
   - **Optional** (pick per project): product-chief, innovator, cs-lead, account-mgr, support, devrel, data-analyst, sr-backend, sr-ai, scale-chief
5. Primary notification channel for the human (chat tool of choice, email, none)?

### Step 2: Detect the Stack

Do NOT assume any stack. Detect, then confirm with the user:

- **Package manager / build**: look for lockfiles (`pnpm-lock.yaml`, `package-lock.json`, `yarn.lock`, `Cargo.toml`, `go.mod`, `pyproject.toml`…) and task runners (nx, turbo, make, just). Derive candidate `build.test` / `build.lint` / `build.build` commands from the project's own scripts.
- **Hosting / deploy**: look for provider config files (e.g. `railway.json`, `vercel.json`, `fly.toml`, `Dockerfile` + k8s manifests, `serverless.yml`) and installed CLIs.
- **Database**: connection strings in `.env.example`, ORM configs (Prisma, Drizzle, TypeORM, SQLAlchemy…).
- **Error tracking / observability**: SDK imports or DSN env var names in `.env.example`.
- **Security scanning**: the package manager's own audit command; a secret scanner if one is installed (otherwise note it as a recommendation).

For each adapter port the enabled agents need, propose a concrete implementation and let the user confirm or edit.

### Step 2b: Detect an Existing Spec-Driven Workflow

Check whether the project already uses a spec-driven development framework — the `feature-development` capability should bind to it, and agents should read ITS context files instead of inventing parallel ones.

- Look for known workflow directories at the project root, e.g. `conductor/` (Conductor: `index.md`, `tracks.md`, per-track `spec.md`/`plan.md`), or any directory the user names.
- If found, record it in `config.json` (see Step 3 `workflow` block):
  - `framework`: the detected framework name
  - `context_files`: where the framework keeps tech-stack / code-standards / product docs (e.g. Conductor: `conductor/tech-stack.md`, `conductor/code_styleguides/`, `conductor/product.md`) — agents read these instead of duplicating them
  - `tracker`: the framework's work registry (e.g. `conductor/tracks.md`)
- In Step 5, pre-fill `feature-development` with a **mode mapping** to the framework's skills if they are installed (e.g. Conductor: spec/plan → `conductor-new-track`, implement → `conductor-implement`, review → `conductor-review`, status → `conductor-status`).
- If nothing is found, set `workflow.framework` to `null` — agents use the capability fallback (spec → plan → tasks in the issue tracker).

### Step 3: Create `.claude/hive/config.json`

```json
{
  "project": "{name}",
  "repo": { "owner": "{owner}", "name": "{repo}", "base_branch": "{main}" },
  "maturity": {
    "stage": 2,
    "label": "early-product",
    "users": "~{n}",
    "last_assessed": "{today}",
    "notes": "{free-form context}"
  },
  "agents": {
    "core": true,
    "optional": ["{enabled optional agents}"]
  },
  "workflow": {
    "framework": "{detected framework or null}",
    "context_files": {
      "tech_stack": "{path or null}",
      "code_standards": "{path or null}",
      "product": "{path or null}"
    },
    "tracker": "{path or null}"
  },
  "discussions": {
    "repo_id": "{resolved in Step 6}",
    "category_ids": { "{category}": "{id}" }
  },
  "human": {
    "name": "{name}",
    "github_handle": "{handle}",
    "notify": { "primary": "{configured|none}", "email": "{configured|none}", "urgent": "{configured|none}" }
  }
}
```

### Step 4: Create Adapters

For each port needed by the enabled agents (registry in `{HIVE_ROOT}/protocols/adapters.md`), create `.claude/hive/adapters/{port-name}.md` using the format from that protocol, filled with the stack detected in Step 2. Ports with no available implementation get `## Status: not-configured` — agents degrade gracefully.

Minimum ports for core agents: `observe-logs`, `observe-errors`, `observe-metrics`, `infra-deploy`, `security-deps`, `security-secrets`, `build-test`, `notify-primary`.
Add `customer-activity` / `customer-feedback` only if customer-facing optional agents are enabled.

### Step 5: Generate `skills-map.json`

Scan the environment for installed skills that can provide each capability from `{HIVE_ROOT}/protocols/capabilities.md`:
- project skills: `.claude/skills/`, `.claude/commands/`
- user/plugin skills: `~/.claude/skills/`, installed plugins, built-in slash commands

Write `.claude/hive/skills-map.json`:

```json
{
  "capabilities": {
    "code-review": "{best match or null}",
    "security-review": "{…}",
    "incident-response": "{…}",
    "architecture-decision": "{…}",
    "testing-strategy": "{…}",
    "debug": "{…}",
    "documentation": "{…}",
    "refactor": "{…}",
    "design-review": "{…}",
    "standup-summary": "{…}",
    "deploy-checklist": "{…}",
    "feature-development": "{…}",
    "ticket-authoring": "{…}",
    "ticket-implementation": "{…}"
  }
}
```

Show the mapping to the user for confirmation — they know their environment best. `null` is fine: agents fall back to the guidance in `capabilities.md`.

If Step 2b detected a workflow framework whose skills are installed, use the **object (mode-mapping) form** for `feature-development` — see `protocols/capabilities.md` for the format and a Conductor example.

**Companion skills**: if `ticket-authoring` or `ticket-implementation` resolve to nothing, hive ships reference providers in `{HIVE_ROOT}/skills/companion/` (`write-ticket`, `implement-ticket`). Offer to install them — preferred: symlink into `~/.claude/skills/` (stays in sync with the hive repo); alternative: copy into the project's `.claude/skills/`. On acceptance, install, then map the capabilities to them.

### Step 6: GitHub Discussions

1. Verify `gh auth status`.
2. Fetch repo id + existing categories:
   ```bash
   gh api graphql -f query='{ repository(owner: "{owner}", name: "{repo}") {
     id
     discussionCategories(first: 20) { nodes { id name } }
   } }'
   ```
3. Compare against the categories required by the enabled agents (see `design.md` category table). List missing ones and ask the user to create them (GitHub currently only allows category creation via the web UI: `https://github.com/{owner}/{repo}/discussions/categories`). Re-fetch after they confirm.
4. Store `repo_id` and all `category_ids` in `config.json`.

### Step 7: Create Agent Context Files + Dispatcher State

```bash
mkdir -p .claude/hive/context
echo '{"processed": {}}' > .claude/hive/dispatcher-state.json
```

For each ENABLED agent, copy its `context.md` template from `{HIVE_ROOT}/agents/{core|optional}/{codename}/context.md` to `.claude/hive/context/{codename}.md`, with header `> Last updated: never (awaiting first run)`.

### Step 8: Register Scheduled Tasks

For each enabled agent, read `{HIVE_ROOT}/agents/{core|optional}/{codename}/schedule.json` and the matching `SKILL-{cycle}.md`, then create one scheduled task per cycle with Claude Code's scheduled-task tooling:
- task id: `{codename}-{cycle}`
- cron: from schedule.json (ask the user for their timezone first)
- prompt: the SKILL file content (or a prompt that reads the SKILL file at run time, so hive updates propagate without re-registering)

Also register the dispatcher: `hive-dispatcher`, from `{HIVE_ROOT}/skills/dispatch/SKILL.md`, every 30 min on weekdays.

**Ask before creating any scheduled task** — list them all first with their cron lines.

### Step 9: Verify

1. Env vars referenced by adapters: check each is set; report missing ones.
2. `gh auth status` — correct account, repo reachable.
3. One read-only dry-run per configured adapter (e.g. fetch 5 log lines via `observe-logs`); mark failing adapters `## Status: blocked` with the error.
4. Print the summary:

```
.claude/hive/
  config.json              ✅ {project}, Stage {n}, {k} agents enabled
  skills-map.json          ✅ {m}/{total} capabilities mapped
  dispatcher-state.json    ✅
  adapters/                ✅ {n} active, {m} not-configured, {p} blocked
  context/                 ✅ {k} agent context files

Scheduled tasks: {n} created
Missing: {list of env vars / categories / adapters to finish}
```

## Constraints
- Do NOT create secrets — only reference env vars
- Do NOT commit env values to git; suggest gitignoring `.claude/hive/adapters/` if any adapter embeds sensitive material
- Do NOT overwrite an existing `.claude/hive/` file without asking
- Ask before creating scheduled tasks
- Never enable an optional agent the user didn't pick
