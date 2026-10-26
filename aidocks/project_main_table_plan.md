---
name: project_main_table_plan
description: "PLAN (7.2, agreed 2026-10-26, all decisions locked, not built): new data/config/tables/main.table (sections [starting_values] [slots] [ranks] [events] [selling], every value falls back to a default) holding the 8 game-settings sliders that were too easy to change (slot cost multipliers, rank/experience modifiers, event chances, sell price) plus 6 hard-coded starting values; sliders removed from the menu and no longer saved in gamesets.crz. Prerequisite for the prestige-spam fix (min_rank 100). 3 sections, docs last."
metadata:
  node_type: memory
  type: project
---

# main.table plan (7.2)

Dev idea (2026-10-26): settings a normal player has no reason to change, which the game settings menu exposed as sliders — effectively cheats (design review #5 in [[project_design_review_7_0]]) — move into a new modder-only config file. One section per turn, dev tests between sections, docs last ([[feedback_docks_last]]). Re-locate code by symbol. This also unblocks the prestige-spam fix: with the experience modifier out of the menu, a min_rank of 100 can't be trivialised ([[project_bugs_exploits_balance]] #7).

## Locked decisions

- **File:** `cycrz/data/config/tables/main.table`, `key=value` lines like the other tables ([[project_data_driven_config]]), comments with `;`. **Sections (dev approved 2026-10-26), in this order:** `[starting_values]` (starting_coins, starting_autocookie, starting_manualcookie), `[slots]` (each base cost next to its multiplier: automation_base_cost, automation_cost_multiplier, auto_slot_base_cost, auto_slot_cost_multiplier, manual_slot_base_cost, manual_slot_cost_multiplier), `[ranks]` (rank_modifier, experience_modifier), `[events]` (auto_event_chance, manual_event_chance), `[selling]` (sell_price). No section is required. Keys and shipped values (today's defaults, so behavior is unchanged):
  - `automation_cost_multiplier=1.01` (was the Automation slot Cost Multiplier slider, `automationMultiplier`)
  - `auto_slot_cost_multiplier=1.01` (Auto slot production Cost Multiplier, `slotMultiplier`)
  - `manual_slot_cost_multiplier=1.01` (Manual slot production Cost Multiplier, `manualSlotMultiplier`)
  - `rank_modifier=1` (Cookie Rank Modifier, `cookiemod`: leaving rank r needs rank_modifier × 4 × r² XP)
  - `experience_modifier=1` (Cookie Experience Modifier, `cookieExpMod`: XP per baked cookie)
  - `auto_event_chance=2` and `manual_event_chance=2` (the two event-chance sliders, `evchanse` / `evchanse2`). **Percent per bake (dev chose 2026-10-26, after first picking an exact "one in N" value and finding higher = rarer backwards):** the value is the percent chance per bake that an event can fire. 0 = off (never), 100 = every bake, decimals allowed (0.5 = 1 bake in 200). Default 2 = exactly 1 bake in 50, matching today's stored 50 (1 in 51) closely, so play is unchanged. Implement in `attempt_autobaker_event` / `attempt_manebaker_event` (`baker_events.nvgt`) by replacing `random(0, probability_chance) != 0` with a percent roll, e.g. `if (double(random_float()) * 100.0 >= chance) return;` (random_float is [0, 1), so 0 never passes and 100 always passes); the globals become doubles holding the percent. The event's own chance check (`random(0, 100) > item.chance`) is unchanged and still applies on top. The old slider (stored 100 − position) showed the stored 50 as "50 percent", which was wrong; the readme/table comments must say it's a percent of bakes, with 2 = 1 bake in 50.
  - `sell_price=50` (Cookie sell price in cents, `cookieSellPrice`)
  - `starting_coins=10`, `starting_autocookie=0`, `starting_manualcookie=1` (hard-coded in `reset_game_state`; starting coins also in the profile setup's "I'll give you …" message + `usercoins`, `cycrz.nvgt`)
  - `automation_base_cost=15`, `auto_slot_base_cost=30`, `manual_slot_base_cost=30` (hard-coded `automationBaseCost` / `slotBaseCost` / `manualSlotBaseCost` in `reset_game_state`; they grow as you buy and are saved per run, so the table only sets the value at the start of a run)
- **No longer in gamesets.crz (dev):** remove these eight from `writepreffs`/`readpreffs`, from `reset_game_settings`, and from the settings Cancel snapshot (`setsnap_*`). Old gamesets.crz files still contain the keys; they're simply never read again.
- **Settings menu:** the eight sliders are removed from the game settings form. The even-splitting checkboxes and the event on/off checkboxes stay.
- **Fallback to defaults (dev confirmed 2026-10-26):** a missing main.table, a missing section, or a missing/invalid line keeps the built-in default for that value — no alert-and-exit like events/slots/ranks, since every value has a safe default. The built-in defaults equal the shipped values above. Reset every value to its default before each parse, so deleting a line on Control L really restores the default. Values are clamped (multipliers ≥ 1, modifiers > 0, sell price ≥ 1, event chances 0 to 100, starting values and base costs ≥ 0).
- **Control L** re-reads main.table like the other tables. Multipliers, modifiers, event chances and sell price apply at once (recompute `xprequired` via `rank_threshold(rank)` after a rank_modifier change); starting values and base costs apply from the next new game or prestige.

## Build sections

1. **main.table + parser + use it.** New file with the keys above (shipped defaults). New parser (e.g. `parsers/main_table.nvgt`) loading into the existing globals at startup and in `reload_config`; replace the hard-coded starting values and base costs in `reset_game_state` and the profile setup with the table values. Sliders still exist this section — but they'd be overwritten by the table at load, so section 2 must follow before testing persistence. Dev tests: new game starts with the table's values; edit a value + Control L applies; missing file falls back to defaults.
2. **Remove the sliders and their gamesets.crz storage.** Settings form, `writepreffs`/`readpreffs`, `reset_game_settings`, Cancel snapshot. Dev tests: settings menu no longer shows them; changing other settings and Save/Cancel still work; values come only from main.table.
3. **Docs.** Readme: settings description (remove the eight), new main.table modder reference (all keys, what each does, event chances as a percent of bakes with 0 = off and 2 = 1 bake in 50), update the section-header file count ("Eight of the files…" → nine, add main.table). Changelog: this opens the **7.2** block — ask the dev to confirm the number and bump `build/version.txt` ([[feedback_update_build_version_txt]]). Add a finished todo line.

After this ships: the prestige-spam fix, option C (quest gate on points + `min_rank` 100) — dev leaning toward it but min_rank 100 not yet confirmed.
