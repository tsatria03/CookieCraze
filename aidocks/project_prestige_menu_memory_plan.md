---
name: project_prestige_menu_memory_plan
description: "7.6 change PLANNED (2026-10-29), first in 7.6 before quest rewards: the prestige store remembers your menu position like the cookie store's shops (globals prestige_last_cat_id / prestige_last_item_id; falls back to the same position when a bought standard upgrade disappears). Second part: hide prestige categories once everything in them is bought. DONE: built, tested, documented (7.6 entries 1-2)."
metadata:
  node_type: memory
  type: project
---

# Prestige store remembers its menu position (7.6)

**Dev (2026-10-29):** "all of the shops in the cookie store remember your menu position; I wish the prestige store also remembered it." Asked to check the cookie store first.

## How the cookie store does it (verified 2026-10-29)

- Globals in dec.nvgt (~240-248) hold the id of the last item chosen per menu: baker_last_cat_id / baker_last_item_id (singles), bundle_last_cat_id / bundle_last_item_id, buymenu_last_id, shopmenu_last_id (achievements have achievements_cat_last_id / achievements_last_id). Boost shop and lottery keep locals while you stay inside them.
- On open: `int start_pos = 0; if (x_last_id != "") { int found = m.find_item_by_id(x_last_id); if (found != -1) start_pos = found; } int mres = m.run(start_pos, true);` then `if (mres != -1) x_last_id = sel;` and Back/Escape clears it (`x_last_id = "";`) before returning to the parent, so the parent still remembers and the child starts at the top next time.
- Lasts for the session only (not saved).

## Prestige store today

menus/menu.nvgt: prestige_store_menu() (~1033) and prestige_store_category_menu(items, chosenMenu) (~1124) both `m.run(0, true)`. Item ids are "cat"+index / "locked"+index (categories) and "item"+index / "locked"+index (items), index into the parsed prestige.store list (stable while the file is unchanged). Every purchase, error, or locked message calls prestige_store_category_menu again, so focus jumps to the top each time.

## Plan

- New globals prestige_last_cat_id and prestige_last_item_id in dec.nvgt beside the shop ones.
- Both prestige menus use the cookie store pattern: start on the remembered id, remember the choice, Back/Escape clears its own id.
- **Difference:** buying a standard (one-time) upgrade removes it from the list, so its id is gone; then start at the same position it had (also remembered, clamped to the list), landing on the item that took its place (usually the next tier) instead of the top. Endless upgrades never disappear, so they land on themselves.
- Session only, like the cookie store.
- Tests (1 step): buy an upgrade and check where the menu lands; go back and check the category is remembered.
- Docs: 7.6 opens with this entry (`New in 7.6.`, build/version.txt to 7.6); readme prestige store section mention.

## Second part: hide emptied categories (dev, 2026-10-29)

Dev: Standard Passive and Standard Head Start should be removed once you buy everything in them; today they stay as "Contains 0 out of 0 purchasable items." In prestige_store_menu() (~1077) an unlocked category is always added; its totalItems already excludes owned items and hidden-locked items. **Plan:** skip the category when totalItems == 0, so any emptied category disappears (Standard ones once fully bought; Endless never empty). The whole-store "You have purchased every available upgrade" message already covers the case where nothing is left. The remembered category falls back to the same position when its category vanished. Readme: the prestige store paragraph saying "entering that category shows a message instead of an empty menu" changes to say the category is removed. Tests: add to the single step (a fully bought Standard category is not listed).

## Build log

- **BUILT and the single step PASSED 2026-10-29 (4 parts):** globals prestige_last_cat_id/_pos, prestige_last_item_id/_pos (dec.nvgt); prestige_menu_start(lastId, lastPos) in menu.nvgt (find by id, else same 0-based position clamped to the item before back; run() returns 1-based so pos = mres - 1); both prestige menus remember and Back clears; categories with totalItems == 0 skipped. Tests: only Endless categories listed; focus stays on the bought upgrade; category list remembers Endless Passive; leaving via Back resets to the top.
- **Added during testing (dev):** the store intro no longer reads the points (now "Prestige store. Please choose a category to browse."; readme now points to P). Also the M key's cointoss.ogg removed from announce_money (dev deleted the file; it lives in sounds/unused now) and the unused coinslot global removed — its own changelog entry at the dev's request (7.6 entry 3).
- **Docs DONE:** 7.6 block opened (build/version.txt 7.6): entry 1 the empty-category fix, entry 2 the menu memory + intro change; readme prestige store paragraphs updated.
