<!-- markdownlint-disable -->

# Hardening Report: azure--k8s-lint/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **azure--k8s-lint/v3.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable tags instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks if the tag is moved. Failing references:
- .github/workflows/default-labels.yml: `actions/stale@v3` (×2)
- .github/workflows/integration-tests.yml: `actions/checkout@v1`
- .github/workflows/prettify-code.yml: `actions/checkout@v2`, `actionsx/prettier@v2`
- .github/workflows/release-pr.yml: `Azure/action-release-workflows/.github/workflows/release_js_project.yaml@v1`
- .github/workflows/unit-tests.yml: `actions/checkout@v1`

Locations:

- `.github/workflows/default-labels.yml:17`
- `.github/workflows/default-labels.yml:29`
- `.github/workflows/integration-tests.yml:22`
- `.github/workflows/prettify-code.yml:11`
- `.github/workflows/prettify-code.yml:14`
- `.github/workflows/release-pr.yml:13`
- `.github/workflows/unit-tests.yml:13`

### missing-permissions (severity: medium)

Four workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. Without explicit permissions, the GITHUB_TOKEN is granted its default (often broad) permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/default-labels.yml:1`
- `.github/workflows/integration-tests.yml:1`
- `.github/workflows/prettify-code.yml:1`
- `.github/workflows/unit-tests.yml:1`

### script-injection (severity: high)

Rule (b) violation in .github/workflows/integration-tests.yml: The job-level env var `PR_BASE_REF` is set to `${{ github.event.pull_request.base.ref }}` (attacker-controlled via a pull_request event) and then expanded unquoted inside a `run:` block — `echo $PR_BASE_REF` and `if [[ $PR_BASE_REF != releases/*`. An unquoted shell expansion allows shell metacharacters in the value to be interpreted by the shell, enabling command injection. The fix is to double-quote all expansions: `echo "$PR_BASE_REF"` and `if [[ "$PR_BASE_REF" != releases/* ]]`.

Locations:

- `.github/workflows/integration-tests.yml:25`
- `.github/workflows/integration-tests.yml:26`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all findings across 5 workflow files:

1. unpinned-uses: Pinned all mutable action tags to full commit SHAs with tag comments preserved:
   - actions/stale@v3 → @98ed4cb500039dbcccf4bd9bedada4d0187f2757 (default-labels.yml ×2)
   - actions/checkout@v1 → @50fbc622fc4ef5163becd7fab6573eac35f8462e (integration-tests.yml, unit-tests.yml)
   - actions/checkout@v2 → @ee0669bd1cc54295c223e0bb666b733df41de1c5 (prettify-code.yml)
   - actionsx/prettier@v2 → @8b6d14bd1241e743fa9a6112b43d691d1890a8e9 (prettify-code.yml)
   - Azure/action-release-workflows@v1 → @3c677ba5ab58f5c5c1a6f0cfb176b333b1f27405 (release-pr.yml)

2. missing-permissions: Added `permissions: {}` top-level block to default-labels.yml, integration-tests.yml, prettify-code.yml, and unit-tests.yml. release-pr.yml already had job-level permissions (actions: read, contents: write) and was not in the missing-permissions finding.

3. script-injection: In integration-tests.yml, quoted all expansions of PR_BASE_REF in the run block: `echo "$PR_BASE_REF"` and `if [[ "$PR_BASE_REF" != releases/* ]]` to prevent shell metacharacter interpretation.

