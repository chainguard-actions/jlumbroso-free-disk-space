<!-- markdownlint-disable -->

# Hardening Report: jlumbroso--free-disk-space/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jlumbroso--free-disk-space/v1.2.0** was hardened automatically. 9 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Six `${{ inputs.* }}` expressions are directly interpolated inside a `run:` shell block in action.yml. The YAML template engine substitutes these values before the shell parses the command, so an attacker-controlled input value can break out of the `[[ ... == 'true' ]]` comparison context and inject arbitrary shell commands. Offending lines:
- `if [[ ${{ inputs.android }} == 'true' ]];` (line 119)
- `if [[ ${{ inputs.dotnet }} == 'true' ]];` (line 130)
- `if [[ ${{ inputs.haskell }} == 'true' ]];` (line 141)
- `if [[ ${{ inputs.large-packages }} == 'true' ]];` (line 152)
- `if [[ ${{ inputs.tool-cache }} == 'true' ]];` (line 165)
- `if [[ ${{ inputs.swap-storage }} == 'true' ]];` (line 176)
Fix: move each input into an `env:` variable and reference it as a quoted shell variable, e.g. `if [[ "$ANDROID" == 'true' ]];`.

Locations:

- `action.yml:119`
- `action.yml:130`
- `action.yml:141`
- `action.yml:152`
- `action.yml:165`
- `action.yml:176`

### unpinned-uses (severity: high)

The workflow uses `jlumbroso/free-disk-space@main`, which is a mutable branch reference rather than an immutable 40-character hex commit SHA. A supply-chain attacker who can push to that branch can change what code runs in this workflow. Pin to a full SHA, e.g. `uses: jlumbroso/free-disk-space@<40-char-sha> # main`.

Locations:

- `.github/workflows/test.yml:10`

### missing-permissions (severity: medium)

The workflow file test.yml has no top-level `permissions:` key and the job `free-disk-space` also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the repository default (often `write-all` for private repos or `read-all` for public repos), granting more access than necessary. Add a minimal `permissions:` block, e.g. `permissions: {}` or `permissions: read-all` at the top level, and restrict further at the job level.

Locations:

- `.github/workflows/test.yml:1`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.android }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:128`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dotnet }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:140`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.haskell }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:153`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.large-packages }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:166`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.tool-cache }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:186`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.swap-storage }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:198`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all findings in action.yml and .github/workflows/test.yml:
1. script-injection / static-inline-injection (action.yml): Added an `env:` block to the composite action step mapping all six inputs (android, dotnet, haskell, large-packages, tool-cache, swap-storage) to environment variables (INPUT_ANDROID, INPUT_DOTNET, INPUT_HASKELL, INPUT_LARGE_PACKAGES, INPUT_TOOL_CACHE, INPUT_SWAP_STORAGE). Replaced all six `${{ inputs.* }}` interpolations in the `run:` shell block with quoted `"$INPUT_*"` variable references.
2. unpinned-uses (.github/workflows/test.yml): Pinned `jlumbroso/free-disk-space@main` to the full commit SHA `54081f138730dfa15788a46383842cd2f914a1be` with a `# main` comment.
3. missing-permissions (.github/workflows/test.yml): Added `permissions: {}` at both the top-level workflow and job level to enforce least-privilege.

