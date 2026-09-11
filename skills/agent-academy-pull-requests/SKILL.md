---
name: agent-academy-pull-requests
description: >-
  List and review pull requests in microsoft/agent-academy using live GitHub
  data. Use for the PR queue, waiting reviews, draft PRs, failing checks,
  review readiness, a specific PR diff, and proposed PR next actions. Review
  curriculum changes against the repository's applicable authoring guidance
  and assess maintenance changes in context. Draft feedback and, only after
  explicit confirmation, post conversation comments, adjust labels/assignees,
  or request reviewers. Never submit PR reviews, approve, merge, or close PRs.
compatibility: Requires the GitHub remote MCP connector and access to microsoft/agent-academy.
---

# Agent Academy pull requests

Read [the shared GitHub workflow](../shared/references/github-workflow.md)
first. Its action restrictions apply even if broader MCP tools are available.

## Listing and triage

List open PRs by default, honoring explicit state/author/reviewer filters
without adding a date cutoff. A list request does not require a PR number.
Resolve the authenticated user for "PRs waiting on me".

For each inspected PR, collect the URL, title, author, draft state, head commit,
changed areas, timestamps, assignees, requested reviewers, existing review
decisions, and linked issues. Read available checks, conflicts/mergeability,
and unresolved discussion when the connector exposes them. Identify which
head commit the checks/reviews apply to; stale results do not cover newer changes.

Keep separate:

- Drafts needing author work.
- PRs waiting for review or a maintainer decision.
- Changes requested or unresolved discussion.
- Failed/pending checks or reported conflicts.
- PRs with no known blocker in the data inspected.

Green checks alone do not prove approval, compliance with branch protection,
or merge readiness. Missing checks are **unknown**, not passing. Do not invent
an unresolved-thread count when the server does not expose it.

Show a compact queue:

| PR | Draft/review state | Checks and coverage | Blocker or decision | Proposed next action |
| --- | --- | --- | --- | --- |

Label this as queue triage, not a completed code/content review. Rank next
actions using observed impact and dependencies, and disclose the inspected
subset. Use the shared 20-row display limit and pagination rules.

## Focused review

When a PR number or URL is given, inspect that PR directly. If the user wants
a deep review but has not chosen a target, retrieve and show candidate PRs,
then ask which to inspect. Never choose a large PR silently.

1. Read the PR description, linked issues, comments, existing reviews, and
   changed files/diffs. Record the inspected head SHA and base branch.
2. Retrieve relevant surrounding content at that head SHA. If patches are
   truncated, fetch the file/content comparison with an exposed read capability.
   If unavailable, state the unreviewed scope instead of treating it as clean.
3. Read applicable guidance from the base branch, not the PR's proposed rules:
   `CONTRIBUTING-GENERAL.md`, `WRITING_STYLE.md`, and `.github/CODEOWNERS`.
   Select `CONTRIBUTING-COURSES.md`, `CONTRIBUTING-SPECIAL-OPS.md`, or
   `CONTRIBUTING-COWORK-COLLECTIVE.md` according to actual changed paths.
   Consult `CUSTOM-COMPONENTS.md` when custom documentation components change.
   Fetch only relevant guidance; conventions can evolve.
4. Trace the changes against the intended learner workflow or maintenance goal.
   Distinguish an observed defect from a question requiring product validation.

For course/mission changes, examine applicable requirements for frontmatter,
objectives, prerequisites, mission brief/theory/lab/completion structure, step
sequence, supported product terminology, screenshots and alt text, relative
links/assets, and navigation. Do not apply every course convention to Special
Ops or Cowork Collective; use their specific guidance.

For dependencies, automation, or code, review the actual changed behavior,
compatibility, configuration, and available CI evidence. Do not force mission
structure rules onto a lockfile or claim that a dependency update is safe solely
because it is automated.

Use source references to assess links and assets where possible. Do not claim
to have run builds, checked external links, inspected an image visually, or
executed a lab if the available MCP capabilities did not permit it.

## Findings and recommendations

Report substantive findings first, with:

| Impact | File and line | Evidence and consequence | Suggested fix |
| --- | --- | --- | --- |

Cite the changed file/line at the inspected commit. Do not fabricate line
numbers when only partial content is available. Label editorial preferences
separately from blockers and avoid repeating already-resolved review feedback.
If there are no findings, say "No findings in the inspected scope", then identify
material unreviewed areas or unknown checks rather than guaranteeing correctness.

Recommend author follow-up, a specific missing check, a maintainer decision,
or a reviewer supported by current assignments/CODEOWNERS. Respect CODEOWNERS
last-matching-rule semantics and do not infer access or availability from ownership.
Use linked `microsoft/agent-academy#number` references throughout.

## Confirmed actions, not review submission

When requested, preview a **new PR conversation comment**, label/assignee change,
or reviewer request, then follow the shared confirmation flow. Re-read the
head SHA before posting review-related feedback; if it changed, re-assess and
seek renewed approval for the revised proposal.

If the user asks to approve, request changes through a review, submit an inline
review, resolve a review thread, merge, or close/reopen a PR, explain that these
are outside this skill. Provide findings or a draft the maintainer can use.
Do not translate "approve this PR" into a posted approval comment without asking.
