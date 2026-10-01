---
name: feedback_yes_no_menu_labels
description: "Label yes/no menu items exactly 'Yes' and 'No' (Yes first); context goes in the prompt, not the labels."
metadata:
  node_type: memory
  type: feedback
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

For a yes/no menu, label the two items exactly `"Yes"` and `"No"`, with **Yes first**. Put all the context in the question/prompt line, never in the option labels.

**Always capitalized (dev, 2026-09-30):** "Yes" and "No" with a capital first letter everywhere — never lowercase "yes"/"no" as a label.

**Use `yes_no()` for every yes or no question (dev, 2026-09-30).** `bool yes_no(string question, bool start_focused = false, bool music_added = false)` in `deps/custom_menu.nvgt` adds "Yes"/"No" with ids, checks by id, speaks "canceled" on No/Escape, and returns true only for Yes. All 13 prompts use it (profile confirm, 3 settings resets, new-game overwrite with music, quest reroll, prestige, 5 minigame bet confirms with start_focused). Never hand-build a Yes/No menu again; don't speak "canceled" at the call site (the function does). The four-option quit prompt is not a yes/no question and doesn't use it.

**Why:** Short, predictable labels are fast to hear and consistent across the game; a screen-reader player learns "Yes is first." Verbose labels ("Yes, delete my save") slow every read.

**How to apply:** Question line carries the meaning ("Are you sure you want to delete this save?"); the two items are just Yes / No. Cancel/escape still speaks "canceled" ([[feedback_menus_say_canceled]]).
