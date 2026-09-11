---
name: github-issue-reply
description: >-
  Help answer a GitHub issue in Copilot Cowork: read the issue and discussion,
  propose a helpful evidence-based reply, revise it with the user, and post
  the exact reply as a new issue comment only after explicit approval.
  Use for "draft an answer", "how should I respond to this issue?", and
  approving or revising an already previewed issue reply. Supports any
  explicitly selected repository available through the GitHub MCP connection.
  Not for PR comments, issue state changes, labels, or assignments.
compatibility: Requires GitHub remote MCP issue reads and an authorized issue-comment tool to post approved replies.
---

# GitHub Issue Reply

Read [the shared workflow policy](../shared/references/github-workflow.md)
first. Posting follows its exact-preview, approval, fresh-read, and verification
contract. A new comment on an issue is the only write this workflow supports.

## Resolve the conversation

Use the supplied issue URL or established `owner/repo` and issue number.
For ambiguous numbers across repositories, ask which destination. Never default
to the plugin source repository. If the user has not selected an issue, use
[GitHub Issues](../github-issues/SKILL.md) to show candidates or ask for its URL.
Do not ask again when the target is clear.

If this is an approval or revision of an existing draft, recover the exact
preview and destination from the conversation. If either is missing, re-preview
rather than treating a generic "yes" as authorization.

## Understand before answering

Read the current issue body, state, labels, and paginated discussion. Confirm
the record is an issue, not a PR. Check whether the question has already been
answered or new information changes the appropriate response.

Retrieve relevant repository documentation, contribution guidance, or linked
fixes through MCP where available. Do not read unrelated private repositories
just to enrich an answer. Linked PRs can provide issue-resolution evidence;
do not start a PR review or post a PR comment.

Separate what is established from hypotheses. A proposed PR is not a merged
fix, and a merged fix is not proof that the reporter's environment is resolved.
Do not claim tests, reproduction, or product behavior you have not verified.
If key details are missing, draft specific clarifying questions instead of
inventing a solution. If discussion retrieval is incomplete, disclose that and
do not post until the evidence needed for a reliable answer is available.

## Propose the answer

Write a natural, helpful response appropriate to the issue: acknowledge the
reported problem briefly, explain supported findings, give concrete next steps,
and ask only necessary questions. Link supporting public or same-repository
evidence when useful. Avoid unnecessary personal data, internal notes, boilerplate,
claims of maintainer authority, and promises about fixes or delivery dates.

Show:

**Destination:** linked `owner/repo#number`.

**Draft - not posted**

The complete proposed comment, clearly separated from private analysis.

If the user explicitly requested drafting only, provide the draft without
pressuring them to post. Otherwise ask using the host's question UI if available:

> Post this exact comment to owner/repo#number?

Wait for the user's response. Do not call a write tool in the same step as an
unanswered approval request.

## Handle the decision

- **Reject:** acknowledge; do not write.
- **Revise:** show the complete revised draft and ask again. The old approval
  cannot authorize new text.
- **Approve:** approval must unambiguously refer to the displayed destination
  and exact text. Re-read the issue and discussion immediately before posting.
  If material context changed, reassess and obtain renewed approval.
- **Multiple issues:** use a separate draft and approval for each issue.
  Never broadcast one reply across all repositories from a blanket instruction.

Confirm commenting is possible and the target is still an issue. Explain
locked, archived, inaccessible, or unsupported targets without attempting an
alternative write. The only payload is the approved comment text on that issue;
do not add a label, assignment, closure, signature, or other unapproved change.

After posting, read back the comment and verify its text and destination.
Return the actual created comment URL and a concise outcome. If the write or
readback is uncertain, say what is known and stop. After a timeout, check for
the comment before any retry; do not post duplicate replies.

If asked to close the issue, edit its body, change metadata, reply to a PR, or
perform other writes, explain that this workflow only posts approved issue
comments. Do not reinterpret a prohibited action as permission to post.
