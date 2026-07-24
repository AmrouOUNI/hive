---
name: hive-dispatch
description: Reactive dispatcher — polls GH Discussions, has enabled agents react and turn insights into tracked issues
---

# dispatch — Reactive Event Dispatcher

## When to Use
Runs every 30 minutes. Polls GH Discussions for new or updated posts. Two phases: agents react with comments, then take actions (create/update issues, propose priorities).

## Why
Agents shouldn't just broadcast reports — they should read, react, and drive work forward. The dispatcher turns insights into tracked work items.

## State Tracking

```
.claude/hive/dispatcher-state.json
{
  "processed": {
    "D_abc123": {
      "processed_at": "2026-03-31T10:30:00Z",
      "updated_at": "2026-03-31T10:00:00Z",
      "agents_dispatched": ["architect", "product-chief"],
      "actions_taken": ["commented-on-#27", "created-#40", "proposed-priority-#decisions"]
    }
  }
}
```

A discussion needs processing when:
- Its ID is NOT in the state file (new post)
- Its `updatedAt` in GitHub > `updated_at` in state file (edited or new human comment)

## Anti-Loop Rules
- NEVER react to posts that contain `🐝` receipt comments AND no new human content since
- NEVER react to #daily-standup posts
- NEVER create new discussions — only comment on existing ones (except #decisions proposals)
- If no unprocessed discussions found, exit silently (no output)

## Procedure

### Step 1: Load State + Config

Read `.claude/hive/dispatcher-state.json`. If it doesn't exist, create it with `{"processed": {}}`.

Read `.claude/hive/config.json` for `repo`, `discussions.repo_id`, `discussions.category_ids`, and `agents` (which agents are enabled). **Only enabled agents may be dispatched** — filter every routing/authority table below accordingly.

### Step 2: Fetch Recent Discussions

```bash
gh api graphql -f query='{ repository(owner: "{owner}", name: "{repo}") { discussions(first: 20, orderBy: {field: UPDATED_AT, direction: DESC}) { nodes { id number title body category { name } createdAt updatedAt author { login } comments(last: 5) { nodes { body createdAt author { login } } } } } } }'
```

### Step 3: Filter to Unprocessed

For each discussion:
1. If category is `daily-standup` → SKIP always
2. If `id` NOT in state → needs processing (new)
3. If `id` in state but `updatedAt` > state `updated_at` → check if update is from a human. If yes → re-process
4. If `id` in state and same `updatedAt` → skip

If nothing to process → exit silently.

### Step 4: Route to Agents

For each unprocessed discussion, check its category (drop any agent not enabled in config.json; if a category ends up with no enabled agents, skip it):

```
#architecture    → Architect, Scale Chief
#security        → Sec Chief
#scaling         → Scale Chief, DevOps
#product         → Product Chief, Innovator
#features        → Product Chief, Architect, Innovator
#incidents       → Obs Chief, DevOps, CTO
#customer        → CS Lead, Account Mgr, Product Chief
#ops             → DevOps, Obs Chief
#research        → Data Analyst, Sr AI, Scout
#decisions       → Architect, Sec Chief, Scale Chief
#roadmap         → Product Chief, Scrum Master
#daily-standup   → NO REACTION
```

---

## PHASE 1: REACT

### Step 5: Comment as Each Agent

For each triggered agent, adopt their persona (see their `AGENT.md` under `{HIVE_ROOT}/agents/`) and produce a comment.

Agent perspectives (enabled agents only):
- **CTO**: strategic trade-offs, maturity-stage fit, priority impact
- **Architect**: DDD patterns, layer boundaries, bounded context concerns
- **Sec Chief**: attack vectors, OWASP, auth implications
- **Obs Chief**: monitoring needs, observability gaps
- **Scale Chief**: performance impact, capacity concerns
- **Product Chief**: user value, RICE scoring, competitive context, feature signals
- **DevOps**: infra impact, deploy risk, cost
- **Innovator**: creative alternatives, adjacent opportunities
- **CS Lead**: customer impact, churn risk, engagement signal
- **Account Mgr**: per-org engagement, outreach opportunity
- **Scrum Master**: sprint impact, process implications
- **Data Analyst**: data patterns, cross-agent correlation
- **Sr AI**: LLM cost impact, prompt implications

**Comment rules:**
- SHORT: 3-5 sentences max
- Start with `**{Role}:**`
- Only comment if you add NEW information or flag a concern
- Skip if the post doesn't concern this agent
- Max 3 agent comments per discussion

Post each comment:
```bash
gh api graphql -f query='mutation { addDiscussionComment(input: { discussionId: "{id}", body: "{comment}" }) { comment { url } } }'
```

---

## PHASE 2: ACT

After commenting, each agent with action authority checks if work items should be created or updated.

### Step 6: Extract Signals

From the discussion content, identify:
- **Pain points** — user frustrations, workarounds, complaints
- **Feature requests** — explicit ("I wish I could...") or implicit (workflow gaps)
- **Bug reports** — something broken or unexpected
- **Competitive mentions** — "I currently use X for that"
- **Priority signals** — intensity of need, frequency of mention

### Step 7: Cross-Reference with Backlog

For each signal, check existing GH Issues:
```bash
gh issue list --repo {owner}/{repo} --limit 50 --state open --json number,title,labels,body
```

Determine:
- Does an issue already exist for this signal? → **update it**
- Is this a new need with no existing issue? → **create one**
- Does this signal change priority of existing work? → **propose change**
- Does this have security implications? → **flag immediately**

### Step 8: Take Actions

**Authority matrix — who can do what:**

| Agent | Comment | Create issue | Update issue | Propose priority | Auto-escalate |
|-------|---------|-------------|-------------|-----------------|---------------|
| CTO | ✅ | ✅ | ✅ | ✅ (decides) | — |
| Product Chief | ✅ | ✅ (idea/feature) | ✅ (add signal) | ✅ (proposes) | — |
| Architect | ✅ | ✅ (tech-debt) | ✅ (add concern) | ❌ | ✅ (arch violation) |
| Sec Chief | ✅ | ✅ (security bug) | ✅ (add finding) | ✅ (P0 security) | ✅ (critical vuln) |
| CS Lead | ✅ | ❌ | ✅ (add signal) | ❌ | ✅ (churn risk) |
| Account Mgr | ✅ | ❌ | ❌ | ❌ | ❌ |
| All others | ✅ | ❌ | ✅ (add signal) | ❌ | ✅ (in their domain) |

**Action types:**

#### A. Add signal to existing issue
When a signal matches an existing issue, comment on that issue:
```bash
gh issue comment {number} --repo {owner}/{repo} --body "📡 **Signal from {source}:** {evidence}

Source: {discussion_url}
Strength: {n} users now mentioning this"
```

#### B. Create new issue
When a signal has no matching issue and is actionable:
```bash
gh issue create --repo {owner}/{repo} --title "{title}" --label "{label}" --body "{body}"
```
Then add to project board:
```bash
gh project item-add {project_number} --owner {owner} --url {issue_url}
```

**Rules for new issues:**
- Label: `idea` (unvalidated), `feature` (validated need), `bug`, `tech-debt`
- Default priority: P2 (unless security → P0)
- Default size: needs estimation
- Body must include: signal source, evidence, proposed scope
- If `capability:ticket-authoring` is mapped in `.claude/hive/skills-map.json`, follow its OUTPUT CONTRACT for the body — numbered acceptance criteria (EARS / Given-When-Then), Definition of Done, and open questions as explicit `[NEEDS CLARIFICATION: …]` markers. You run unattended: apply the contract's structure, skip the provider's interactive phases; anything you'd have asked becomes a marker for the human to resolve before promoting the issue.

#### C. Propose priority change
When 3+ users mention the same need, or a signal is urgent:

Post to #decisions:
```bash
gh api graphql -f query='mutation { createDiscussion(input: { repositoryId: "{repo_id}", categoryId: "{decisions_cat_id}", title: "Priority Proposal: #{number} {current} → {proposed}", body: "{rationale}" }) { discussion { url } } }'
```

Format:
```markdown
## Priority Proposal: #{number} {title}

**Current:** {P1/P2}
**Proposed:** {P0/P1}

### Evidence
- {user1} mentioned this in interview ({date}) — {link}
- {user2} asked about this via bot ({date})
- {user3} competitive pressure: {competitor} launched this

### Signal Strength
{n} users, {frequency}, {intensity}

### Impact if we don't act
{what happens if we defer}

---
*Proposed by: Product Chief | Awaiting CTO review*
```

#### D. Escalate
For critical issues (security vulnerability, churn risk, production incident):

Post to #decisions with urgency:
```bash
gh api graphql -f query='mutation { createDiscussion(input: { repositoryId: "{repo_id}", categoryId: "{decisions_cat_id}", title: "⚠️ ESCALATION: {description}", body: "{details}" }) { discussion { url } } }'
```

---

### Step 9: Post Receipt

After all comments AND actions, post the receipt:

```bash
gh api graphql -f query='mutation { addDiscussionComment(input: { discussionId: "{id}", body: "🐝 *Dispatched to: {agents}*\n📋 *Actions: {action_summary}*" }) { comment { url } } }'
```

Receipt format:
```
🐝 *Dispatched to: CS Lead, Product Chief, Account Mgr*
📋 *Actions:*
- *Updated #27 — added interview signal (3rd user mention)*
- *Created #40 — WeChat contact import (idea, P2)*
- *Priority proposal: #27 P1→P0 posted to #decisions*
```

If no actions were taken:
```
🐝 *Dispatched to: CS Lead, Product Chief, Account Mgr*
📋 *Actions: none — analysis only*
```

### Step 10: Update State

Update `.claude/hive/dispatcher-state.json` with:
- Discussion ID
- Current `updatedAt`
- Timestamp of processing
- Agents dispatched
- Actions taken (list of strings)

### Step 11: Summary

```
Dispatcher: processed {n} discussions, {m} comments, {k} actions ({action_types})
```

## Constraints
- Do NOT write code or create PRs
- Do NOT modify files except .claude/hive/dispatcher-state.json
- Do NOT react to #daily-standup
- Maximum 3 agent comments per discussion
- New issues default to P2 (never auto-assign P0 unless security)
- Priority changes are PROPOSALS — CTO decides
- Never auto-send emails or messages to users (drafts only, human approves)
