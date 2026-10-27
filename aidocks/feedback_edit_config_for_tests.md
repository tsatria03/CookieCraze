---
name: feedback_edit_config_for_tests
description: "When a test step needs a config value changed (events/stores/tables), make the edit yourself and revert it as soon as the dev says that step passed."
metadata:
  node_type: memory
  type: feedback
---

When a test step needs a temporary change to a config file under `cycrz/data/decompiled/config/` (an `.event`, `.store` or `.table`), make the edit yourself before giving the step, instead of asking the dev to type it. Then **put it back the moment the dev says that step passed** ("past", "passed", "works"), before giving the next step, and say that you reverted it.

**Why:** The dev asked for this (2026-10-27) after several steps where they had to hand-edit main.table, quests.table and ranks.table themselves. It saves them editor round-trips, and reverting right away keeps test values from leaking into a commit.

**How to apply:**
- Edit with Python byte-level replaces that keep CRLF (never `sed -i` — [[project_gitbash_crlf_gotcha]]); assert the target text occurs exactly once.
- In the step, say which file and value you changed and that they need Control L (or a relaunch) to pick it up.
- On "passed", restore the original exact text and confirm. If a later step needs a different value, revert first, then make the new edit.
- Only skip this when the edit can't be done from here (e.g. something in AppData) — then give the dev the exact text to type.
- Fits with [[feedback_test_steps_one_at_a_time]] and [[feedback_check_git_log_for_commits]] (no git checks during step testing).
