# Agent Academy for Copilot Cowork

A community-built Copilot Cowork plugin for the team maintaining
[`microsoft/agent-academy`](https://github.com/microsoft/agent-academy).
It uses the official GitHub remote MCP server at
`https://api.githubcopilot.com/mcp/` to retrieve live repository data, recommend
next actions, and carry out a limited set of explicitly confirmed changes.

## Skills

| Skill | Purpose | Example request |
| --- | --- | --- |
| `agent-academy-guide` | Help choose a workflow when intent is unclear | "Help me manage Agent Academy." |
| `agent-academy-issues` | List and triage issues; draft replies and next actions | "Which Agent Academy issues need attention, and what should we do next?" |
| `agent-academy-pull-requests` | List PRs, assess blockers, and review specific changes | "List open PRs and recommend the next action for each." |
| `agent-academy-status` | Summarize the backlog and closed/merged work | "Give me the current Agent Academy status, including what has been closed." |

There is no mandatory quiz. Clear requests go straight to the relevant workflow.
Only vague requests get a short task picker. A detailed PR review without a
target prompts for a PR after showing candidates.

The skills focus on `microsoft/agent-academy`, not this plugin repository.
They separate curriculum development, learner support, badge administration,
and maintenance so high-volume badge issues do not hide course blockers.
PR reviews use applicable contribution and writing guidance from the PR's base
branch, along with changed files, discussion, and available checks.

Reports have **no default date cutoff**. They distinguish open issues, open PRs,
closed issues, merged PRs, and PRs closed without merging. Closed does not always
mean completed. Lists show up to 20 rows by default, with coverage and totals
where available; this is a display limit, not a history filter. Incomplete
pagination, unavailable checks, and missing permissions are disclosed.

These skills do not analyze surveys, verify course completion, or issue badges.
They complement, rather than replace, Agent Academy's existing feedback-report
workflow.

## Confirmation before changes

Analysis, reports, and drafts do not change GitHub. When you request a supported
action, the skill shows its exact target and content, asks for approval, re-reads
the target for changes, and then executes only what you approved.

| Supported after explicit confirmation | Not supported by these skills |
| --- | --- |
| New issue/PR conversation comments | Submitted or inline PR reviews, approvals, and request-changes reviews |
| Add/remove existing labels and assignees | PR merges or PR close/reopen |
| Request PR reviewers | New issues/PRs, repository file/branch edits, or workflow runs |
| Close/reopen issues | Badge issuance, comment edits/deletion, review-thread resolution, or permission changes |

An approved batch must enumerate every target and operation. Rejection leaves
GitHub unchanged; a material change to the target requires a renewed preview.
Unrelated labels and assignees are preserved. Ambiguous writes are read back
before any retry to avoid duplicate comments.

These are **skill behavior rules, not OAuth permission restrictions**. The GitHub
connector may expose broader capabilities, and the skills cannot technically
remove those tools or enforce repository-scoped authorization. Use appropriate
GitHub permissions and organization policies in addition to the skill guidance.

## App package

- `manifest.json`: Microsoft 365 app manifest with the supplied
  `OAuthPluginVault` reference ID.
- `color.png`: 192x192 GitHub mark on a dark background.
- `outline.png`: 32x32 white GitHub mark on a transparent background.
- `skills/`: four registered skill folders and their shared workflow policy at
  `skills/shared/references/github-workflow.md`.

The manifest uses stable schema version `1.30` (August 2026) and omits
`mcpToolDescription` to enable dynamic tool discovery, supported by manifest
versions 1.29 and later when the host permits it.
No hardcoded tool catalog, scripts, or access tokens are included in the plugin.
The skills discover the connector's available MCP tools at runtime; they do not
require the GitHub CLI, a local clone, or a personal access token pasted into chat.
The vault reference identifies an existing OAuth configuration; it is not an
access token and does not create or configure OAuth registration.

## Package and install

From the repository root:

```sh
zip -r github-cowork-plugin.zip manifest.json color.png outline.png skills/
```

Upload the ZIP through your tenant's supported custom-app/plugin upload flow.
The manifest and two icons must be at the ZIP root, alongside `skills/`.
Include the shared reference folder; the skills require it before GitHub access.
Your tenant must allow custom plugins, manifest version 1.30, and dynamic MCP
tool discovery.

Before uploading, ensure the OAuth registration is available to your tenant and,
if restricted to specific app IDs, permits `7fee2dee-05a8-48be-9270-9cfb97603d77`.
Users must authorize GitHub access; the vault reference alone does not grant it.
Live sign-in and tool invocation must be confirmed in Copilot Cowork.

If your upload channel requires manifest v1.28, do not just change the version:
that schema also requires an `mcpToolDescription.file` reference and a bundled
tool-description JSON file containing the actual MCP tool definitions.

## Try it in Cowork

The host must expose GitHub MCP read capabilities for the requested data and
write capabilities plus repository permissions for confirmed actions.
Missing tools or access should produce a clear limitation, not an empty report
or a claim that an action succeeded.

Use these acceptance scenarios after uploading the package:

1. Ask "Help me manage Agent Academy." Expect a short workflow choice, then ask
   "List open PRs." Expect a PR list without another picker.
2. Request repository status with no date range. Expect open and closed/merged
   work, distinct issue/PR counts, linked evidence, and explicit coverage limits.
3. Choose a specific PR for review. Expect file/line evidence and applicable
   authoring guidance, with unknown checks or unreviewed files called out.
4. On an existing test issue that you are authorized to change, request a
   low-risk label update. Inspect the exact preview and reject it first: nothing
   should change. Request it again, approve the preview, and confirm only that
   update was made. Any cleanup needs its own explicit approval.
5. Ask to approve or merge a PR. Expect the skill to explain that those actions
   are outside its scope, without submitting a review or merging.

Local fixture-based evaluations cannot establish live Cowork routing, OAuth
behavior, or GitHub writes. Those require the connected host and user-authorized
acceptance scenarios; never use production issues for unapproved test writes.

## Branding and publishing

The icons are derived from the
[GitHub mark](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png).
GitHub's logo remains subject to its
[logo usage guidelines](https://github.com/logos).
This plugin is not an official GitHub app.

Developer metadata identifies this repository's owner. The privacy and terms
URLs currently point to GitHub's service policies, not a separate policy for
this community plugin. Review and replace these with appropriate publisher
policies before distributing the plugin.

## References

- [Build plugins for Copilot Cowork](https://learn.microsoft.com/microsoft-365/copilot/cowork/cowork-plugin-development)
- [Register MCP servers as agent connectors](https://learn.microsoft.com/microsoftteams/platform/m365-apps/agent-connectors)
- [Microsoft 365 app manifest versions](https://learn.microsoft.com/en-us/microsoftteams/platform/resources/schema/manifest-schema)