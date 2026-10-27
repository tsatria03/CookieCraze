---
name: feedback_settings_vs_tables
description: "main.table holds plain numbers only (no true/false). Player-taste on/off switches go in the in-game settings; design switches that change what the game is stay in the config tables."
metadata:
  node_type: memory
  type: feedback
---

**main.table holds pure numbers only — no bools (dev, 2026-10-27).** Money, counts, multipliers, chances. If a new main.table idea is an on/off, it doesn't go there.

**Where an on/off switch goes — ask who it's for:**
- **Player taste** (doesn't change production or rules, only what they hear/see, e.g. repeat combo announcements, intro/exit sounds) → a checkbox in the in-game game settings (`gamsetsmenu`, settings_menu.nvgt), saved in preffs, with a sensible default.
- **Design/mechanic** decided by the modder for everyone (e.g. `enabled=true:90` in combos.table, which turns the whole combo system on and sets its unlock rank) → stays in that system's config table.
- A switch that lets a player skip part of the game is a cheat — neither place; it was removed outright (the baker event on/off checkboxes, 7.2, [[project_main_table_plan]]).

**Why:** The dev wants main.table to read as a clean balance sheet and wants each player to control their own sound/speech preferences. Agreed while planning [[project_combo_repeat_plan]].

**How to apply:** When proposing any new setting, place it by this test and say which bucket it's in. Settings-checkbox labels follow the existing "Enable <verb>ing … ." style (capital, full stop).
