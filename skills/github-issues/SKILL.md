---
name: github-issues
description: >-
  Browse, list, filter, and triage GitHub issues in Copilot Cowork using the
  GitHub remote MCP connection. Use for issues in a specific owner/repo,
  iterating over issues across all repositories accessible to the signed-in
  account, issue backlog reports, and proposed next actions. Produce a separate
  Markdown table per repository with linked issues and clear coverage.
  This skill is read-only. For drafting an answer and posting it after approval,
  use github-issue-reply. Do not use for pull-request workflows.
compatibility: Requires GitHub remote MCP tools and authorization for the requested repositories.
---

# GitHub Issues

Read [the shared workflow policy](../shared/references/github-workflow.md)
first - it governs scope, pagination, and safe data handling for this skill.

## Select repositories

Use a provided `owner/repo`, URL, or explicit repository list directly; ask one
scope question only if neither that nor "all accessible repositories" is
established. Verify the enumeration capability actually covers the requested
scope before treating any list as complete (see shared policy).

## Retrieve and triage

For each selected repository, paginate matching issues independently,
collecting number, title, state, labels, assignees, update time, and closure
info. Metadata alone is enough for a plain list; for a substantive next-action
recommendation, also check the relevant issue body/comments and linked fixes,
and label metadata-only suggestions as preliminary. Never invent priority,
ownership, or resolution - old age alone is not grounds for closure.

## One Markdown table per repository

Open with a brief scope/coverage summary, then one table per repository in
stable `owner/repo` order:

```markdown
## owner/repo

State: open. Coverage: 2 matching issues; complete.

| Issue | Title | State | Labels | Assignees | Updated | Proposed next action |
| --- | --- | --- | --- | --- | --- | --- |
| [owner/repo#12](https://github.com/owner/repo/issues/12) | Example bug | Open | bug | @maintainer | 2026-09-01 | Read reproduction details and assess a reply |
```

The row is a format example, not real data. Use `None` for verified-empty
labels/assignees and `Unknown` for missing data. A successfully empty query
gets a single "No matching issues" row; access denial, disabled issues, or
rate limits get an explicit status row instead - never disguise an error as
"no issues". Mark archived repositories rather than skipping them, but do not
propose commenting there.

Close with observation time/timezone, repositories covered vs. discovered, and
any incomplete pages, so totals are only claimed exact when retrieval is complete.

## Continue to a proposed answer

When the user selects an issue for a reply, hand off to
[GitHub Issue Reply](../github-issue-reply/SKILL.md) with the exact repository,
issue number, and evidence already gathered - it will refresh context itself.
This skill never posts anything; a list of next actions is not authorization.
