<!-- markdownlint-disable -->

# Hardening Report: azure--k8s-lint/v4.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **azure--k8s-lint/v4.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses a reusable workflow reference pinned to a mutable tag (`@v1`) rather than a full 40-character commit SHA. This means the referenced workflow could be silently updated to a different (potentially malicious) version without any change to this file. Failing reference: `Azure/action-release-workflows/.github/workflows/release_js_project.yaml@v1`

Locations:

- `.github/workflows/release-pr.yml:13`

### script-injection (severity: high)

Rule (b) violation: The env var `PR_BASE_REF` is populated from `${{ github.event.pull_request.base.ref }}` (attacker-controllable via a pull request) and then expanded **unquoted** in two shell commands inside a `run:` block: `echo $PR_BASE_REF` and `if [[ $PR_BASE_REF != releases/* ]]`. An unquoted shell expansion allows shell metacharacters (`;`, `|`, `&`, glob chars, whitespace) embedded in the branch name to be interpreted by the shell, enabling command injection. Both expansions must be double-quoted: `echo "$PR_BASE_REF"` and `if [[ "$PR_BASE_REF" != releases/* ]]`.

Locations:

- `.github/workflows/integration-tests.yml:28`
- `.github/workflows/integration-tests.yml:29`

### missing-permissions (severity: medium)

The workflow file `unit-tests.yml` has no top-level `permissions:` key and its only job (`build`) also has no job-level `permissions:` key. Without explicit permissions, the job runs with the default token permissions, which may include write access to repository contents and other scopes depending on the organisation's settings. A minimal `permissions: contents: read` (or more restrictive) block should be added.

Locations:

- `.github/workflows/unit-tests.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

1. release-pr.yml: Pinned Azure/action-release-workflows reusable workflow from mutable @v1 tag to full SHA @3c677ba5ab58f5c5c1a6f0cfb176b333b1f27405 # v1. 2. integration-tests.yml: Double-quoted both unquoted $PR_BASE_REF shell expansions (echo "$PR_BASE_REF" and if [["$PR_BASE_REF" != releases/*]]) to prevent shell metacharacter injection. 3. unit-tests.yml: Added top-level `permissions: contents: read` block to enforce least-privilege token access.

