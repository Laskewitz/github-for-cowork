---
name: github-branch-cleanup
description: >-
  Review repository branches for stale, abandoned, or unsafe cleanup candidates.
  Use when the user wants branch hygiene, stale-branch cleanup, merged-release
  cleanup, or a safe check before deleting branches. Produce a concise branch
  report with age, last activity, merge status, and decision guidance. If the
  user requests a report, send the final summary to the signed-in user through
  Copilot Cowork and Outlook with responsive, Outlook-safe HTML/CSS.
compatibility: Requires repository or remote branch metadata and enough access to inspect branch age, merge state, and recent activity.
---

# Branch Cleanup

Use this skill to identify stale or risky branches before any cleanup is attempted.
It is meant to reduce maintenance overhead without deleting active work.

## Scope and evidence

Resolve the target repository and branch set first. Check the branch list, last
commit activity, whether the branch still has open PRs or unmerged changes, and
whether it appears to be a release, hotfix, or temporary work branch.

Prefer evidence from:

- branch age and last commit date
- merge state against the default branch
- open pull requests or linked work items
- naming conventions like `feature/`, `bugfix/`, `hotfix/`, `release/`

If the repository is ambiguous, ask one narrow scope question before proceeding.

## Decision model

Classify each branch as one of the following:

- **Keep:** active or recently updated work, open review, or still relevant
- **Review:** stale but potentially still useful; needs a human decision
- **Cleanup candidate:** merged, abandoned, or no longer connected to active work
- **Unsafe to delete:** branch contains unmerged work or is tied to a release

Do not mark a branch as safe to delete solely because it is old. Merge status and
latest useful activity matter more than age alone.

## Output format

Open with a summary of the repository and the criteria used. Then produce one
clear table for the cleanup candidates and one for branches needing human review.

```markdown
## Branch Cleanup

Scope: owner/repo • default branch: main • 17 branches reviewed

### Cleanup candidates

| Branch | Age | Last activity | Merge status | Recommendation |
| --- | --- | --- | --- | --- |
| feature/old-poc | 182 days | 2026-02-01 | Merged | Safe to delete |
| bugfix/abandoned | 240 days | 2025-12-20 | Unmerged | Needs human review |
```

Use real branch names and actual evidence; do not fabricate stale states.

## Report requirement

When the user requests a report or cleanup overview, send the final result to the
signed-in user via Copilot Cowork and Outlook. Use responsive, Outlook-safe
HTML/CSS so the summary renders cleanly in desktop, web, tablet, and mobile
clients.

## Boundaries

This skill is advisory. It does not delete branches or rewrite history without
explicit approval. When a branch can be safely cleaned up, recommend the action
and the evidence behind it, but leave the actual deletion to the user or the
authorized workflow.
