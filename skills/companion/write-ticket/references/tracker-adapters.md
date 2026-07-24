# Tracker Adapters — detection, reads, writes

The skill core never talks to a tracker directly. All tracker I/O goes through the
adapter resolved here. This keeps the pipeline identical across Jira, GitHub Issues,
Linear, or no tracker at all.

## Detection (run once, in Phase 0)

Probe in this order and use the **first** match. When two trackers are plausibly
configured (e.g. a GitHub repo in a Jira shop), ask the user once and remember the
answer for the session.

| Priority | Signal | Adapter |
|---|---|---|
| 1 | A project/user skill dedicated to a tracker exists (e.g. a `jira` skill in the available-skills list) | **Delegate to that skill** — it is the project's single write boundary |
| 2 | `acli` on PATH and authenticated (`acli jira auth status` succeeds), or an Atlassian MCP server is connected | **Jira (direct)** |
| 3 | `git remote get-url origin` is a GitHub URL and `gh auth status` succeeds | **GitHub Issues** |
| 4 | A Linear MCP server is connected (search tools for `linear`) | **Linear** |
| 5 | None of the above | **Local markdown** fallback |

**Single write boundary (non-negotiable):** if the project defines its own skill or
tooling for tracker writes, ALWAYS delegate to it — never call the underlying CLI/MCP
directly, even when available. Project skills centralize auth, format conversion,
custom fields, and idempotency. Bypassing them breaks all four.

## Adapter operations

Each adapter must provide these operations. If one is unsupported, degrade as noted.

### `read(ticket_id)` → title, body, status, labels, parent/epic, comments

- **Jira**: `acli jira workitem view <KEY>` (key-value text output — parse lines; it
  does NOT support `--output-format json`). Retry once on failure, then ask the user
  to paste the ticket content.
- **GitHub**: `gh issue view <number> --json title,body,state,labels,milestone,comments`
- **Linear**: MCP `get_issue` (load via ToolSearch first).
- **Local**: read the markdown file path the user provides.

### `create(title, body, type, extra)` → ticket id/URL

- **Jira**: `acli jira workitem create` — pass rich bodies as ADF via `--from-json`
  (Jira Cloud REST v3 rejects wiki markup; `--description-file` rejects ADF). Draft in
  wiki markup for human review, convert at push time. Custom fields (team, sprint,
  epic link…) usually require the REST API/MCP, not acli — if the project has
  conventions for these, they belong in the project's tracker skill, not here.
- **GitHub**: `gh issue create --title … --body-file …` (markdown natively).
- **Linear**: MCP `create_issue`.
- **Local**: write `./tickets/<slug>.md` (create the directory if needed) and tell
  the user where it is.

### `update(ticket_id, body, extra)`

Same channels as `create`. Never destructively replace a body the user hand-edited
without showing the diff first.

### `transition(ticket_id, target_status)`

- **Jira**: `acli jira workitem transition --key <KEY> --status "<Status>" --yes`.
  Transitions are state-dependent and one-hop: if a hop fails, walk the workflow one
  status at a time. Status names are workspace-specific — discover them from the
  ticket's project, don't assume English names.
- **GitHub**: issues have open/closed only; use labels (`gh issue edit --add-label`)
  for in-progress/in-review conventions if the repo uses them.
- **Linear**: MCP `update_issue` with the target workflow state.
- **Local**: update a `Status:` line in the file.

If a needed status/transition does not exist in the workflow, say so plainly and
leave the ticket at its furthest valid status — a missing transition is a tracker
admin matter, not something to work around with a semantically wrong transition.

### `parent_context(ticket_id)` → epic/parent + sibling tickets

- **Jira**: `acli jira workitem view <EPIC>` +
  `acli jira workitem search --jql "parent = <EPIC> ORDER BY created ASC"`
- **GitHub**: milestone issues (`gh issue list --milestone …`) or tracked-by
  task-lists in the parent issue body.
- **Linear**: MCP project/parent issue queries.
- **Local / unsupported**: ask the user for context; do not skip enrichment.

## Ticket ID formats

| Tracker | Pattern | Branch-slug form |
|---|---|---|
| Jira | `ABC-1234` | lowercase: `abc-1234` |
| GitHub | `#123` or bare number | `issue-123` |
| Linear | `ABC-123` | lowercase |
| Local | file slug | slug |

## Failure discipline

Adapter call fails → retry once → then ask the user for the data (paste) or the
action (manual push), and continue the pipeline with what you have. Never silently
skip a phase because the tracker was unreachable, and never fall back to a
*different* write channel than the resolved adapter without telling the user.
