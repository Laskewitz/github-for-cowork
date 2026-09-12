# 🐙 GitHub for Cowork

Work with GitHub directly in Copilot Cowork. This community-built plugin uses
the [GitHub remote MCP server](https://api.githubcopilot.com/mcp/) to browse
issues, propose next actions, and draft answers you can approve for posting.

## 🚀 Minimal path to awesome

1. Follow the [OAuth setup guide](setup/README.md) to create a GitHub OAuth app,
   register it in the Teams Developer Portal, and replace the placeholder in
   `manifest.json`.
2. Package `manifest.json`, `color.png`, `outline.png`, and `skills/` into a ZIP.
   Do not include the repository folder, `setup/`, or secrets.
3. In Copilot Cowork, open **Customize -> Add plugin -> Upload package** and
   select the ZIP.
4. In Cowork, try: `List open issues in owner/repo`.

When prompted, sign in to GitHub and approve access. Start with read-only
requests before testing approved comment publishing.

## 🧰 Five GitHub skills

| Skill | What it does | Example | Email report |
| --- | --- | --- | --- |
| `github-issues` | Lists and triages issues in one repo or across accessible repos, with one Markdown table per repo | "List open issues across all repositories I can access." | ✓ |
| `github-priority-matrix` | Turns open issues into a prioritized action plan using an impact vs. effort matrix (low effort/high impact first) | "Give me an action plan for owner/repo based on impact and effort." | ✓ |
| `github-issue-reply` | Reads an issue and discussion, drafts an answer, and posts it only after exact approval | "Propose an answer to owner/repo#42." |  |
| `github-dependency-audit` | Reviews repository dependencies for drift, security risk, and upgrade impact | "Audit the dependencies for owner/repo and rank the risk." | ✓ |
| `github-branch-cleanup` | Spots stale and risky branches and flags safe cleanup candidates | "Review stale branches in owner/repo and show what should be cleaned up." | ✓ |

Use a specific `owner/repo`, issue URL, explicit repository list, or "all repos".
Clear requests go directly to the relevant workflow; there is no mandatory quiz.
If a target is ambiguous, the skill asks one focused question.

## 🎨 Branding and policies

The 192x192 color icon and 32x32 transparent white icon derive from the
[GitHub mark](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)
and remain subject to [GitHub's logo usage guidelines](https://github.com/logos).
This is a community-built plugin, not an official GitHub app.

## 🙌 Credits

Built together in collaboration with [Garry Trinder](https://github.com/garrytrinder).

## 📚 References

- [Build plugins for Copilot Cowork](https://learn.microsoft.com/microsoft-365/copilot/cowork/cowork-plugin-development)
- [Microsoft 365 app manifest reference](https://learn.microsoft.com/en-us/microsoftteams/platform/resources/schema/manifest-schema)
- [GitHub OAuth scopes](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/scopes-for-oauth-apps)
