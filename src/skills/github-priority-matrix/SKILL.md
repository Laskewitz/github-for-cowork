---
name: github-priority-matrix
description: >-
  Turn open GitHub issues into a prioritized action plan using an impact vs.
  effort action-priority matrix: low effort/high impact first, then high
  effort/high impact, then low effort/low impact, then high effort/low
  impact. Use when the user asks for an action plan, a priority matrix, "what
  should I tackle first", or wants open issues ranked by impact and effort
  instead of a plain per-repo listing. Works for one repository, an explicit
  list, or all accessible repositories, and returns a single consolidated
  list. Offer the user a choice between Markdown output or an emailed report
  sent through Copilot Cowork and Outlook with responsive, Outlook-safe
  HTML/CSS. Read-only; does not comment, label, or close anything.
compatibility: Requires GitHub remote MCP tools and authorization for the requested repositories.
---

# GitHub Priority Matrix

Read [the shared workflow policy](../shared/references/github-workflow.md)
first for scope, pagination, and data-handling rules. This skill returns one
prioritized plan rather than [GitHub Issues](../github-issues/SKILL.md)'s
per-repo inventory; reuse its scope/retrieval approach rather than duplicating it.

## Gather candidates

Resolve scope exactly as GitHub Issues does. Default to open issues, paginate
per repository, exclude PRs. For each issue, also collect labels, reaction
counts, comment/participant counts, and linked issues/PRs - if the connected
tools can't return reactions or comments, say so rather than treating it as
zero. Every impact/effort estimate is **preliminary** unless read from an
explicit label.

## Estimate impact and effort

Prefer an explicit repository convention first: labels like `impact:*`,
`effort:*`, `size:*`, or `priority:*` - state when a value came from a label
rather than an inference. Otherwise infer from what's retrievable:

- **Higher impact:** many reactions/commenters; labels like `bug`, `critical`,
  `security`, `regression`; other issues/PRs blocked by or referencing it.
- **Lower effort:** labels like `good first issue`, `easy`, `help wanted`, or
  small `size:*`; a narrow scope; an existing draft PR covering most of it.
- **Higher effort:** labels like `needs design`, `breaking change`, `epic`; a
  multi-system description; no progress at all on a long-open issue.

If an issue has no usable label, reactions, or comments, mark it
**"insufficient signal - unranked"** rather than guessing, and list it
separately at the end.

## Quadrants and ordering

1. **Low effort / High impact** - quick wins, tackle first.
2. **High effort / High impact** - major projects, plan next.
3. **Low effort / Low impact** - fill-ins.
4. **High effort / Low impact** - reconsider; likely not worth doing soon.

Within a quadrant, order by the strongest available impact signal (reactions,
then comments, then an explicit priority label). Classify strictly on the
estimated effort/impact, not on convenience - don't move a high-effort issue
into quadrant 1 just because it has many reactions.

## Output format

Open with scope, filters, observation time/timezone, and a one-line caveat
that estimates are inferred unless labeled. Then one table per quadrant
(add a `Repo` column when scope spans multiple repositories):

```markdown
## 1. Low effort / High impact - do first

| Issue | Title | Impact signal | Effort signal | Proposed action |
| --- | --- | --- | --- | --- |
| [owner/repo#12](https://github.com/owner/repo/issues/12) | Example bug | 14 reactions, `bug` label | `good first issue` | Fix and open a PR; low risk, high visibility |
```

Repeat for quadrants 2-4, then a final "Insufficient signal - unranked" table
noting what data was missing. The example row is a format illustration only -
use real, retrieved issues and links, escaping `|` and newlines in every cell.

Close with total issues considered, the count per quadrant vs. unranked, and
any incomplete repositories/pages - never present a partial pull as the full plan.

## Output delivery

Before producing the final plan, ask the user whether they want it as Markdown
in the chat (default if unspecified) or as an emailed report. If they choose
email, send the final result to the signed-in user via Copilot Cowork and
Outlook. The email body should use responsive, Outlook-safe HTML/CSS, with
clear headings, compact quadrant tables, and readable mobile behavior on
desktop, web, tablet, and mobile clients.

## Boundaries

Read-only: never comments, labels, closes, or reorders anything. To act on an
item, hand off to [GitHub Issue Reply](../github-issue-reply/SKILL.md) with
the exact repository and issue number from the plan.
