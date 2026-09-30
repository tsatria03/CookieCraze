---
name: project_high_bugs_fix_plan
description: "IN PROGRESS (6.9): plan to fix the 8 open High-severity player-facing bugs one per section/commit — new-game prestige leak, achievements not saved, settings Save overwriting a slot, unsafe save write, dead menu search, settings Cancel not undoing, mid-round Escape losing the bet, Ctrl+L free quest reroll — then docs last."
metadata:
  node_type: memory
  type: project
---

# High-severity bug fix plan (6.9)

Agreed 2026-09-30. Source list: the High items in [[project_bugs_player_facing]]. **One bug per section, one section per turn; the dev tests and commits between sections** ([[feedback_stage_commits_before_big_changes]], [[feedback_check_git_log_for_commits]]). Docs are the final section ([[feedback_docks_last]]). Mark each section **DONE** here as it lands. Line numbers are 6.9 — re-locate by symbol ([[feedback_verify_code_while_fixing]]).

**Build order (dev-decided 2026-09-30): no-decision sections first — 1, 2, 3, 8 — then the ones with open decisions — 4, 5, 6, 7 — then 9 (docs).** Section numbers below keep the dev's bug numbering, not the build order.

**Numbering:** the dev's list numbers 1–8 map to the audit numbers in [[project_bugs_player_facing]] as: 1→#1, 2→#2, 3→#3, 4→#4, 5→#6, 6→#7, 7→#8, 8→#9. Sections below use the dev's numbers.

## Cross-cutting facts found while planning

- `reload_config(true)` (`extrafuncts.nvgt`) is called **after** `readdata()` by Ctrl+L in the main game (`game.nvgt` ~374) and in minigames (`extrafuncts.nvgt` ~953). It currently does `achievementsUnlocked.delete_all(); init_achievements(...)` and `assign_quests()` — so it wipes both restored achievements (section 2) and restored quests (section 8). Both sections must make `reload_config` **preserve** state rather than rebuild it.
- `readdata()` already recomputes `xprequired` from `cookiemod` and calls `apply_all_prestige_store_upgrades()` (`savefuncts.nvgt` ~312-319). Game settings like `cookiemod`/`cookieSellPrice` live in the preferences file (`writepreffs`/`st`), not the save.
- Legacy engine file API (verified in `Legacy-NVGT/src/filesystem.cpp`): `file_move` / `file_rename` **fail if the target exists** (`OPT_FAIL_ON_OVERWRITE`) — delete the target first. `file_copy(src, dst, overwrite)`, `file_delete`, `file_exists`, `file_get_size` exist.
- `resetgame()` (full reset) is called from the new-game paths at `menu.nvgt` ~222 and ~241; `resetgame(true)` from prestige ~2389.
- `usersetsmenu()` and `gamsetsmenu()` call themselves (after a name change, after a cancelled reset), so any "snapshot on entry" must be taken by the caller (`preffsmenu`), not inside the submenu.
- Blackjack and higher or lower each hold a local `bool in_round` (`jackgame` ~463, `highlowgame` ~1036), in scope at their Escape handlers (~478, ~1055).

## Sections

### 1. New game keeps the previous slot's prestige progress (audit #1) — DONE, dev-tested 2026-09-30; changelog + todo updated
In `resetgame()`'s full-reset branch (`cycrz.nvgt`), before `reset_game_state()`: `prestige_points = 0; prestigePurchased.resize(0); prestigeEndlessCounts.delete_all();` plus the combo stats (`stat_highest_combo_reached`, `stat_combos_started`, `stat_combos_broken` — confirm exact names), then `apply_all_prestige_store_upgrades()` so the `prestige_*` multipliers, starting bonuses and rank discount recompute to zero before `reset_game_state()` adds them. No open decisions. Test: prestige on one slot, start a new game on another, check prestige points, prestige store, starting money.

### 2. Achievements aren't saved (audit #2)
- `writedata`: `achievementCount` + `achievement_<i>` ids (same shape as `prestigePurchasedCount`).
- `readdata`: always `achievementsUnlocked.delete_all()` first (slot isolation). If `achievementCount` exists, load every saved id. Either way, then call `init_achievements(...)`, which silently adds any whose stat already crosses (keeps old saves working and migrates them on next save).
- Remove the now-redundant `init_achievements` calls after `readdata()` (`cycrz.nvgt` ~68, `menu.nvgt` ~157) — `readdata` owns it.
- `reload_config`: stop `delete_all()`; keep the dictionary and just run `init_achievements` after re-parsing.
- Prestige already leaves `achievementsUnlocked` alone; full reset already clears it. No open decisions. Note: saves that already lost unlocks to this bug can't be restored — unlocks come back as their stats are re-crossed, with rewards, one final time.

### 3. Saving settings from the main menu overwrites the last slot (audit #3)
`gamsetsmenu()` Save: wrap `writedata(); readdata();` in `if (ingame)`. In game the pair is still needed (readdata recomputes `xprequired` from a changed `cookiemod`). No open decisions.

### 4. Unsafe save write (audit #4)
- `savedata::save()` (`deps/savedata.nvgt`): write `fn + ".tmp"`, close; if the tmp is non-empty, `file_delete(fn + ".bak")`, `file_move(fn, fn + ".bak")` (if `fn` exists), `file_move(fn + ".tmp", fn)`. Port the legacy fork's explicit close.
- `savedata::load()`: port the fork's missing-file guard. If the loaded dictionary is empty (failed decrypt/truncated) and `fn + ".bak"` exists, load the backup and set a `loaded_from_backup` flag.
- Caller announces the restore in one sentence ([[feedback_one_sentence_game_messages]]).
- Check any code that deletes a save slot also deletes its `.bak`/`.tmp`.
- **Open decision (ask at this section):** where to announce the backup restore (recommend: on load, before entering the game).

### 5. Menu search rejects letters after a purchase prompt (dev #5, audit #6)
Root cause: the global `vd`'s disallowed-character setting persists between prompts (`deps/virtual_dialogs.nvgt` `input_box` ~132-145). **Recommended fix:** clear the stored restriction at the end of `vd.input_box`, so a restriction applies only to the prompt that set it (every current caller sets it right before its own prompt). Fallback if the dev prefers minimal: `vd.set_disallowed_chars("")` in `find_searched_item` (`deps/custom_menu.nvgt` ~218). Confirm at this section.

### 6. Settings Cancel/Escape don't undo changes (dev #6, audit #7)
Snapshot in `preffsmenu()` when a submenu button is pressed (submenus recurse, so they can't snapshot themselves); restore on Cancel and Escape, and speak "canceled" ([[feedback_menus_say_canceled]]).
- Game settings: every slider/checkbox/list global in `gamsetsmenu` (multipliers, `cookiemod`, `cookieExpMod`, `evchanse`/`evchanse2`, `cookieSellPrice`, `numberFormatMode`, the sfx/event/distribution/locked-shop bools).
- Audio: the four volumes plus `ambtype`/`ambienceIndex`/`mustype`/`musicIndex`; if a track changed, restart the original track; re-apply volumes.
- User settings: `playergender`.
- **Open decision (ask at this section):** should Cancel also revert first/last name changes made through their own prompts (recommend yes — nothing sticks without Save).

### 7. Escape mid-round loses the bet (dev #7, audit #8)
At the Escape handlers in `jackgame` and `highlowgame`, check `in_round`.
- **Open decision (ask at this section):** (a) block leaving until the round ends, with one sentence such as "Finish this round before leaving." (recommended — simplest, player keeps control); (b) leave and count it as a loss; (c) higher or lower only: bank the pot on exit when a streak is running.

### 8. Ctrl+L gives a free quest reroll (dev #8, audit #9)
Make `reload_config` preserve quests: record the active quest ids before re-parsing `quests.table`, re-resolve them by id against the fresh `loadedQuests`, and only call `assign_quests()` to fill slots whose id no longer exists (or when none were active). Works regardless of the readdata/reload order. No open decisions.

### 9. Docs — done PER SECTION, not at the end (dev-decided 2026-09-30)
**Once the dev confirms a section works:** mark it DONE here, add its player-facing `changelog.txt` 6.9 entry (newest at top), and move its `todo_list.txt` line from `****Unfinished.` to the top of the finished section ([[feedback_todo_list_format]]). See [[feedback_docks_last]] for the bug-fix exception.
- 6.9 changelog count: store crash, Ctrl+S/slots bet timing (logged 2026-09-30), section 1 = **3 of 10**. The 7 remaining sections fit exactly; consolidate if anything else lands ([[feedback_changelog_rules]]).
What's left for the very end:
- `readme.txt`: only if a documented behavior changed (e.g. settings Cancel, save backup).
- Mark each fixed item in [[project_bugs_player_facing]] **FIXED (6.9)**; mark this plan SHIPPED.
- `build/version.txt` stays 6.9.
