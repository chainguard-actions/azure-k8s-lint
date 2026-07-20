<!-- markdownlint-disable -->

# Hardening Report: azure--k8s-lint/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **azure--k8s-lint/v3.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions by mutable tags instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks if the tag is moved. Failing references:
- default-labels.yml: `actions/stale@v3` (used twice)
- integration-tests.yml: `actions/checkout@v1`
- prettify-code.yml: `actions/checkout@v2`, `actionsx/prettier@v2`
- release-pr.yml: `Azure/action-release-workflows/.github/workflows/release_js_project.yaml@v1`
- unit-tests.yml: `actions/checkout@v1`

Locations:

- `.github/workflows/default-labels.yml:15`
- `.github/workflows/default-labels.yml:25`
- `.github/workflows/integration-tests.yml:22`
- `.github/workflows/prettify-code.yml:13`
- `.github/workflows/prettify-code.yml:16`
- `.github/workflows/release-pr.yml:13`
- `.github/workflows/unit-tests.yml:16`

### missing-permissions (severity: medium)

Four workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. Without explicit permissions, workflows run with the default (potentially broad) token permissions. Affected files: default-labels.yml, integration-tests.yml, prettify-code.yml, unit-tests.yml.

Locations:

- `.github/workflows/default-labels.yml:1`
- `.github/workflows/integration-tests.yml:1`
- `.github/workflows/prettify-code.yml:1`
- `.github/workflows/unit-tests.yml:1`

### script-injection (severity: high)

Rule (b) violation in integration-tests.yml: The env var `PR_BASE_REF` is set from `${{ github.event.pull_request.base.ref }}` (attacker-controllable via a PR) and then used **unquoted** inside a `run:` block in two places: `echo $PR_BASE_REF` and `if [[ $PR_BASE_REF != releases/* ]]`. Unquoted shell expansion allows an attacker to inject shell metacharacters (`;`, `|`, `&`, glob chars, etc.) through a crafted branch name. Both expansions must be double-quoted: `echo "$PR_BASE_REF"` and `if [[ "$PR_BASE_REF" != releases/* ]]`.

Locations:

- `.github/workflows/integration-tests.yml:25`
- `.github/workflows/integration-tests.yml:26`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all findings across 5 workflow files: (1) Pinned all mutable action tags to full 40-char commit SHAs with tag comments preserved — actions/stale@v3 (×2), actions/checkout@v1 (×2), actions/checkout@v2, actionsx/prettier@v2, and Azure/action-release-workflows@v1. (2) Added `permissions: {}` top-level block to default-labels.yml, integration-tests.yml, prettify-code.yml, and unit-tests.yml. (3) Fixed script injection in integration-tests.yml by double-quoting both `$PR_BASE_REF` expansions in the run block.

