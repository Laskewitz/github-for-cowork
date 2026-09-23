---
name: github-issue-reply
description: >-
  Help answer a GitHub issue: read the issue and discussion,
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
first - posting follows its exact approval contract. A new issue comment is
the only write this workflow supports.

## Resolve the conversation

Use the supplied issue URL or established `owner/repo` + number; ask which
destination only if ambiguous, and never default to the plugin's own repo. If
no issue is selected yet, use [GitHub Issues](../github-issues/SKILL.md) to
find one. For an approval/revision of an existing draft, recover the exact
preview and destination from context - a generic "yes" without a visible draft
means re-preview, not authorization.

## Understand before answering

Read the current issue body, state, labels, and paginated discussion; confirm
it's an issue, not a PR. Pull relevant repo docs or linked fixes as needed, but
don't read unrelated private repositories just to enrich an answer. Separate
established facts from hypotheses - a proposed fix isn't a merged one, and a
merged fix isn't proof the reporter's problem is resolved. If key details are
missing, draft clarifying questions instead of guessing.

## Propose the answer

Write a natural, helpful reply: acknowledge the problem, share supported
findings, give concrete next steps, ask only necessary questions. Avoid
unnecessary personal data, claims of maintainer authority, or delivery promises.

Show the destination (`owner/repo#number`), then the full text under
**Draft - not posted**. If the user asked for a draft only, stop there.
Otherwise ask "Post this exact comment to owner/repo#number?" and wait -
never call the write tool in the same step as an unanswered request.

## Handle the decision

- **Reject:** no write. **Revise:** new full draft + new approval - the old
  approval never carries over to new text.
- **Approve:** must clearly match the shown destination and text. Re-read the
  issue right before posting; if context changed materially, re-preview.
- **Multiple issues:** a separate draft and approval per issue - never
  broadcast one reply across repositories from a blanket instruction.

Confirm commenting is still possible on that issue before posting; explain
locked/archived/unsupported targets instead of finding an alternative write.
After posting, read back the comment, return its real URL, and report clearly
if verification failed. After an ambiguous timeout, check for the comment
before any retry - never post a duplicate.

If asked to close the issue, edit metadata, reply to a PR, or make any other
change, explain that this workflow only posts approved issue comments.
