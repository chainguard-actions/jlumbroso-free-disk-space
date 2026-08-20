<!-- markdownlint-disable -->

# Hardening Report: jlumbroso--free-disk-space/v1.3.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jlumbroso--free-disk-space/v1.3.1** was hardened automatically. 10 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

All 7 `inputs.*` values are directly interpolated via `${{ inputs.* }}` expressions inside the single `run:` shell block in action.yml (rule a). Before the shell executes, GitHub Actions substitutes the raw input string into the script text, allowing a caller to inject arbitrary shell metacharacters. Affected lines use patterns like: `if [[ ${{ inputs.android }} == 'true' ]]; then`, `if [[ ${{ inputs.dotnet }} == 'true' ]]; then`, `if [[ ${{ inputs.haskell }} == 'true' ]]; then`, `if [[ ${{ inputs.large-packages }} == 'true' ]]; then`, `if [[ ${{ inputs.docker-images }} == 'true' ]]; then`, `if [[ ${{ inputs.tool-cache }} == 'true' ]]; then`, and `if [[ ${{ inputs.swap-storage }} == 'true' ]]; then`. Fix: move each input into an `env:` variable and reference it as a quoted shell variable, e.g. `if [[ "$INPUT_ANDROID" == 'true' ]]; then`.

Locations:

- `action.yml:113`
- `action.yml:124`
- `action.yml:135`
- `action.yml:147`
- `action.yml:185`
- `action.yml:197`
- `action.yml:210`

### unpinned-uses (severity: high)

The workflow references `jlumbroso/free-disk-space@main`, which is a mutable branch name rather than an immutable 40-character commit SHA. A compromised or force-pushed branch could silently replace the action with malicious code. Pin to a full SHA, e.g. `jlumbroso/free-disk-space@<40-char-sha> # main`.

Locations:

- `.github/workflows/test.yml:10`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and the only job (`free-disk-space`) also has no job-level `permissions:` key. Without explicit permissions, the job inherits the repository default (often `write` for the GITHUB_TOKEN), granting broader access than necessary. Add a minimal `permissions:` block, e.g. `permissions: {}` or `permissions: contents: read`.

Locations:

- `.github/workflows/test.yml:1`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.android }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:133`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dotnet }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:145`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.haskell }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:158`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.large-packages }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:172`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.docker-images }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:194`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.tool-cache }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:207`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.swap-storage }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:219`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all 10 findings across 2 files: (1) In action.yml, added an env: block to the composite step mapping all 7 inputs (android, dotnet, haskell, large-packages, docker-images, tool-cache, swap-storage) to environment variables (INPUT_ANDROID, INPUT_DOTNET, INPUT_HASKELL, INPUT_LARGE_PACKAGES, INPUT_DOCKER_IMAGES, INPUT_TOOL_CACHE, INPUT_SWAP_STORAGE), and replaced all 7 `${{ inputs.* }}` inline expressions in the run: block with quoted `"$INPUT_*"` shell variable references. (2) In .github/workflows/test.yml, pinned `jlumbroso/free-disk-space@main` to the full commit SHA `54081f138730dfa15788a46383842cd2f914a1be # main`, and added `permissions: {}` at the workflow top level to enforce least-privilege.

