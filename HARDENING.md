<!-- markdownlint-disable -->

# Hardening Report: peter-evans--create-or-update-project-card/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **peter-evans--create-or-update-project-card/v3.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references across workflow files use mutable version tags instead of pinned 40-character SHA commit hashes, making the workflows vulnerable to supply-chain attacks if those tags are moved. Failing references include: actions/checkout@v4, actions/setup-node@v4, actions/upload-artifact@v4, actions/download-artifact@v4, actions/github-script@v7, peter-evans/create-pull-request@v5, peter-evans/enable-pull-request-automerge@v3, peter-evans/slash-command-dispatch@v3.

Locations:

- `.github/workflows/ci.yml:22`
- `.github/workflows/ci.yml:23`
- `.github/workflows/ci.yml:29`
- `.github/workflows/ci.yml:30`
- `.github/workflows/ci.yml:38`
- `.github/workflows/ci.yml:42`
- `.github/workflows/ci.yml:45`
- `.github/workflows/ci.yml:75`
- `.github/workflows/ci.yml:97`
- `.github/workflows/ci.yml:113`
- `.github/workflows/ci.yml:131`
- `.github/workflows/ci.yml:152`
- `.github/workflows/ci.yml:168`
- `.github/workflows/ci.yml:184`
- `.github/workflows/ci.yml:200`
- `.github/workflows/ci.yml:213`
- `.github/workflows/ci.yml:220`
- `.github/workflows/ci.yml:224`
- `.github/workflows/automerge-dependabot.yml:8`
- `.github/workflows/slash-command-dispatch.yml:8`
- `.github/workflows/update-major-version.yml:16`

### script-injection (severity: high)

Sub-rule (a): `update-major-version.yml` directly interpolates `${{ github.event.inputs.main_version }}` and `${{ github.event.inputs.target }}` inside `run:` shell commands. These are workflow_dispatch inputs that can be controlled by whoever triggers the workflow. If the values contain shell metacharacters, they will be interpreted by the shell before quoting can occur. Offending lines: `run: git tag -f ${{ github.event.inputs.main_version }} ${{ github.event.inputs.target }}` and `run: git push origin ${{ github.event.inputs.main_version }} --force`.

Locations:

- `.github/workflows/update-major-version.yml:24`
- `.github/workflows/update-major-version.yml:26`

### missing-permissions (severity: medium)

The following workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the GITHUB_TOKEN is granted its default (potentially broad) permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/automerge-dependabot.yml:1`
- `.github/workflows/slash-command-dispatch.yml:1`
- `.github/workflows/update-major-version.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed all findings across 4 workflow files:

1. **unpinned-uses** (ci.yml, automerge-dependabot.yml, slash-command-dispatch.yml, update-major-version.yml): Pinned all `uses:` references to full 40-char SHAs with tag comments: actions/checkout@v4→11d5960a, actions/setup-node@v4→49933ea5, actions/upload-artifact@v4→ea165f8d, actions/download-artifact@v4→d3f86a10, actions/github-script@v7→f28e40c7, peter-evans/create-pull-request@v5→4e1beaa7, peter-evans/enable-pull-request-automerge@v3→a660677d, peter-evans/slash-command-dispatch@v3→f996d7b7.

2. **script-injection** (update-major-version.yml): Moved `${{ github.event.inputs.main_version }}` and `${{ github.event.inputs.target }}` out of `run:` shell strings into `env:` blocks, referenced as double-quoted `"$MAIN_VERSION"` and `"$TARGET"` in the shell commands.

3. **missing-permissions** (automerge-dependabot.yml, slash-command-dispatch.yml, update-major-version.yml): Added minimal `permissions:` blocks — automerge-dependabot gets `pull-requests: write, contents: write`; slash-command-dispatch gets `issues: read, pull-requests: read`; update-major-version gets `contents: write`.

