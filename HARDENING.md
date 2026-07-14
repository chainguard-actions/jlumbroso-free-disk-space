<!-- markdownlint-disable -->

# Hardening Report: jlumbroso--free-disk-space/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **jlumbroso--free-disk-space/v1.2.0** was hardened automatically. 9 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Six `${{ inputs.* }}` expressions are interpolated directly inside a `run:` shell command string in action.yml. The inputs `android`, `dotnet`, `haskell`, `large-packages`, `tool-cache`, and `swap-storage` are each used in `if [[ ${{ inputs.X }} == 'true' ]]` conditions. Because YAML template substitution happens before the shell sees the string, a caller supplying a crafted value (e.g. `true ]]; malicious_command; if [[ true`) can inject arbitrary shell commands. All six occurrences are in the same composite-action `run:` step. The fix is to map each input to an env var and reference the env var (double-quoted) inside the shell script instead.

Locations:

- `action.yml:113`
- `action.yml:123`
- `action.yml:133`
- `action.yml:144`
- `action.yml:158`
- `action.yml:167`

### unpinned-uses (severity: high)

The workflow references `jlumbroso/free-disk-space@main` — a mutable branch name rather than a pinned 40-character commit SHA. If the referenced repository is compromised or the branch is force-pushed, the workflow will silently execute attacker-controlled code. Pin to a full SHA, e.g. `jlumbroso/free-disk-space@<40-char-sha> # main`.

Locations:

- `.github/workflows/test.yml:10`

### missing-permissions (severity: medium)

The workflow file `.github/workflows/test.yml` has no top-level `permissions:` key and no job-level `permissions:` key on the `free-disk-space` job. Without an explicit permissions block the job inherits the repository's default token permissions, which may be broader than necessary. Add a `permissions: {}` block (or the minimal required scopes) at the top level or on the job.

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

Fixed all findings in action.yml and .github/workflows/test.yml: (1) Moved all six ${{ inputs.* }} expressions from the run: shell script into an env: block on the step, replacing them with double-quoted $VAR_NAME references in the shell to prevent script injection. (2) Pinned jlumbroso/free-disk-space@main to full SHA 54081f138730dfa15788a46383842cd2f914a1be with a # main comment. (3) Added permissions: {} at the top level of test.yml.

