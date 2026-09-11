# GitHub for Cowork

Work with GitHub directly in Copilot Cowork. This community-built plugin uses
the [GitHub remote MCP server](https://api.githubcopilot.com/mcp/) to browse
issues, propose next actions, and draft answers you can approve for posting.

## Setup

Follow the [OAuth setup guide](setup/README.md) to create a GitHub OAuth app,
register its credentials in the Teams Developer Portal, and connect the
registration to this plugin's manifest.

## Two issue-focused skills

| Skill | What it does | Example |
| --- | --- | --- |
| `github-issues` | Lists and triages issues in one repo or across accessible repos, with one Markdown table per repo | "List open issues across all repositories I can access." |
| `github-issue-reply` | Reads an issue and discussion, drafts an answer, and posts it only after exact approval | "Propose an answer to owner/repo#42." |

Use a specific `owner/repo`, issue URL, explicit repository list, or "all repos".
Clear requests go directly to the relevant workflow; there is no mandatory quiz.
If a target is ambiguous, the skill asks one focused question.

## Try it in Cowork

1. List issues in a specific repository, then across all accessible repositories.
   Expect one table per repo, pagination coverage, and honest capability limits.
2. Ask for an answer to a specific issue. Inspect the draft and reject it:
   nothing should be posted.
3. On an issue you are authorized to comment on, request a revised draft and
   approve the exact text. Expect one verified comment and its link.
4. Ask to close an issue or reply to a PR. Expect an explanation that only
   approved issue comments are supported.

Local fixture evaluations are simulations, not proof of live Cowork discovery,
OAuth, or writes. Real comment tests need explicit user approval.

## Branding and policies

The 192x192 color icon and 32x32 transparent white icon derive from the
[GitHub mark](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)
and remain subject to [GitHub's logo usage guidelines](https://github.com/logos).
This is a community-built plugin, not an official GitHub app.

Developer metadata identifies the publisher. The privacy and terms URLs currently
point to GitHub's service policies, not a separate publisher policy; review and
replace them with appropriate policies before distribution.

## References

- [Build plugins for Copilot Cowork](https://learn.microsoft.com/microsoft-365/copilot/cowork/cowork-plugin-development)
- [Microsoft 365 app manifest reference](https://learn.microsoft.com/en-us/microsoftteams/platform/resources/schema/manifest-schema)
- [GitHub OAuth scopes](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/scopes-for-oauth-apps)
