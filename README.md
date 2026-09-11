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

## Issue listings

"All repos" means repositories accessible to the signed-in account **through
the current MCP authorization**, including owned, collaborator, and organization
repositories where authorized. This can be narrower than what you see on GitHub.
If the connector cannot enumerate that scope, the skill explains the gap and
asks for explicit repositories or a narrower scope. Public search, owned-only
lists, and issues assigned to you are not substitutes for complete enumeration.

Listings default to **open issues, with no date cutoff**. Ask for closed or all
states when needed. Repository and issue pages are both paginated, PR records
are excluded, and counts distinguish complete results from partial retrieval.
Explicit "all repos" includes forks and archives unless filtered.

Each repository gets its own Markdown table:

### owner/repo

| Issue | Title | State | Labels | Assignees | Updated | Proposed next action |
| --- | --- | --- | --- | --- | --- | --- |
| owner/repo#42 | Example issue | Open | bug | @maintainer | 2026-09-01 | Inspect reproduction details and draft a reply |

This row illustrates the format; real results link to the actual repository and
issue. Metadata-only recommendations are marked preliminary. Empty repositories
get a no-matching-issues row only after a complete successful query; access
denials, disabled issues, and rate limits get explicit status rows instead.
Large results can continue in labeled batches, without silently presenting the
first page as all issues. User-controlled text is escaped for valid tables.

## Draft, approve, post

The reply skill reads the issue and its discussion, proposes an evidence-based
answer, and shows the exact destination and full text as **Draft - not posted**.
It asks for approval before posting. Draft-only requests remain draft-only.

- Rejection means no write.
- Edits require a new complete preview and approval.
- Approval covers one exact comment on one issue, not future or bulk replies.
- Material changes to the issue/discussion require reassessment and renewed approval.
- The posted comment is read back, and its actual URL is returned.
- An ambiguous timeout is investigated before any retry to avoid duplicates.

**The only supported write is a new issue comment.** No issue creation, metadata
updates, close/reopen, comment edits/deletions, PR workflows, repository changes,
or permission changes. Linked PRs can be read as evidence for an issue answer,
but never become targets for commenting or review.

These are **skill behavior rules, not OAuth permission restrictions**. The
connector may expose broader tools. Use appropriate GitHub permissions and
organization policies in addition to the skill instructions.

## Authorization and prerequisites

The host needs GitHub MCP tools for issue reads, repository enumeration for
all-repo requests, and an authorized issue-comment tool for posting.
Missing tools, permissions, organization approval, or SSO access are reported
explicitly, not treated as empty results or successful actions.

For an OAuth app, `public_repo` is a starting scope for approved comments on
public repositories. It does not grant access to private repositories. Private
repository access with classic OAuth generally requires the broader `repo`
scope, plus the user's access and any organization authorization. Request that
broader scope only when needed; these skills do not change OAuth configuration.
`read:org` alone does not grant private repository contents access.

The plugin requires neither a local clone nor GitHub CLI. Do not paste tokens
into chat. The manifest's `OAuthPluginVault` reference identifies an existing
OAuth registration; it is not an access token and does not grant permissions by
itself. Ensure the registration permits app ID
`7fee2dee-05a8-48be-9270-9cfb97603d77` if restricted by app ID.

## Package

Version **1.1.0**, using stable Microsoft 365 manifest schema **1.30**.
Dynamic tool discovery omits `mcpToolDescription` and requires a supporting host.

```text
github-cowork-plugin.zip
  manifest.json
  color.png
  outline.png
  skills/
    github-issues/SKILL.md
    github-issue-reply/SKILL.md
    shared/references/github-workflow.md
```

The manifest and icons remain at the archive root. Both skills require the
shared reference. The committed root ZIP is the installable package.

To rebuild from the repository root using Python's standard library, write a
fresh archive so removed skill files cannot survive from a previous version:

```sh
python3 - <<'PY'
from pathlib import Path
from zipfile import ZIP_DEFLATED, ZipFile

files = [Path("manifest.json"), Path("color.png"), Path("outline.png")]
files += sorted(path for path in Path("skills").rglob("*") if path.is_file())
with ZipFile("github-cowork-plugin.zip", "w", ZIP_DEFLATED) as package:
    for path in files:
        package.write(path, path.as_posix())
PY
```

This is a packaging command for the developer, not a runtime skill dependency.
It includes only the manifest, both icons, and the contents of `skills/`.
Do not ZIP the whole repository: exclude `setup/`, this README, `.git/`, other
ZIPs, and credentials. Never store secrets inside the packaged `skills/` folder.
Upload the ZIP through your tenant's custom-plugin upload flow. Your tenant
must support custom plugins, manifest 1.30, and dynamic MCP discovery.

## GitHub Releases

The [release workflow](.github/workflows/release.yml) builds a fresh package
when a tag such as `v1.1.0` is pushed. The tag must match `version` in
`manifest.json`. It publishes `github-cowork-plugin.zip` as a GitHub Release
asset, containing only the manifest, icons, and `skills/` contents.

See [publishing a release](setup/README.md#5-publish-a-github-release) for the
steps and authorization considerations.

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
