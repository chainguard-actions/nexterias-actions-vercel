<!-- markdownlint-disable -->

# Hardening Report: nexterias--actions-vercel/v2.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **nexterias--actions-vercel/v2.0.0** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in .github/workflows/ci.yml are pinned to mutable version tags instead of full 40-character commit SHAs. Unpinned actions can be silently updated to include malicious code. Failing references: `pnpm/action-setup@v2`, `actions/setup-node@v6`, `actions/upload-artifact@v7`, `actions/download-artifact@v8`, `kenji-miyake/setup-git-cliff@v2`, `actions/github-script@v9`, `softprops/action-gh-release@v3`.

Locations:

- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:30`
- `.github/workflows/ci.yml:40`
- `.github/workflows/ci.yml:60`
- `.github/workflows/ci.yml:63`
- `.github/workflows/ci.yml:67`
- `.github/workflows/ci.yml:89`
- `.github/workflows/ci.yml:93`
- `.github/workflows/ci.yml:97`
- `.github/workflows/ci.yml:102`
- `.github/workflows/ci.yml:107`

### unpinned-uses (severity: high)

Multiple `uses:` references in .github/workflows/codeql.yml are pinned to mutable version tags instead of full 40-character commit SHAs. Failing references: `actions/checkout@v6`, `github/codeql-action/init@v4`, `github/codeql-action/autobuild@v4`, `github/codeql-action/analyze@v4`.

Locations:

- `.github/workflows/codeql.yml:43`
- `.github/workflows/codeql.yml:47`
- `.github/workflows/codeql.yml:55`
- `.github/workflows/codeql.yml:68`

### unpinned-uses (severity: high)

Multiple `uses:` references in .github/workflows/dependency-review.yml are pinned to mutable version tags instead of full 40-character commit SHAs. Failing references: `actions/checkout@v6`, `actions/dependency-review-action@v4`.

Locations:

- `.github/workflows/dependency-review.yml:12`
- `.github/workflows/dependency-review.yml:14`

### script-injection (severity: high)

Sub-rule (a): Direct `${{ }}` expression interpolation inside `run:` shell commands. (1) The 'deploy' job echoes step outputs directly into the shell: `echo ${{ steps.vercel.outputs.deployment-url }}` and `echo ${{ steps.vercel.outputs.deployment-status }}` — these are unquoted expressions expanded by the YAML template engine before the shell sees them, enabling shell metacharacter injection. (2) The 'tag' job interpolates `${{ github.ref }}` directly into a shell command: `echo "name=$(echo '${{ github.ref }}' | sed ...)"` and `git tag -f ${{ steps.tag.outputs.name }} ${{ github.ref }}` — both allow injection via a crafted ref name.

Locations:

- `.github/workflows/ci.yml:79`
- `.github/workflows/ci.yml:120`
- `.github/workflows/ci.yml:127`

### github-env-injection (severity: high)

The 'Get target tag' step in the 'tag' job writes a value derived from `${{ github.ref }}` (an untrusted input) directly to `$GITHUB_OUTPUT` without sanitization (`printf '%s' ... | tr -d '\n\r'`). A crafted ref name containing newlines could inject arbitrary key-value pairs into the output file, potentially poisoning subsequent steps. Offending line: `run: echo "name=$(echo '${{ github.ref }}' | sed -r 's/refs\/tags\/(v[0-9]+)\..*/\1/')" >> $GITHUB_OUTPUT`

Locations:

- `.github/workflows/ci.yml:120`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection

**Notes:**

Fixed all findings across three workflow files:

**ci.yml**:
- Pinned pnpm/action-setup@v2 → @eae0cfeb286e66ffb5155f1a79b90583a127a68b
- Pinned actions/setup-node@v6 → @249970729cb0ef3589644e2896645e5dc5ba9c38
- Pinned actions/upload-artifact@v7 → @043fb46d1a93c77aae656e7c1c64a875d1fc6a0a
- Pinned actions/download-artifact@v8 → @3e5f45b2cfb9172054b4087a40e8e0b5a5461e7c
- Pinned kenji-miyake/setup-git-cliff@v2 → @2778609c643a39a2576c4bae2e493b855eb4aee8
- Pinned actions/github-script@v9 → @3a2844b7e9c422d3c10d287c895573f7108da1b3
- Pinned softprops/action-gh-release@v3 → @3d0d9888cb7fd7b750713d6e236d1fcb99157228
- Fixed script-injection: moved deployment-url and deployment-status outputs into env block
- Fixed script-injection: moved github.ref and steps.tag.outputs.name into env blocks in tag job
- Fixed github-env-injection: sanitized GITHUB_REF with tr -d '\n\r' before writing to GITHUB_OUTPUT

**codeql.yml**:
- Pinned actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803
- Pinned github/codeql-action/init@v4 → @e4fba868fa4b1b91e1fdab776edc8cfbe6e9fb81
- Pinned github/codeql-action/autobuild@v4 → @e4fba868fa4b1b91e1fdab776edc8cfbe6e9fb81
- Pinned github/codeql-action/analyze@v4 → @e4fba868fa4b1b91e1fdab776edc8cfbe6e9fb81

**dependency-review.yml**:
- Pinned actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803
- Pinned actions/dependency-review-action@v4 → @2031cfc080254a8a887f58cffee85186f0e49e48

