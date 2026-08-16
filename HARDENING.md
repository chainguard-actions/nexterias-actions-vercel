<!-- markdownlint-disable -->

# Hardening Report: nexterias--actions-vercel/v2.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **nexterias--actions-vercel/v2.1.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Two `${{ ... }}` expressions are directly interpolated inside a `run:` shell command block in the `deploy` job. The lines `echo ${{ steps.vercel.outputs.deployment-url }}` and `echo ${{ steps.vercel.outputs.deployment-status }}` pass step output values through YAML template substitution before the shell ever sees them. If the Vercel deployment URL or status contains shell metacharacters (e.g. from a branch name or PR title embedded in the URL), this could lead to command injection. These values should be passed via `env:` variables and referenced as double-quoted shell variables (e.g. `echo "$DEPLOYMENT_URL"`) instead.

Locations:

- `.github/workflows/ci.yml:75`
- `.github/workflows/ci.yml:76`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in `.github/workflows/ci.yml` at lines 75-76. Moved `${{ steps.vercel.outputs.deployment-url }}` and `${{ steps.vercel.outputs.deployment-status }}` from direct interpolation inside the `run:` block into an `env:` block as `DEPLOYMENT_URL` and `DEPLOYMENT_STATUS`. The shell script now safely references them as double-quoted environment variables (`echo "$DEPLOYMENT_URL"` and `echo "$DEPLOYMENT_STATUS"`), eliminating the risk of command injection from attacker-controlled values embedded in deployment URLs or status strings.

