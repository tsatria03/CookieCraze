---
name: feedback_menus_say_canceled
description: "Every menu/input escape, Back, or No path speaks 'canceled' for audible feedback."
metadata:
  node_type: memory
  type: feedback
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

Every menu or input-box path that backs out — escape, a Back option, or a No answer — must speak `"canceled"` so the player gets audible confirmation that nothing happened.

**Why:** Without a spoken cue, a screen-reader player can't tell whether an escape registered or the game hung. "canceled" closes the loop.

**Exception (dev, 2026-09-30):** cancelling a store quantity box or a name box keeps its own error message ("Error! Please enter a valid number…" and the name-invalid message) instead of "canceled" — the dev prefers it. Don't change those.

**Exception (dev, 2026-10-28): a Back item in a NESTED menu does not say "canceled".** Choosing Back inside a submenu (store categories, quests, slot manager submenus, and the like) just returns to the parent menu silently — that is navigation, not cancelling. "canceled" is for backing out of an action or a top-level menu: Escape, No on a confirmation, and leaving a top-level menu. Don't add "canceled" to nested Back items or flag them.

**How to apply:** When adding a menu or input flow, wire `speak("canceled")` (or the game's equivalent) into every cancel/back/No branch. Related UI conventions: [[feedback_yes_no_menu_labels]].
