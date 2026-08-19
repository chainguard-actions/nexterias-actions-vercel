<!-- markdownlint-disable -->

# Hardening Report: nexterias--actions-vercel/v1.2.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **nexterias--actions-vercel/v1.2.1** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in ci.yml use mutable tag-based refs instead of full 40-character SHA digests, making the workflow vulnerable to supply-chain attacks. Unpinned references: `pnpm/action-setup@v2` (lines 27, 63, 93), `actions/setup-node@v4` (lines 30, 65, 95), `actions/upload-artifact@v4` (line 41), `actions/download-artifact@v4` (line 68), `kenji-miyake/setup-git-cliff@v2` (line 99), `actions/github-script@v7` (line 101), `softprops/action-gh-release@v2` (line 108).

Locations:

- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:30`
- `.github/workflows/ci.yml:41`
- `.github/workflows/ci.yml:63`
- `.github/workflows/ci.yml:65`
- `.github/workflows/ci.yml:68`
- `.github/workflows/ci.yml:93`
- `.github/workflows/ci.yml:95`
- `.github/workflows/ci.yml:99`
- `.github/workflows/ci.yml:101`
- `.github/workflows/ci.yml:108`

### unpinned-uses (severity: high)

Multiple `uses:` references in codeql.yml use mutable tag-based refs instead of full 40-character SHA digests. Unpinned references: `actions/checkout@v4` (line 45), `github/codeql-action/init@v3` (line 49), `github/codeql-action/autobuild@v3` (line 62), `github/codeql-action/analyze@v3` (line 72).

Locations:

- `.github/workflows/codeql.yml:45`
- `.github/workflows/codeql.yml:49`
- `.github/workflows/codeql.yml:62`
- `.github/workflows/codeql.yml:72`

### unpinned-uses (severity: high)

Multiple `uses:` references in dependency-review.yml use mutable tag-based refs instead of full 40-character SHA digests. Unpinned references: `actions/checkout@v4` (line 13), `actions/dependency-review-action@v4` (line 15).

Locations:

- `.github/workflows/dependency-review.yml:13`
- `.github/workflows/dependency-review.yml:15`

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation inside `run:` shell commands. In the `deploy` job, `${{ steps.vercel.outputs.deployment-url }}` and `${{ steps.vercel.outputs.deployment-status }}` are interpolated directly into `echo` commands without quoting or env-var indirection: `echo ${{ steps.vercel.outputs.deployment-url }}` and `echo ${{ steps.vercel.outputs.deployment-status }}`. An attacker-controlled deployment URL or status value could inject shell metacharacters.

Locations:

- `.github/workflows/ci.yml:76`
- `.github/workflows/ci.yml:77`

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation inside `run:` shell commands in the `tag` job. `${{ github.ref }}` is interpolated directly into a shell command: `echo "name=$(echo '${{ github.ref }}' | sed -r 's/refs\/tags\/(v[0-9]+)\..*/\1/')" >> $GITHUB_OUTPUT`. Additionally, `${{ steps.tag.outputs.name }}` and `${{ github.ref }}` are interpolated directly into `git tag -f ${{ steps.tag.outputs.name }} ${{ github.ref }}` and `git push -f --tags origin ${{ steps.tag.outputs.name }}`. These allow injection of arbitrary shell commands via a crafted ref or tag output.

Locations:

- `.github/workflows/ci.yml:121`
- `.github/workflows/ci.yml:126`
- `.github/workflows/ci.yml:127`

### github-env-injection (severity: high)

In the `tag` job's 'Get target tag' step, `${{ github.ref }}` (an untrusted input) is written directly to `$GITHUB_OUTPUT` without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). The offending line is: `echo "name=$(echo '${{ github.ref }}' | sed -r 's/refs\/tags\/(v[0-9]+)\..*/\1/')" >> $GITHUB_OUTPUT`. A newline embedded in the ref value could inject additional key=value pairs into GITHUB_OUTPUT, potentially overwriting subsequent step outputs.

Locations:

- `.github/workflows/ci.yml:121`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection

**Notes:**

Fixed all findings across three workflow files:

**ci.yml**:
- Pinned pnpm/action-setup@v2 → @eae0cfeb286e66ffb5155f1a79b90583a127a68b
- Pinned actions/setup-node@v4 → @49933ea5288caeca8642d1e84afbd3f7d6820020
- Pinned actions/upload-artifact@v4 → @ea165f8d65b6e75b540449e92b4886f43607fa02
- Pinned actions/download-artifact@v4 → @d3f86a106a0bac45b974a628896c90dbdf5c8093
- Pinned kenji-miyake/setup-git-cliff@v2 → @2778609c643a39a2576c4bae2e493b855eb4aee8
- Pinned actions/github-script@v7 → @f28e40c7f34bde8b3046d885e986cb6290c5673b
- Pinned softprops/action-gh-release@v2 → @3bb12739c298aeb8a4eeaf626c5b8d85266b0e65
- Fixed script-injection in deploy job: moved deployment-url and deployment-status expressions to env: block
- Fixed script-injection in tag job: moved github.ref and steps.tag.outputs.name to env: blocks
- Fixed github-env-injection: sanitized github.ref with printf/tr before writing to GITHUB_OUTPUT

**codeql.yml**:
- Pinned actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262
- Pinned github/codeql-action/init@v3 → @4187e74d05793876e9989daffde9c3e66b4acd07
- Pinned github/codeql-action/autobuild@v3 → @4187e74d05793876e9989daffde9c3e66b4acd07
- Pinned github/codeql-action/analyze@v3 → @4187e74d05793876e9989daffde9c3e66b4acd07

**dependency-review.yml**:
- Pinned actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262
- Pinned actions/dependency-review-action@v4 → @2031cfc080254a8a887f58cffee85186f0e49e48

