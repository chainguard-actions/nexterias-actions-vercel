<!-- markdownlint-disable -->

# Hardening Report: nexterias--actions-vercel/v2.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **nexterias--actions-vercel/v2.1.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): Direct ${{ }} expression interpolation inside run: shell commands. In the 'deploy' job, `echo ${{ steps.vercel.outputs.deployment-url }}` and `echo ${{ steps.vercel.outputs.deployment-status }}` interpolate step outputs directly into the shell. In the 'tag' job 'Get target tag' step, `${{ github.ref }}` is interpolated directly into the shell command. In the 'tag' job 'Update & Push tag' step, `${{ steps.tag.outputs.name }}` and `${{ github.ref }}` are interpolated directly into git commands. All of these bypass shell quoting and allow an attacker-controlled value to inject shell metacharacters.

Locations:

- `.github/workflows/ci.yml:79`
- `.github/workflows/ci.yml:80`
- `.github/workflows/ci.yml:107`
- `.github/workflows/ci.yml:112`
- `.github/workflows/ci.yml:113`

### github-env-injection (severity: high)

The 'Get target tag' step in the 'tag' job writes `${{ github.ref }}` (processed through sed) directly to $GITHUB_OUTPUT without sanitization (no `printf '%s' ... | tr -d '\n\r'` step). A ref containing newline characters could inject arbitrary key=value pairs into the GitHub output environment file: `echo "name=$(echo '${{ github.ref }}' | sed -r 's/refs\/tags\/(v[0-9]+)\..*/\1/')" >> $GITHUB_OUTPUT`

Locations:

- `.github/workflows/ci.yml:107`

### missing-permissions (severity: medium)

The workflow file cleanup-deployments.yml has no top-level `permissions:` key and the only job ('cleanup') also has no job-level `permissions:` key. This means the workflow runs with the default (potentially broad) token permissions. A minimal permissions block (e.g., `permissions: {}` or specific scopes) should be added.

Locations:

- `.github/workflows/cleanup-deployments.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, github-env-injection, missing-permissions

**Notes:**

ci.yml: (1) Lines 79-80: moved `steps.vercel.outputs.deployment-url` and `steps.vercel.outputs.deployment-status` into an `env:` block and referenced as `$DEPLOYMENT_URL`/`$DEPLOYMENT_STATUS` in the run script. (2) Line 107 (Get target tag): moved `github.ref` into `env: GITHUB_REF`, used `printf '%s'` piped through `sed` then `tr -d '\n\r'` before writing to `$GITHUB_OUTPUT` — fixes both script-injection and github-env-injection. (3) Lines 112-113 (Update & Push tag): moved `steps.tag.outputs.name` and `github.ref` into `env: TAG_NAME`/`GITHUB_REF` and referenced as double-quoted shell variables in the git commands. cleanup-deployments.yml: Added `permissions: {}` at the workflow top level to restrict the default GitHub token to no permissions.

