---
name: project_gitbash_crlf_gotcha
description: "In this Git Bash, `sed -i` silently rewrites a CRLF file as LF, and `grep -c $'\\r$'` counts every line as CRLF even when it isn't — so neither can edit or verify line endings. Use the Edit tool for edits and Python byte counts (or the Grep tool) for checks."
metadata:
  node_type: memory
  type: project
---

Found 2026-10-25 while building the quest baseline plan, section 2.

- **`sed -i` strips CR.** Running `sed -i 's/…/…/'` in the Bash tool rewrote `cycrz.nvgt`, `extrafuncts.nvgt`, `menu.nvgt` and `quests_table.nvgt` from CRLF to plain LF (menu.nvgt first lost CRLF in the rank-burst section 1 "rank <" spacing fix). Every other tracked file stayed CRLF.
- **`grep -c $'\r$' file` is useless here.** On a probe file with one CRLF line and one LF line it reported 2 of 2. All "crlf=N total=N" checks made with it earlier in that session proved nothing.
- **What works:**
  - Edits: use the Edit tool (it keeps each file's endings) — never `sed -i` on project files. For multi-line moves, a Python script that reads/writes bytes and keeps `\r\n` (like the readme reorganization) is fine.
  - Checks: count bytes in Python — `b.count(b'\r\n')` vs `b.count(b'\n') - b.count(b'\r\n')` (bare LF) — or use the Grep tool with pattern `[^\r]\n`, multiline, as [[feedback_dock_checks_use_grep]] says.
  - To scan everything: loop `git ls-files` through the Python byte count and print files with any bare LF.
- **Repair (done once):** the four files were converted back to CRLF with a Python byte replace, after asserting they had no CRLF at all. `git diff` was unaffected (git compares normalized content; `.gitattributes` pins `*.nvgt` to CRLF — [[project_repo_hygiene]]). This restores a file's own original ending, so it doesn't break [[feedback_no_crlf_normalization]].
