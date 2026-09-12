---
name: github-dependency-audit
description: >-
  Review repository dependencies for outdated packages, version drift, known
  security issues, and upgrade risk. Use when the user asks for a dependency
  audit, package-health review, upgrade-risk assessment, or maintenance check.
  Produce a concise report with affected packages, risk level, evidence, and
  recommended next actions. If the user requests a report, send the final
  summary to the signed-in user through Copilot Cowork and Outlook with
  responsive, Outlook-safe HTML/CSS.
compatibility: Requires repository context, package-manager metadata, and access to the target dependency files or package registry information.
---

# Dependency Audit

Use this skill when the user wants a dependency review rather than a general code
review. The goal is to identify drift, stale packages, and upgrade risk without
making unsupported claims.

## Scope and evidence

Resolve the target repository and package manager first. Prefer the actual lock
file and manifest that are in scope for the repository being reviewed:

- `package.json` / `package-lock.json` or `pnpm-lock.yaml` / `yarn.lock`
- `requirements.txt`, `poetry.lock`, or `pyproject.toml`
- `go.mod` / `go.sum`
- `Cargo.toml` / `Cargo.lock`
- `pom.xml` / `gradle` files

If the repository is ambiguous, ask one narrow question before proceeding.
Do not infer package status from a single file when the workspace has multiple
package managers or multiple service roots.

## What to assess

For each dependency, check whether it is:

- Outdated relative to the current supported range or target baseline
- Affected by a known vulnerability or deprecation note
- Blocking a critical upgrade path or compatibility change
- Repeated across multiple services or workspaces, increasing drift risk

Prefer evidence from the repository itself and the package manager metadata.
When a package is too new, not exposed, or not subject to the same support model,
label the status as `unknown` instead of guessing.

## Output format

Open with scope, repository, package manager, and the time observed. Then give a
brief executive summary and a table of findings.

```markdown
## Dependency Audit

Scope: owner/repo • npm • 12 dependencies reviewed

### Summary
- 3 packages need review
- 1 high-risk upgrade
- 1 package is behind the supported range

| Package | Current | Latest | Risk | Evidence | Recommendation |
| --- | --- | --- | --- | --- | --- |
| example-lib | 1.2.0 | 2.1.0 | High | breaking changes in release notes | upgrade in a dedicated PR |
| safe-lib | 3.4.0 | 3.5.1 | Low | patch release only | update when next sprint allows |
```

Use a consistent format and keep the row count manageable. Mark missing evidence
as `Unknown` rather than inventing a conclusion.

## Report requirement

When the user requests a report or shareable summary, send the final result to the
signed-in user via Copilot Cowork and Outlook. The email body should use
responsive, Outlook-safe HTML/CSS, with clear headings, a compact table layout,
and readable mobile behavior on desktop, web, tablet, and mobile clients.

## Boundaries

This skill is read-only. It does not update package manifests, push changes, or
approve dependency upgrades without the user's explicit action. When a risky
upgrade is found, suggest the next step and the likely impact, but do not make
changes on the user's behalf.
