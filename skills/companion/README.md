# Companion Skills

These are **environment skills**, not hive skills. The difference:

- Hive skills (`skills/strategy/`, `skills/infra/`, …) are prompts loaded by scheduled agents. They never run standalone.
- Companion skills are installed into a Claude Code environment (`~/.claude/skills/` or a project's `.claude/skills/`) and invoked either **by a human** as a slash command or **by hive agents** through the capability layer (`protocols/capabilities.md`).

They are vendored here so a fresh clone of hive is self-sufficient: they are the reference providers for the `ticket-authoring` and `ticket-implementation` capabilities, and no equivalent ships natively with Claude Code.

| Skill | Provides capability | What it does |
|-------|--------------------|--------------|
| `write-ticket` | `ticket-authoring` | Spec-driven ticket drafting for any tracker (Jira, GitHub Issues, Linear, markdown) — EARS/Given-When-Then ACs, DoD, `[NEEDS CLARIFICATION]` markers, optional workflow-track seeding |
| `implement-ticket` | `ticket-implementation` | Ticket → PR pipeline: retro-spec, enrichment, plan with AC-coverage gate, adversarial gap analysis, **human plan approval (hard gate)**, TDD, parallel review, PR |

Both resolve project context in cascade: `.claude/hive/config.json.workflow` (hive-managed project, any framework) → `conductor/index.md` → frugal repo scan. Both are tracker-agnostic via their `references/tracker-adapters.md`.

## Installing

**Recommended — symlink (stays in sync with the repo):**

```bash
ln -s {HIVE_ROOT}/skills/companion/write-ticket ~/.claude/skills/write-ticket
ln -s {HIVE_ROOT}/skills/companion/implement-ticket ~/.claude/skills/implement-ticket
```

**Or copy** into the environment (`~/.claude/skills/`) or a single project (`<project>/.claude/skills/`) if you want a frozen version.

The hive `setup` skill offers this installation automatically when it finds the `ticket-authoring` / `ticket-implementation` capabilities unmapped (Step 5).

## Editing

Edit them HERE (the hive repo is the canonical source) and let installs be symlinks wherever possible. If you improve one mid-project, commit the change here.
