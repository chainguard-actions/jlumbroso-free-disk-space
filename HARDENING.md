<!-- markdownlint-disable -->

# Hardening Report: jlumbroso--free-disk-space/v2.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jlumbroso--free-disk-space/v2.0.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index ed4b963..838f873 100644
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
@@ -19,40 +17,51 @@ inputs:
     description: "Remove Haskell runtime"
     required: false
     default: "true"
-
   # option inspired by:
   # https://github.com/apache/flink/blob/master/tools/azure-pipelines/free_disk_space.sh
   large-packages:
     description: "Remove large misc. packages — includes azure-cli, Chrome, Firefox, PowerShell, mono, and the llvm-, php-, mongodb-, mysql-, dotnet- and aspnetcore- package families. Respects subject toggles: `dotnet: false` exempts .NET from this sweep."
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
   preinstalled-runtimes:
     description: "Remove pre-installed runtimes (Node, Go, Python, Ruby). ⚠️ Workflows using actions/setup-node, setup-python, setup-go or setup-ruby will re-download their runtime, and a pinned version may not be available."
     required: false
     default: "false"
-
   # DEPRECATED alias, kept so no existing workflow breaks at v2.0.0.
   tool-cache:
     description: "DEPRECATED — renamed to `preinstalled-runtimes`. Still honoured, and will keep working until v3.0.0; it prints a warning when used."
     required: false
     default: "false"
-
   swap-storage:
     description: "Remove swap storage. ⚠️ Jobs that need swap may be killed under memory pressure, with no error mentioning this action — see #12. Off by default for that reason."
     required: false
     default: "false"
-
 runs:
   using: "composite"
   steps:
     - shell: bash
       env:
         ANDROID: ${{ inputs.android }}
@@ -63,224 +72,4 @@ runs:
         PREINSTALLED_RUNTIMES: ${{ inputs.preinstalled-runtimes }}
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
-     
... (truncated)
```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Notes:**

No changes made. I checked action.yml again: the composite step takes every input (android, dotnet, haskell, large-packages, docker-images, preinstalled-runtimes, tool-cache, swap-storage) through env: and reads it as a quoted shell variable. There is no ${{ }} in run:, no unpinned uses:, no curl|sh, no credentials and no writes to GITHUB_OUTPUT, GITHUB_ENV or GITHUB_PATH. The visible part of the previous patch removed blank lines and replaced the inline script. The diff is cut off, so what replaced the script can't be seen, but the security issue it would have addressed (inputs reaching the shell directly) isn't in the current code. Re-applying the patch would only change the file's structure.

