---
name: project_combo_repeat_plan
description: "SHIPPED 7.2 (both sections built, all 4 steps passed, 2026-10-27): past the last combo tier, the tier sounds cycle again (same spacing) with an optional repeat_message from combos.table; multiplier stays at the last tier's value; a game-settings checkbox (on by default) turns repeat announcements off. 2 sections."
metadata:
  node_type: memory
  type: project
---

# Combo tier repeats (7.2)

Dev idea (2026-10-27), discussed over several turns before recording. Combos today: `combos.table` [tiers] has 8 lines (count:multiplier:sound:message) at 50, 75, …, 225 clicks, 2x…16x. Each tier fires its sound + message once when passed (`game.nvgt` manual-bake branch: `get_combo_tier_index` vs `lastComboTierIndex`); past 225 nothing more happens. Combos only exist on the main game screen.

## Locked decisions (dev)

- **Repeats:** once a combo passes the last tier, the tiers cycle again as audio milestones, keeping their spacing: with the shipped table tier 1's sound at 250, tier 2's at 275 … tier 8's at 425, then tier 1 again at 450, forever while the combo lasts.
- **Multiplier does NOT grow and does NOT reset:** it stays at the last tier's value (16x shipped) for the rest of the combo. Dev: a rising number would make players think production rose when it didn't. `get_combo_multiplier` needs no change (it already holds the highest tier passed).
- **Old tier messages never repeat** (their flavor text climbs in excitement; cycling back from "Cookie Singularity" to "hitting your stride" sounds like a step down).
- **Optional repeat message in combos.table** [settings], shipped ON:
  `repeat_message=%combo% click combo! Still going at %multiplier%x per press!`
  Same tokens as tier messages (`format_combo_message`). Missing/empty line → repeats are sound-only. "Still going" wording chosen so nobody thinks the multiplier rose.
- **Player switch in the game settings, not combos.table** (it's taste, not design — see [[feedback_settings_vs_tables]]). Checkbox label (dev's wording): `Enable repeating combo tier announcements past the last tier.` — "announcements" = the repeat sounds AND the repeat message together; unticking turns both off (past the last tier is then silent, as today). **On by default.** Placed **right after the exit sound checkbox** in `gamsetsmenu` (settings_menu.nvgt).
- **Three layers (dev-approved recap):** (1) modder: whether repeat_message exists, and which tier sounds cycle (the [tiers] sounds); (2) player checkbox: whether repeats happen at all (sound + message); (3) player F2 (combos buffer mute): silences every combo sound and message, normal tiers and repeats alike, still logging messages.
- Unchanged: the combos `enabled=true:90` switch stays in combos.table (a modder/design decision).

## Implementation notes (verified 2026-10-27)

- Settings plumbing mirrors `exitsfx`: global bool in `dec.nvgt` (default true); snapshot/restore in settings_menu.nvgt (`setsnap_*` lines ~5/17/30, for Cancel/Escape undo); `create_checkbox` right after the exit sound one (~line 136) + `form.is_checked` read-back (~line 162); `readpreffs` (`st.d.exists`/`read_bool`), `reset_game_settings` default true, `writepreffs` (`st.add`) in savefuncts.nvgt.
- Repeat timing: period P = (last count − first count) + (last count − second-to-last count) → shipped: (225−50)+25 = 200, so tier i repeats at count_i + k·P. One-tier table: P = that tier's count. Needs its own "last repeat fired" tracker (reset wherever `lastComboTierIndex` resets: cycrz.nvgt new-game reset and game.nvgt ~line 247 when the combo breaks) so each repeat fires once.
- Sound: `pool.play_stationary("sounds/" + tier.sound, false)` guarded by `!buffer_muted("combos")`, like the normal tiers; message via `speak_buffer(format_combo_message(repeat_message, comboCount, comboMult), "combos")`.
- Parser: `combos_config` gains `string repeat_message`, reset each parse, read by key in [settings].

## Build sections

**Section 1 BUILT and all 4 steps PASSED (2026-10-27); test edits restored. Section 2 docs DONE: readme combos player paragraph, combos.table repeat_message + tiers spacing text, changelog 7.2 entry 7 of 10, todo Finished line. Dev commits both sections together.** combos_table.nvgt: `repeat_message` field (cleared each parse), `combo_repeat_period()`, `combo_repeat_count(n)`. dec.nvgt: `bool comborepeats = true;` after exitsfx, `int comboRepeatsFired = 0;` after lastComboTierIndex. game.nvgt: an `else if` after the tier-fire block replays the cycled tier sound + repeat_message once the last tier is passed; `comboRepeatsFired = 0` beside both `lastComboTierIndex = -1` resets (combo break in game.nvgt, new game in cycrz.nvgt). Setting wired like exitsfx (snapshot/restore, checkbox after exit sound, read-back, preffs key `comborepeats`, reset default true). combos.table: `repeat_message=…` after stop_message. **Test edit in place:** combos.table tiers 5..40 step 5 (normally 50..225 step 25) and start_count=2 (normally 25) — restore when testing ends.

1. **Code + table + setting.** Parser field, repeat logic in game.nvgt, the setting end to end, `repeat_message` line in combos.table (after `stop_message`). Tests (edit config yourself — [[feedback_edit_config_for_tests]]): a long combo past 225 hears tier 1's sound + repeat message at 250, tier 2's at 275, multiplier still 16x; untick the setting → silent past 225; delete repeat_message → sound only; F2 mute → nothing.
2. **Docs.** Readme: combos player section (repeats, multiplier holds), game settings list (new checkbox), combos.table reference (`repeat_message`, how repeats space out). Changelog 7.2 entry (7 of 10). Todo: add Unfinished item when starting / Finished at the end. Memory.
