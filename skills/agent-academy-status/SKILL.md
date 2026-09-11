---
name: agent-academy-status
description: >-
  Report the status of microsoft/agent-academy from live GitHub issues and pull
  requests. Use for repository health, open backlog, closed or merged work,
  team status, blockers, priorities, "what has been closed?", and "what should
  we work on next?". Cover open issues/PRs and closed/merged history without
  a default date cutoff, with clear counts and evidence-backed next actions.
  This is a GitHub work-status report, not survey feedback, visitor analytics,
  course-completion verification, or badge issuance.
compatibility: Requires the GitHub remote MCP connector and access to microsoft/agent-academy.
---

# Agent Academy repository status

Read [the shared GitHub workflow](../shared/references/github-workflow.md)
first. A report is read-only; recommending an action does not authorize it.

## Scope

Default to `microsoft/agent-academy`, covering the current open backlog and
historical closed/merged work. **Do not default to the last 7 days or any other
time window.** If the user supplies a period, apply it explicitly and say which
events it filters. Do not repeatedly ask for a date range when none is needed.

For an overall report, include the groups below. If the user requests only
closed work, a content area, or another narrower view, honor that scope.
For "what should we work on next?", go directly to a prioritized status view
without the task picker.

## Retrieve and count

Keep these populations distinct:

| Group | Definition |
| --- | --- |
| Open issues | Current open issues, excluding PRs |
| Open pull requests | Current open PRs, including drafts identified separately |
| Closed issues | Currently closed issues, with closure reason when available |
| Merged pull requests | PRs with a merge recorded |
| PRs closed without merge | Closed PRs with no merge recorded |

Closed issues are not automatically completed fixes: they can be duplicates,
not planned, or otherwise closed. A reopened issue belongs in the open backlog;
do not count each historical close event as a separate closed issue.
Merged PRs are a subset of closed PRs, so avoid counting them twice.

Use exact, scope-matching server counts when available, or count deduplicated
metadata through complete pagination. A combined issue count may include PRs;
do not use it as an issue-only total. If the history exceeds search caps, use
list pagination or other supported count capabilities. If unavailable, state
the partial scope; do not extrapolate all-history totals from one page.

Retrieve enough metadata to group by category and inspect current blockers.
Do not fetch all historical comment bodies merely to compute a count. Use
discussion/details reads for the items behind substantive recommendations.
Treat inferred classifications separately from actual labels.

For closed-history displays, order issues by `closed_at` and merged PRs by
`merged_at`; use `closed_at` for PRs closed without merge. Do not mistake
`updated_at` for completion time. If the API cannot sort by these fields,
paginate the relevant metadata and sort it before claiming a global "most
recent" list, or explicitly label the result as a retrieved subset.

The default 20-row limit is only for display. State "showing N of M" when the
matching total is reliable. Do not imply 20 rows are the entire history or a
seven-day sample. Continue pagination for an explicit exhaustive request.

## Interpret the work

Separate curriculum/content development, learner support/bugs,
badge/completion administration, and maintenance/dependencies. Refresh current
labels and infer categories from context where needed. Report ambiguous items
as unclassified instead of forcing them into a misleading metric.

Find learner blockers, unanswered actionable issues, missing ownership,
draft PRs awaiting author work, reviews waiting on decisions, failed/pending
checks, and confirmed dependencies between issues and PRs. Link related
items without treating one issue plus its fixing PR as two independent tasks.

Do not equate lack of assignment with abandonment or old age with low value.
Use evidence from comments, linked work, and applicable ownership guidance
before proposing a specific person or closure. Never assume badge entitlement.

No trend, throughput, or "improved since last week" claim without a comparable
historical baseline. An all-history closed count is not a time-bounded delivery
metric. Counts describe GitHub work, not actual course completions or awards.

## Report

Use this compact structure, omitting empty detail sections when appropriate:

1. **Summary:** most important current facts and overall data coverage.
2. **Open backlog:** counts and categories; include draft/review/check states
   where available, and a short linked table of work needing attention.
3. **Closed and merged work:** separate totals for closed issues, merged PRs,
   and PRs closed without merge, followed by recent examples ordered by the
   correct event times. Explicitly state that there is no default date cutoff.
4. **Recommended next actions:** ranked, concrete actions with source evidence,
   owner basis if known, and whether an action is a draft or needs a decision.
5. **Scope and limitations:** repository, observation time/timezone, filters,
   count completeness, list coverage, and unknown checks or inaccessible data.

Use linked `microsoft/agent-academy#number` references. Keep recommendations
distinct from committed work and GitHub metadata. Do not publish the report
as a GitHub comment automatically.

## Follow-through

For deeper issue analysis, read [issue triage](../agent-academy-issues/SKILL.md).
For a detailed PR review, read [pull requests](../agent-academy-pull-requests/SKILL.md).
Reuse the user's scope and already-fetched evidence where suitable.

If the user requests a supported change from the recommendations, use the
shared exact-preview and explicit-confirmation flow. A request for a report
or a "next actions" list is not permission to execute those actions.
