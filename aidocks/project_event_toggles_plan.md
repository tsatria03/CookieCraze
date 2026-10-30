---
name: project_event_toggles_plan
description: "7.6 change DONE (2026-10-30): bring back the two game settings checkboxes that switch baker events off, removed in 7.2 (commit a813ec0) as cheat-like; some users missed them. main.table stays untouched. DONE (7.6 entry 7)."
metadata:
  node_type: memory
  type: project
---

# Bring back the baker event checkboxes (7.6)

**Dev (2026-10-30):** "Is it possible to bring back the global check boxes for turning on and off baking events? ... Some users did not like the fact that it was removed. You can leave main.table alone for this one." This reverses part of 7.2's main.table move ([[project_main_table_plan]]), which called switching events off a cheat; the event chances stay in main.table.

## What 7.2 removed (git show a813ec0)

- dec.nvgt: `bool playervents = true;` (auto baking) and `bool playervents2 = true;` (manual baking).
- settings_menu.nvgt (game settings, right after the auto bake sound checkbox): `form.create_checkbox("Enable player events during auto baking.", playervents)` and `("Enable player events during manual baking.", playervents2)`; read back with form.is_checked on Save; the Cancel snapshot setsnap_playervents / setsnap_playervents2.
- savefuncts.nvgt: preffs keys "playervents" / "playervents2" (st.add / st.read_bool) and `playervents=true; playervents2=true;` in reset_game_settings.
- baker_events.nvgt: `if (!playervents) return;` in attempt_autobaker_event and `if (!playervents2) return;` in attempt_manebaker_event.
- game.nvgt: the call sites were guarded too (`if(playervents2==true) attempt_manebaker_event(evchanse2);`); now a comment says main.table's chance of 0 is the only off switch.

## Plan

- Restore all of the above with the same names, wording, placement, and preffs keys, default on. Guard inside the two attempt_ functions only (one place each), and replace the game.nvgt comment.
- Old preffs files from before 7.2 may still hold a false value under those keys; it will be honoured again (the player's old choice). Files saved since 7.2 lack the keys, so they default to on.
- Cookie flipper events are not affected (separate flipper.event and the flipper's own event list).
- Tests (1 step): turn auto off and check no events fire while auto baking; same for manual; Cancel undoes a change.
- Docs: readme (the game settings / random events text that says events cannot be switched off; the 7.2-era note pointing to F3), one 7.6 changelog entry (7 of 10), memory.

## Build log

- **BUILT, the single step PASSED, docs DONE 2026-10-30:** restored as planned (guards inside attempt_autobaker_event / attempt_manebaker_event only; game.nvgt comment updated; settings_menu comment updated). Tests: manual off = no events in ~150 presses; auto off = none while auto baking; Cancel undid an uncheck. Readme random events paragraph (was "cannot be switched off") rewritten; 7.6 changelog entry 7 of 10.
