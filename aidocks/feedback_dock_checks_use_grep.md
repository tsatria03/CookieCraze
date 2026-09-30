---
name: feedback_dock_checks_use_grep
description: "Verify dock line length and CRLF endings with the Grep tool (no permission prompt), not a PowerShell command the dev has to approve."
metadata:
  node_type: memory
  type: feedback
---

After editing anything in `cycrz/docks/`, verify it with the **Grep tool**, not PowerShell/Bash:
- **Line length ([[feedback_dock_line_length_1024]]):** pattern `.{1025,}` on `cycrz/docks`, `output_mode: count` — 0 matches means every line is 1024 chars or less.
- **CRLF endings ([[feedback_no_crlf_normalization]]):** pattern `[^\r]\n` with `multiline: true`, `output_mode: count` — 0 matches means no bare LF.

**Why:** the dev asked (2026-09-30) not to be prompted for these routine checks; a PowerShell `ReadAllText` one-liner needed approval every time, while Grep runs without asking.

**How to apply:** use these two Grep calls after every dock edit and report the result in one line; don't reach for a shell command for them.
