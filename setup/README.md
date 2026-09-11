# Set up GitHub OAuth for Cowork

[Back to the main README](../README.md)

This guide connects GitHub for Cowork to the GitHub remote MCP server using a
GitHub **OAuth app** and an **OAuth client registration** in the Teams Developer
Portal. These are separate registrations; neither is a GitHub personal access
token or a Microsoft Entra app registration.

## What connects to what

| Value | Where it comes from | Where it goes |
| --- | --- | --- |
| GitHub Client ID | GitHub OAuth app settings | Developer Portal **Client ID** |
| GitHub client secret | GitHub OAuth app settings | Developer Portal **Client secret** only |
| OAuth client registration ID | Developer Portal after saving | Manifest `authorization.referenceId` |
| Microsoft 365 app ID | Root `id` in `manifest.json` | Developer Portal app restriction, if enabled |

**Never put the GitHub client secret or access tokens in the manifest, repository,
ZIP, screenshots, or chat.** The registration ID is a reference to a stored
configuration, not the secret itself. Use your own registration rather than
assuming the ID already present in this repository is available to your tenant.

## Prerequisites

- A GitHub account allowed to create an OAuth app, personally or for an organization.
- A Microsoft 365 account with access to the Teams Developer Portal and permission
  to register OAuth configuration.
- A Cowork tenant that permits custom plugins and supports manifest version 1.30
  and dynamic MCP tool discovery.
- Access to the repositories you intend to use. OAuth does not give a user more
  repository permissions than they already have.

## 1. Create a GitHub OAuth app

1. Open [GitHub Developer settings](https://github.com/settings/developers).
2. Select **OAuth Apps**, then **New OAuth App** or **Register a new application**.
   Choose the appropriate account or organization owner. Do not select
   **GitHub Apps** for this setup.
3. Enter the following application details:

| GitHub field | Value |
| --- | --- |
| Application name | `GitHub for Cowork` |
| Homepage URL | A public page for your plugin, or `https://m365.cloud.microsoft/cowork` for a Cowork test setup |
| Application description | `Use GitHub issues in Copilot Cowork and post replies after approval.` |
| Authorization callback URL / Redirect URI | `https://teams.microsoft.com/api/platform/v1.0/oAuthRedirect` |
| Allow wildcard matching, if shown | Leave disabled; use the exact redirect URI |
| Enable Device Flow | Leave disabled; this setup uses the browser authorization-code flow |

The callback is the Microsoft OAuth redirect endpoint, **not** the GitHub MCP
URL or the homepage URL. Preserve its spelling and capitalization.

4. Review the token-expiration setting. GitHub currently enables **Expire user
   access tokens** by default for new OAuth apps. Keep the Developer Portal
   refresh configuration consistent with this setting; see
   [token expiration and refresh](#token-expiration-and-refresh).
5. Select **Register application**. If editing an existing app, save the
   settings using **Update application**.
6. Copy the **Client ID** from the app's settings.
7. Select **Generate a new client secret** and complete GitHub's authentication
   prompt yourself if requested. Store the secret securely and copy it directly
   into the Developer Portal in the next step.

Optional: upload the GitHub icon as the application logo, following
[GitHub's logo usage guidelines](https://github.com/logos).

## 2. Register the OAuth client in the Teams Developer Portal

Open [Tools > OAuth client registration](https://dev.teams.microsoft.com/tools/oauth-configuration/)
and select **Register Client**. Labels can vary slightly between portal versions.

### App settings

| Portal field | Value or choice |
| --- | --- |
| Registration name | `GitHub for Cowork` |
| Base URL | `https://api.githubcopilot.com/mcp/` |
| Restrict usage by organization | Choose the intended Microsoft 365 tenant scope; see below |
| Restrict usage by Teams app | Prefer **Existing Teams app** with your manifest's root `id` when available |

The Base URL scopes the resource endpoints to which the stored authorization
can be applied. Use the GitHub MCP endpoint, matching `mcpServerUrl` in the
manifest, not `https://github.com/` or the authorization endpoint.

For tenant-only testing, the portal supports **My organization only**.
For cross-tenant distribution, select **Any Microsoft 365 organization**.
Cowork's plugin-development guidance recommends the latter for plugins intended
to work across tenants. This setting controls Microsoft 365 tenant usage; it
does not select a GitHub organization or bypass its access policies.

For app restrictions:

- **Existing Teams app** binds the registration to a specific Microsoft 365
  app ID. Use the root `id` from [manifest.json](../manifest.json), currently
  `7fee2dee-05a8-48be-9270-9cfb97603d77` if you have not changed it.
- **Any Teams app (for testing and store validation only)** is useful for initial
  testing where a fixed app binding is not yet available. Do not treat it as the
  production default; restrict it to the intended app when possible.

Despite the portal's Teams terminology, this registration also supplies the
OAuth configuration used by the Cowork MCP connector.

### OAuth settings

| Portal field | Value |
| --- | --- |
| Client ID | Your GitHub OAuth app's Client ID |
| Client secret | Your GitHub OAuth app's client secret |
| Authorization endpoint | `https://github.com/login/oauth/authorize` |
| Token endpoint | `https://github.com/login/oauth/access_token` |
| Refresh endpoint | `https://github.com/login/oauth/access_token` when using expiring tokens; see below |
| Scope | Start with `public_repo` for public-repository comments; see scope choices below |
| Enable Proof Key for Code Exchange (PKCE) | Enabled; GitHub supports PKCE with `S256` |
| Client password authentication method | **Request body parameters (default)** |

Use the client ID and secret from the **same** GitHub OAuth app whose redirect
URI you configured. Do not put the Microsoft 365 app ID in the Client ID field.
The portal manages the authorization-code exchange and PKCE values; you do not
need to generate them manually.

### Choose GitHub scopes

Use the least access needed for the repositories and actions you intend to use.
The portal's **Scope** field asks for comma-separated entries.

| Need | Scope field |
| --- | --- |
| Read public issues and post approved comments on public issues | `public_repo` |
| Also read organization/team membership where needed | `public_repo,read:org` |
| Access private as well as public repositories | `repo` |
| Private repositories plus organization/team membership lookups | `repo,read:org` |

`repo` includes `public_repo`, so do not request both. `read:org` does not grant
private repository contents access. This plugin does not need workflow, email,
or administrative scopes for its issue workflows.

The screenshot-style configuration `public_repo,read:org` is appropriate if you
need those organization reads, but `read:org` is not mandatory for every issue
request. Public read-only information can be accessed with less scope; this
plugin also supports approved comments, which is why `public_repo` is the
public-repository starting point.

Both `public_repo` and `repo` are broader than comment-only permissions and are
not limited to a single repository. The skills' approval rules do not narrow
the token's permissions. Organization OAuth restrictions or SSO requirements
can still limit access. "All repos" only covers what the connection and its
exposed tools can actually enumerate.

GitHub's raw OAuth protocol uses space-delimited scopes in authorization
requests; follow the **comma-separated format in this portal field**, rather
than manually constructing the authorization URL.

### Token expiration and refresh

For an OAuth app issuing expiring access tokens, configure the Refresh endpoint
as `https://github.com/login/oauth/access_token` and verify that the Cowork
connection can refresh successfully before distributing the plugin.
GitHub uses that endpoint for refreshing as well as exchanging codes.

If you deliberately use an existing non-expiring-token configuration, the
Refresh endpoint can be left blank, as in some older setups. Do not assume
blank is correct for new apps: GitHub now enables token expiration by default.
Requesting `offline_access` also opts a sign-in into expiring tokens and refresh;
it is not needed just to list issues. Do not add it without configuring and
testing refresh support.

### Save and copy the registration ID

Select **Save**, then copy the complete **OAuth client registration ID** shown
in the registration card. Use the copy button rather than copying a visually
truncated value.

This is the value for the manifest. It is **not** the GitHub Client ID, the
GitHub client secret, or the Microsoft 365 app ID.

## 3. Add the registration to the manifest

Open the root [manifest.json](../manifest.json). In the `agentConnectors` entry
whose `id` is `github`, locate:

```text
agentConnectors[].toolSource.remoteMcpServer.authorization
```

Replace only `referenceId` with the complete registration ID you copied.
The relevant `toolSource` fragment should look like this:

```json
{
  "remoteMcpServer": {
    "mcpServerUrl": "https://api.githubcopilot.com/mcp/",
    "authorization": {
      "type": "OAuthPluginVault",
      "referenceId": "YOUR_OAUTH_CLIENT_REGISTRATION_ID"
    }
  }
}
```

This is a fragment of `toolSource`, not a replacement for the whole manifest.
Keep the existing names, icons, skill registrations, and schema version.
Use camel-case **`referenceId`** here, not `reference_id` or
`oAuthConfigurationId`, which belong to other manifest formats.

Do not add a `composeExtensions` section just because some Teams OAuth examples
show one. This plugin uses an **agent connector**, not a message extension.
GitHub client credentials and scope choices stay in the Developer Portal;
only the registration reference is added to this manifest.

If creating a separate independently distributed app, choose its own root app ID
and make the portal's app restriction match it. For updates to the same app,
retain the app ID and increment the app `version` as required by your deployment
flow; `manifestVersion` identifies the schema, not your release version.

## 4. Rebuild, upload, and sign in

### Create the ZIP

Save the manifest, then run the following from the **repository root**, not
from `setup/`. This uses Python 3's standard library and creates a fresh archive,
replacing any existing `github-for-cowork.zip`.

```sh
python3 - <<'PY'
from pathlib import Path
from zipfile import ZIP_DEFLATED, ZipFile

files = [Path("manifest.json"), Path("color.png"), Path("outline.png")]
files += sorted(path for path in Path("skills").rglob("*") if path.is_file())
with ZipFile("github-for-cowork.zip", "w", ZIP_DEFLATED) as package:
    for path in files:
        package.write(path, path.as_posix())
PY
```

**Only package `manifest.json`, the two icons, and `skills/` with its contents.**
Do not ZIP the entire repository. Exclude `setup/`, the root README, `.git/`,
existing ZIPs, test outputs, and any credential files. Keep secrets out of
`skills/` too, because every file in that directory is included.

The resulting layout is:

```text
github-for-cowork.zip
  manifest.json
  color.png
  outline.png
  skills/
    github-issues/SKILL.md
    github-priority-matrix/SKILL.md
    github-issue-reply/SKILL.md
    shared/references/github-workflow.md
```

There must be no enclosing repository folder inside the ZIP. Rebuild whenever
you change the manifest or skills; editing files does not update an existing ZIP.

### Upload and verify

1. Use the newly rebuilt ZIP, not an older downloaded package.
   The prebuilt ZIP does not automatically pick up your new registration ID.
2. Confirm `manifest.json`, `color.png`, and `outline.png` are at the ZIP root
   alongside `skills/`. This `setup/` guide does not need to be in the app package.
3. Upload or update the plugin through your tenant's supported custom-plugin flow.
4. Start with a read request such as "List open issues in owner/repo".
5. Complete GitHub sign-in and consent when prompted, using an account with
   the necessary repository access.
6. Test reply drafting on a suitable test issue. Reject a draft first and confirm
   nothing is posted. Then approve an exact comment only when you intend to
   publish it; verify the resulting comment link.

Registration does not sign every user in or grant repository access. Each user
must authorize GitHub access, and tenant/GitHub organization policies still apply.

## 5. Publish a GitHub Release

The [release workflow](../.github/workflows/release.yml) runs when you push a
tag matching `v*`, or when manually triggered from the Actions tab for an
existing tag. It accepts release tags of the form `vMAJOR.MINOR.PATCH`,
checks that the tag matches `version` in the tagged `manifest.json`, verifies
the registered skill files and icon dimensions, and builds a fresh ZIP from
the tagged source. It does not upload the prebuilt ZIP from the repository.

The workflow packages **only the manifest, both icons, and all files under
`skills/`**, then creates a GitHub Release with generated notes and attaches
`github-for-cowork.zip`. Setup docs, the root README, workflow files, and
other repository files are not included.

Before releasing:

- Ensure GitHub Actions is enabled and repository/organization policies allow
  the workflow's `contents: write` permission to create releases.
- Review the package for secrets. A public release is public distribution.
- Decide whether the release should use your published OAuth registration or
  whether installers must create their own. Including a registration reference
  does not make it usable by every tenant or grant GitHub access.
- Commit the workflow and intended plugin changes before tagging. Only the
  source at the tag is packaged; uncommitted changes are not included.

For example, if the manifest version is `1.1.0`, run these commands from a
checkout at the intended release commit, after confirming that `v1.1.0` does
not already exist:

```sh
git tag -a v1.1.0 -m "GitHub for Cowork 1.1.0"
git push origin v1.1.0
```

For later releases, update the manifest's app `version`, commit the changes,
and use a new matching tag. Do not change `manifestVersion` just to create a
release, and do not move or force-push published release tags.

You can also trigger the same workflow manually instead of pushing a tag: open
the repository's **Actions** tab, select **Release plugin package**, click
**Run workflow**, and enter the existing tag (for example `v1.1.0`) in the
`tag` input. The tag must already exist and still be pushed first — manual
dispatch does not create the tag, it only re-runs the build and publish steps
against it. This is useful for retrying a run after fixing the workflow file
itself, without creating a new tag.

Open the repository's **Actions** tab to monitor **Release plugin package**.
After it succeeds, open **Releases** and download `github-for-cowork.zip`
from the release's **Assets**. GitHub's automatically generated **Source code**
archives contain the repository and are not the installable plugin package.

No personal access token or additional repository secret is required by this
workflow: it uses the job's automatically supplied `GITHUB_TOKEN`.
It creates a new release and deliberately does not overwrite an existing one.
If a run reports that the release already exists, inspect that release and its
assets before retrying; use a new version/tag for a changed package.
Creating this workflow does not itself create a tag or publish a release.

## Troubleshooting

| Symptom | Check |
| --- | --- |
| Redirect URI mismatch | GitHub must allow the exact `https://teams.microsoft.com/api/platform/v1.0/oAuthRedirect` callback |
| Client authentication/token exchange fails | Client ID and secret must come from the same OAuth app; verify the secret and request-body authentication method |
| Registration unavailable to the plugin | Check Microsoft 365 tenant restrictions, the app-ID binding, and the full copied registration ID |
| Sign-in works but requests return 401/403 | Check token validity, scopes, GitHub permissions, organization approval/SSO, Base URL, and refresh configuration |
| Public repositories work but private ones do not | `public_repo` does not include private access; evaluate `repo` only if needed, plus organization authorization |
| "All repos" is incomplete | Verify repository-enumeration tools and authorization coverage; a broader scope alone cannot add a missing MCP capability |
| Changed registration has no effect after upload | Inspect the manifest inside the rebuilt ZIP and make sure the updated package was installed |
| Access fails after token expiration | Check the refresh endpoint and host refresh support; initial successful sign-in does not prove refresh works |

When reducing previously granted scopes, changing the portal field alone may
not narrow existing tokens. Revoke the old GitHub authorization and authorize
again with the intended scope, understanding that this disconnects the affected
authorization until sign-in is completed again.

For secret rotation, create a replacement GitHub client secret, update the
portal securely, verify the connection, and retire the old secret. Never commit
either secret. If a secret is exposed, revoke it promptly and replace it.

## References

- [Create a GitHub OAuth app](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/creating-an-oauth-app)
- [GitHub OAuth authorization, PKCE, and token refresh](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/authorizing-oauth-apps)
- [GitHub OAuth scopes](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/scopes-for-oauth-apps)
- [Configure OAuth in Teams Developer Portal](https://learn.microsoft.com/en-us/microsoftteams/platform/messaging-extensions/api-based-oauth#configure-oauth-in-developer-portal)
- [Register MCP servers as agent connectors](https://learn.microsoft.com/en-us/microsoftteams/platform/m365-apps/agent-connectors)
- [Build plugins for Copilot Cowork](https://learn.microsoft.com/microsoft-365/copilot/cowork/cowork-plugin-development)
