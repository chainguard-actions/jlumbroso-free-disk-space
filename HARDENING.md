<!-- markdownlint-disable -->

# Hardening Report: jlumbroso--free-disk-space/v1.3.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **jlumbroso--free-disk-space/v1.3.1** was hardened automatically. 8 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The single `run:` block in action.yml directly interpolates seven `${{ inputs.* }}` expressions inside shell `if [[ ... ]]` conditions without routing them through env vars. Before the shell ever sees the command, GitHub Actions performs YAML template substitution, inserting the raw input value into the shell string. A caller supplying a crafted value (e.g., `true ]]; then malicious_cmd; if [[ true`) can break out of the condition and execute arbitrary commands. Affected lines: `if [[ ${{ inputs.android }} == 'true' ]]`, `if [[ ${{ inputs.dotnet }} == 'true' ]]`, `if [[ ${{ inputs.haskell }} == 'true' ]]`, `if [[ ${{ inputs.large-packages }} == 'true' ]]`, `if [[ ${{ inputs.docker-images }} == 'true' ]]`, `if [[ ${{ inputs.tool-cache }} == 'true' ]]`, `if [[ ${{ inputs.swap-storage }} == 'true' ]]`. Fix: move each input into an `env:` variable and reference the env var (double-quoted) in the shell condition instead.

Locations:

- `action.yml:113`
- `action.yml:122`
- `action.yml:132`
- `action.yml:143`
- `action.yml:163`
- `action.yml:172`
- `action.yml:181`

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

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Added an env: block to the single composite step in action.yml mapping all seven inputs to environment variables (INPUT_ANDROID, INPUT_DOTNET, INPUT_HASKELL, INPUT_LARGE_PACKAGES, INPUT_DOCKER_IMAGES, INPUT_TOOL_CACHE, INPUT_SWAP_STORAGE). Replaced all seven ${{ inputs.* }} expressions in the run: shell script with double-quoted env var references (e.g., "$INPUT_ANDROID"), eliminating the script injection vulnerability where crafted input values could break out of the [[ ... ]] conditions and execute arbitrary commands.

