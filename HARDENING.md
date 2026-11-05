<!-- markdownlint-disable -->

# Hardening Report: jlumbroso--free-disk-space/v1.3.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jlumbroso--free-disk-space/v1.3.2** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 5d4fdf9..cd54385 100644
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
@@ -19,34 +17,46 @@ inputs:
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
     - shell: bash
       env:
         ANDROID: ${{ inputs.android }}
@@ -56,200 +66,4 @@ runs:
         DOCKER_IMAGES: ${{ inputs.docker-images }}
         TOOL_CACHE: ${{ inputs.tool-cache }}
         SWAP_STORAGE: ${{ inputs.swap-storage }}
-      run: |
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
-        if [[ "$ANDROID" == 'true' ]]; then
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
-        if [[ "$DOTNET" == 'true' ]]; then
-          BEFORE=$(getAvailableSpace)
-
-          # https://github.community/t/bigger-githu
... (truncated)
```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Notes:**

I made no changes because hardened/action/action.yml no longer needs this fix. All seven inputs are already passed to the composite step through env: (ANDROID, DOTNET, HASKELL, LARGE_PACKAGES, DOCKER_IMAGES, TOOL_CACHE, SWAP_STORAGE) and used quoted in bash conditionals. The run: script has no ${{ }} expressions, no uses: references to pin, no writes to GITHUB_ENV/GITHUB_OUTPUT/GITHUB_PATH, and nothing downloaded from the internet. The visible part of the earlier patch only removed blank lines and moved the run: body out of the file (its diff was cut off), which doesn't affect security. Repeating it would add churn and could break the action.

