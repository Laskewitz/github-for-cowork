---
name: agent-academy-guide
description: >-
  Help users choose how to work with the microsoft/agent-academy GitHub repository
  in Copilot Cowork. Use for broad requests such as "help with Agent Academy",
  "what can this plugin do?", or "where do I start?". Offer a short task picker
  when intent is unclear, then use the issue, pull-request, or status skill.
  Do not make clear requests repeat the picker: issue triage goes to
  agent-academy-issues, PR lists/reviews to agent-academy-pull-requests, and
  repository health or "what should we work on next?" to agent-academy-status.
compatibility: Requires the GitHub remote MCP connector for repository data and a host able to read packaged skill files.
---

# Agent Academy task guide

Read [the shared GitHub workflow](../shared/references/github-workflow.md)
before accessing GitHub. It defines repository scope and confirmation rules.

## Start from the user's intent

Use what the user already told you. A skill is a shortcut, not a questionnaire.
Do not test their GitHub knowledge or ask questions whose answers are known.

| User intent | Read and follow |
| --- | --- |
| Issues, bugs, learner support, labels, issue next actions | [Issue triage](../agent-academy-issues/SKILL.md) |
| PR queue, review readiness, a specific diff, review next actions | [Pull requests](../agent-academy-pull-requests/SKILL.md) |
| Status, open and closed work, priorities across the repo | [Repository status](../agent-academy-status/SKILL.md) |

Use the host's supported skill-loading mechanism or read the linked packaged
file directly. Do not assume a tool named after the skill exists. Once a
specialist is selected, follow it rather than returning to this picker. If its
file cannot be loaded, explain the limitation; do not claim it was invoked.

## Offer a picker only when needed

For a vague request, briefly explain that the plugin uses live GitHub data to
help the team manage `microsoft/agent-academy`. Ask one question:

> What would you like to work on?

Use these choices with the host's question UI when available:

- Triage issues
- Review pull requests
- Get repository status

Explain that you can recommend next actions and draft changes, and that supported
GitHub writes require confirmation. If the user only asked what the plugin does,
answer that question without forcing them to choose a task.

## Keep follow-up questions useful

- "List open PRs" needs no target number or extra task picker.
- "Review a PR" without a target: retrieve candidate PRs, show them, and ask
  which one to inspect before doing a deep review.
- "What should we work on next?" is a repository-status request; start there.
- "My issues" or "my reviews": resolve GitHub identity as described in the policy.
- For missing context, ask one focused question at a time, reusing all existing
  filters and choices.
- For disallowed actions such as merging or awarding a badge, explain the
  boundary and provide a draft or relevant analysis instead.
