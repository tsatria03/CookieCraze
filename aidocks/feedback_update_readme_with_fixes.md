---
name: feedback_update_readme_with_fixes
description: "Always update cycrz/docks/readme.txt where a fix or change affects anything it describes (keys, readouts, settings, mechanics), in the same pass as the changelog + todo updates."
metadata:
  node_type: memory
  type: feedback
---

Whenever a fix or feature changes something `cycrz/docks/readme.txt` describes — a key, a spoken readout, a setting, a mechanic, a config field — update the readme too, in the same pass as the changelog entry and todo-list move (i.e. once the dev confirms the fix works, per [[feedback_docks_last]]). Search the readme for every place the changed thing is mentioned (keys list, feature sections, modder reference), not just the obvious line, and fix nearby inaccuracies about the same feature.

**Why:** the dev said (2026-10-25) "always update the readme.txt file where possible", after the 7.1 F/J readout fix shipped a changelog entry while the readme still described the old behavior ("per bake when baking mode is active"). Players read the readme with a screen reader as the game's manual, so stale lines mislead them.

**How to apply:** don't ask whether to update the readme — just do it with the other docks, and list what changed in the summary. Keep dock lines ≤ 1024 chars ([[feedback_dock_line_length_1024]]), plain text, and keep the file's CRLF endings ([[feedback_no_crlf_normalization]]). Related: [[feedback_changelog_rules]], [[feedback_todo_list_format]].
