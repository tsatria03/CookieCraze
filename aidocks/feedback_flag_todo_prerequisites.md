---
name: feedback_flag_todo_prerequisites
description: "The dev picks todo-list items in any order, not top to bottom; before starting one, say if it has a prerequisite (another item or a decision) that must come first."
metadata:
  node_type: memory
  type: feedback
---

The dev works through `cycrz/docks/todo_list.txt` by picking items **at random**, not in the listed priority order (dev, 2026-10-25). When they pick one, check first whether it depends on something else — another todo item, an unmade design decision, or code that a different item restructures — and **say so before starting**, naming the prerequisite and why.

Known dependencies (as of 2026-10-25):
- Readme "prestige keeps all minigame stats" wording → after the endless-quests-after-prestige fix (wording depends on which stats prestige resets) — exploit #8 in [[project_bugs_exploits_balance]].
- Rank items (all ranks at once + summary, consistent milestones, 21M and 2B overflows) share the rank-up code; merging the two copies is step one of the all-at-once item, so ideally do that first or batch them — code #11 in [[project_bugs_code]].
- Prestige spam and store prices after losses need the dev's design decisions first ([[project_design_review_7_0]]).

**Why:** the todo order mixes urgency with dependencies; picking at random could start an item whose wording or code is about to change.

**How to apply:** at the start of each picked item, one or two lines: "no prerequisites" or "needs X first because Y" — then wait if it's blocked. Related: [[feedback_confirm_before_implementing]], [[feedback_pause_after_each_fix]].
