# feasibility — Assess Feature Feasibility

## When to Use
Innovator uses this after ideation, for each proposed feature that warrants deeper analysis.

## Inputs
- Proposed feature idea (from ideation output)
- ADRs (`docs/adr/`) for architectural constraints
- Tech stack context (`context/tech-stack.md`)
- Current codebase structure

## Procedure

1. Read the proposed feature idea — extract what needs to be built
2. Check architecture compatibility — read ADRs and tech-stack to see if current architecture supports it
3. Estimate complexity:
   - Lines of code / number of files affected (rough range)
   - New modules or services required
   - Database schema changes needed
4. Identify dependencies:
   - New libraries or frameworks required
   - External APIs or services needed
   - Cross-team or cross-agent coordination
5. Assess maturity-stage fit — is this appropriate for the product's current stage?
6. Score feasibility 1-5:
   - **5**: Trivially fits current architecture, minimal effort
   - **4**: Fits with minor extensions, moderate effort
   - **3**: Requires meaningful new work, some unknowns
   - **2**: Significant architectural changes needed, high effort
   - **1**: Would require fundamental rearchitecture or is technically impractical
7. Post assessment to `#features` as a reply to the original idea:

```markdown
---
agent: innovator
type: analysis
severity: info
tags: [feasibility]
mentions: []
requires: review
---

## Feasibility Assessment: {feature name}

### Architecture Fit
{compatible / needs extension / incompatible} — {explanation}

### Complexity Estimate
- Scope: {files/modules affected}
- New components: {list}
- Schema changes: {yes/no — details}

### Dependencies
- Libraries: {list or "none"}
- External APIs: {list or "none"}
- Coordination: {agents/teams involved}

### Maturity Fit
{appropriate / premature / overdue} — {rationale}

### Feasibility Score: {1-5}
{one-sentence rationale}
```

## Output Format
Single post to `#features` (as reply to the idea) in the template above.

## Rules
- Always read ADRs before scoring — existing decisions constrain what is feasible
- A score of 1-2 is not a rejection, it is a signal that the idea needs rethinking or phasing
- Be honest about unknowns — "unknown" is better than a guess
- Never conflate feasibility with desirability — this skill assesses "can we", not "should we"
