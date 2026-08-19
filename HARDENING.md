<!-- markdownlint-disable -->

# Hardening Report: nexterias--actions-vercel/v1.2.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **nexterias--actions-vercel/v1.2.3** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` blocks in ci.yml directly interpolate `${{ }}` expressions inside shell commands, violating rule (a). This allows expression values to be parsed as shell code before the shell ever sees them.

1. (line 79–80) `echo ${{ steps.vercel.outputs.deployment-url }}` and `echo ${{ steps.vercel.outputs.deployment-status }}` — step outputs interpolated directly into shell.
2. (line 128) `run: echo "name=$(echo '${{ github.ref }}' | sed -r ...)" >> $GITHUB_OUTPUT` — `github.ref` interpolated directly into a shell command.
3. (lines 133–134) `git tag -f ${{ steps.tag.outputs.name }} ${{ github.ref }}` — both a step output and `github.ref` interpolated directly as shell arguments.

Locations:

- `.github/workflows/ci.yml:79`
- `.github/workflows/ci.yml:80`
- `.github/workflows/ci.yml:128`
- `.github/workflows/ci.yml:133`

### github-env-injection (severity: high)

In the `tag` job of ci.yml, the value of `${{ github.ref }}` is written to `$GITHUB_OUTPUT` without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). An attacker-controlled ref containing newlines could inject arbitrary key-value pairs into the output environment.

Offending line: `run: echo "name=$(echo '${{ github.ref }}' | sed -r 's/refs\/tags\/(v[0-9]+)\..*/\1/')" >> $GITHUB_OUTPUT`

Locations:

- `.github/workflows/ci.yml:128`

### unpinned-uses (severity: high)

Multiple `uses:` references in workflow files are pinned to mutable tags or version strings rather than immutable 40-character commit SHAs, making them vulnerable to supply-chain attacks if the referenced tag is moved.

**.github/workflows/ci.yml** unpinned references:
- `pnpm/action-setup@v2` (lines 29, 64, 97)
- `actions/setup-node@v4` (lines 33, 68, 101)
- `actions/upload-artifact@v4` (line 41)
- `actions/download-artifact@v4` (line 71)
- `kenji-miyake/setup-git-cliff@v2` (line 100)
- `actions/github-script@v7` (line 103)
- `softprops/action-gh-release@v2` (line 110)

**.github/workflows/codeql.yml** unpinned references:
- `actions/checkout@v4` (line 43)
- `github/codeql-action/init@v3` (line 47)
- `github/codeql-action/autobuild@v3` (line 57)
- `github/codeql-action/analyze@v3` (line 65)

**.github/workflows/dependency-review.yml** unpinned references:
- `actions/checkout@v4` (line 13)
- `actions/dependency-review-action@v4` (line 15)

Locations:

- `.github/workflows/ci.yml:29`
- `.github/workflows/ci.yml:33`
- `.github/workflows/ci.yml:41`
- `.github/workflows/ci.yml:71`
- `.github/workflows/ci.yml:100`
- `.github/workflows/ci.yml:103`
- `.github/workflows/ci.yml:110`
- `.github/workflows/codeql.yml:43`
- `.github/workflows/codeql.yml:47`
- `.github/workflows/codeql.yml:57`
- `.github/workflows/codeql.yml:65`
- `.github/workflows/dependency-review.yml:13`
- `.github/workflows/dependency-review.yml:15`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, github-env-injection, unpinned-uses

**Notes:**

Fixed all three findings across ci.yml, codeql.yml, and dependency-review.yml:

1. script-injection: Moved all ${{ }} expressions out of run: shell strings into env: blocks. Deployment outputs (deployment-url, deployment-status) moved to DEPLOYMENT_URL/DEPLOYMENT_STATUS env vars. github.ref moved to GITHUB_REF env var. steps.tag.outputs.name moved to TAG_NAME env var. All referenced as plain shell variables.

2. github-env-injection: The github.ref value is now sanitized with `printf '%s' "$GITHUB_REF" | tr -d '\n\r'` before being processed by sed and before writing to $GITHUB_OUTPUT.

3. unpinned-uses: All 13 unpinned action references pinned to full 40-character commit SHAs with mutable tag preserved in a comment: pnpm/action-setup@v2, actions/setup-node@v4, actions/upload-artifact@v4, actions/download-artifact@v4, kenji-miyake/setup-git-cliff@v2, actions/github-script@v7, softprops/action-gh-release@v2, actions/checkout@v4 (codeql.yml and dependency-review.yml), github/codeql-action/init@v3, github/codeql-action/autobuild@v3, github/codeql-action/analyze@v3, actions/dependency-review-action@v4.

