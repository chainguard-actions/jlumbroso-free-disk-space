<!-- markdownlint-disable -->

# Hardening Report: jlumbroso--free-disk-space/v1.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jlumbroso--free-disk-space/v1.0.0** was hardened automatically. 8 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Five `${{ inputs.* }}` expressions are interpolated directly inside the `run:` shell block in action.yml. The values are substituted into `if [[ ${{ inputs.X }} == 'true' ]]` conditions before the shell ever sees them, allowing a caller to inject arbitrary shell commands via any of these inputs. Offending lines:
- `if [[ ${{ inputs.android }} == 'true' ]]; then`
- `if [[ ${{ inputs.dotnet }} == 'true' ]]; then`
- `if [[ ${{ inputs.haskell }} == 'true' ]]; then`
- `if [[ ${{ inputs.large-packages }} == 'true' ]]; then`
- `if [[ ${{ inputs.swap-storage }} == 'true' ]]; then`
Fix: move each input into an `env:` variable and reference it as a quoted shell variable, e.g. `if [[ "$INPUT_ANDROID" == 'true' ]]; then`.

Locations:

- `action.yml:113`
- `action.yml:124`
- `action.yml:135`
- `action.yml:146`
- `action.yml:162`

### unpinned-uses (severity: high)

The workflow uses `jlumbroso/free-disk-space@main`, which references a mutable branch name (`main`) rather than a full 40-character commit SHA. This means the action code can change at any time without notice, enabling a supply-chain attack. Pin to a specific commit SHA, e.g. `jlumbroso/free-disk-space@<40-char-sha> # main`.

Locations:

- `.github/workflows/test.yml:10`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and the single job `free-disk-space` also has no `permissions:` key. Without explicit permissions, the workflow inherits the repository default (typically `contents: write` for push events), granting broader access than necessary. Add a top-level `permissions: {}` block or restrict to the minimum required scopes.

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

Fixed all findings in action.yml and .github/workflows/test.yml:
1. script-injection / static-inline-injection (action.yml): Added an `env:` block to the composite action step mapping all five inputs (android, dotnet, haskell, large-packages, swap-storage) to environment variables (INPUT_ANDROID, INPUT_DOTNET, INPUT_HASKELL, INPUT_LARGE_PACKAGES, INPUT_SWAP_STORAGE). Replaced all five `${{ inputs.* }}` interpolations in the `run:` block with quoted shell variable references (e.g. `"$INPUT_ANDROID"`).
2. unpinned-uses (test.yml): Pinned `jlumbroso/free-disk-space@main` to the full commit SHA `54081f138730dfa15788a46383842cd2f914a1be` with a `# main` comment.
3. missing-permissions (test.yml): Added `permissions: {}` at both the top-level workflow and the job level to enforce least-privilege access.

