<!-- markdownlint-disable -->

# Hardening Report: jlumbroso--free-disk-space/v1.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **jlumbroso--free-disk-space/v1.0.0** was hardened automatically. 8 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Five `${{ inputs.* }}` expressions are directly interpolated inside a `run:` shell command string in action.yml. GitHub Actions performs template substitution before the shell executes the script, so a calling workflow can supply a value containing shell metacharacters (`;`, `|`, `$(...)`, etc.) that break out of the `[[ ... ]]` conditional and execute arbitrary commands. Offending lines:
- Line 107: `if [[ ${{ inputs.android }} == 'true' ]]; then`
- Line 117: `if [[ ${{ inputs.dotnet }} == 'true' ]]; then`
- Line 128: `if [[ ${{ inputs.haskell }} == 'true' ]]; then`
- Line 138: `if [[ ${{ inputs.large-packages }} == 'true' ]]; then`
- Line 153: `if [[ ${{ inputs.swap-storage }} == 'true' ]]; then`

Fix: move each input into an `env:` variable and reference it as a quoted shell variable, e.g. `if [[ "$ANDROID" == 'true' ]]; then`.

Locations:

- `action.yml:107`
- `action.yml:117`
- `action.yml:128`
- `action.yml:138`
- `action.yml:153`

### unpinned-uses (severity: high)

The workflow file references `jlumbroso/free-disk-space@main`, which uses a mutable branch name (`main`) instead of a full 40-character commit SHA. If the referenced repository is compromised or the branch is force-pushed, the action will silently execute attacker-controlled code. Pin to a specific commit SHA, e.g. `jlumbroso/free-disk-space@<40-char-sha> # main`.

Locations:

- `.github/workflows/test.yml:10`

### missing-permissions (severity: medium)

The workflow file `.github/workflows/test.yml` has no top-level `permissions:` key and the single job `free-disk-space` also has no `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (e.g. `write` on contents). Add a minimal `permissions:` block such as `permissions: {}` or `permissions: read-all` at the top level, then grant only the specific scopes required.

Locations:

- `.github/workflows/test.yml:1`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.android }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:121`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dotnet }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:133`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.haskell }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:146`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.large-packages }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:159`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.swap-storage }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:178`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses, missing-permissions

**Notes:**

1. action.yml: Added an `env:` block to the composite step mapping all five inputs to environment variables (INPUT_ANDROID, INPUT_DOTNET, INPUT_HASKELL, INPUT_LARGE_PACKAGES, INPUT_SWAP_STORAGE). Replaced all five `${{ inputs.* }}` expressions in the run: block with quoted shell variable references (e.g., `[[ "$INPUT_ANDROID" == 'true' ]]`), eliminating script injection risk.
2. .github/workflows/test.yml: Pinned `jlumbroso/free-disk-space@main` to the full commit SHA `54081f138730dfa15788a46383842cd2f914a1be` with a `# main` comment for readability.
3. .github/workflows/test.yml: Added `permissions: {}` at both the top-level workflow and job level to enforce least-privilege token access.

