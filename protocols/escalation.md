# Escalation Protocol

## Notification Channels

Channels are adapter ports — the concrete tool (Slack, Telegram, email provider, SMS gateway…) is configured per project in `.claude/hive/adapters/`.

| Channel | Port | When |
|---------|------|------|
| GitHub Discussions | `gh discussion` | Always (every post) |
| Primary chat channel | `adapter:notify.primary` | Level 1+ (review, approval, urgent) |
| Email | `adapter:notify.email` | Level 2+ (approval, urgent) |
| Urgent channel (SMS/phone/pager) | `adapter:notify.urgent` | Level 3 only (critical), optional |

## Escalation Timeline

```
T+0     Approval request posted
        → GH Discussion created
        → notify.primary message sent
        → notify.email sent (if Level 2+)

T+2h    No response on Level 2
        → notify.primary reminder

T+3h    No response on Level 2
        → notify.urgent (if configured)
        → CTO auto-defers non-critical items

T+6h    Non-critical auto-deferred
        → Labeled "deferred"
        → Added to next standup

T+30min No response on Level 3 (CRITICAL)
        → notify.urgent
        → If still no response: execute pre-authorized safe action
          (rollback, disable feature flag, scale down)
```

## What Requires Each Level

### Level 3 — Human Approval Required

- Deploy to production
- Send email/message to customer
- Merge breaking architectural change
- Any spend > $10/day on any service
- Delete data or destructive operations
- External communications to users
- New dependency adoption
- Discounting or billing changes

### Level 2 — CTO Decides (Human Notified)

- Non-breaking architecture changes
- Feature prioritization changes
- Adopt new internal pattern
- Sprint goal adjustment
- Agent role/schedule changes

### Level 1 — Agent Consensus

- Refactoring decisions
- Test strategy changes
- Documentation structure changes
- Internal tooling updates

### Level 0 — Autonomous

- Routine reports and health checks
- Progress updates
- Metrics digests
- Code commits to feature branches

## Notification Message Formats (notify.primary)

### Level 1 — Notify
```
HIVE — {project}

{emoji} {type}: {title}
From: {agent}

{1-2 sentence summary}

Link: {GH Discussion URL}
```

### Level 2 — Approval
```
HIVE — {project}

APPROVAL NEEDED
From: {agent}
Re: {title}

{summary}
{key data point}

Reply YES / NO / LATER
Link: {GH Discussion URL}
```

### Level 3 — Urgent
```
HIVE — {project}

URGENT: {title}

{what's wrong}
{impact}
{recommended action}

Reply YES to proceed or NO to hold.
```

## Agent Behavior While Waiting

1. Post approval request
2. Mark task as "waiting-human" in context.md
3. Continue with other queued work
4. The dispatcher run checks `.claude/hive/approval-queue.json` on its schedule
5. When approved → resume blocked task
6. Agents are NEVER idle waiting — always pick up next available work
