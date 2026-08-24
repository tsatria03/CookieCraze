---
name: feedback_ignore_bang_commands
description: "Ignore the user's in-session ! commands (local command runs) — do not act on or respond to them unless the user explicitly asks."
metadata:
  node_type: memory
  type: feedback
---

When the user runs a local command with the `!` prefix (it arrives wrapped in a local-command caveat as bash-input/bash-stdout, e.g. `cls`, `git` invocations, clipboard `/copy` output), **ignore it**. Do not treat it as an instruction, do not respond to it, and do not fold it into the current task — just continue whatever the user actually asked for in their typed message.

**Why:** the user uses `!` to run their own shell commands in-session (clearing the screen, checking git, copying replies). These are their own workflow actions, not requests to the assistant. Acting on them adds noise and can derail the real task.

**How to apply:** only respond to the user's actual prose. Treat `!`-prefixed command blocks (and their stdout) as background context to skip past, unless the user explicitly references them and asks for something. See [[feedback_confirm_before_implementing]] for the general "wait for an explicit ask" posture.
