---
name: project_bugs_code
description: "Bug audit 2026-09-30 (v6.8): internal code defects — save/load ordering, int overflow, recursive menu/game-loop calls, parser robustness, unchecked config loads, stale vendored deps, missing stat increments, duplication hotspots. Unfixed unless marked."
metadata:
  node_type: memory
  type: project
---

# Code bugs (audit of 6.8, 2026-09-30)

Defects a player may not see directly but that cause wrong state, latent crashes or future bugs. Companion to [[project_bugs_player_facing]], [[project_bugs_exploits_balance]], [[project_bugs_build_docs]]. Line numbers are 6.8 — re-locate by symbol ([[feedback_verify_code_while_fixing]]). Mark **FIXED (version)** when shipped. No compile-breakers ([[project_angelscript_braceless_if]], [[project_angelscript_reserved_words]]) were found anywhere in the audit.

## Save / load

1. **Unsafe save write + silent empty load** — see [[project_bugs_player_facing]] #4 (`deps/savedata.nvgt` `save()`/`load()`). The vendored `savedata.nvgt` is also *older* than the legacy fork's: it lacks the fork's missing-file guard in `load()` and the explicit `f.close()` in `save()`. Port both.
2. **Achievements not persisted** — see [[project_bugs_player_facing]] #2.
3. **Saved quests restored before `completedQuestIds` is loaded** (`savefuncts.nvgt` `readdata`, active-quest block ~175-197 before completed block ~198-214). If fewer saved quest ids resolve than `max_active` (id renamed, `max_active` raised), the fallback `assign_quests()` runs against the *previous slot's* (or empty) completed list and may offer done tiers or skip open ones. Fix: move the `completedQuestCount` block above `activeQuestCount`.
4. **Unknown completed-quest ids are dropped and then erased** (`savefuncts.nvgt` ~207-212). If `quests.table` fails to load (unchecked, #9) or an id is renamed, the next save deletes those completions permanently, lowering the prestige-points multiplier. Fix: keep unknown ids in a side list and write them back.
5. **Lottery ticket counts written as double, read as int** (`savefuncts.nvgt` ~264 `sd.read_int`). Counts > 2³¹ load wrong/negative — reintroduces the bug that making `owned` a double fixed. The same loop never zeroes tickets absent from the save, so a newly added ticket id keeps the previous slot's count. Fix: `read_double`; zero every `owned` first.
6. **Slot isolation relies on `readdata` overwriting everything** — any key missing from a save (old save, empty dictionary from #1) keeps the previous slot's in-memory value. #1, #3, #5 and the new-game prestige leak ([[project_bugs_player_facing]] #1) are all instances. Structural fix: reset to defaults before every load.
7. **Single-instance lock differs by build** — mutex name is `crc32(NVGT_VERSION_COMMIT_HASH + "cycrz")` (`deps/instance.nvgt` ~17), so a compiled release and a from-source run can both write the same saves at once.
8. **Save keys in public source** (`dec.nvgt` ~235-238), no integrity check. Fine for single-player; saves are trivially editable.

## Numeric

9. **`int()` overflow in reward rolls.** `ranks_table.nvgt` ~79 and `baker_events.nvgt` ~58 use `random(int(minVal), int(maxVal))`. Default rank reward `100*rank` overflows past rank ~21.5M → `INT_MIN` → the "reward" drains money, and `convert_to_currency` returns "" for negatives (empty amount spoken). Fix: roll as double (`minVal + random_double * range`).
10. **`prestige_points` and `pointsEarned` are 32-bit `int`** (`prestige_table.nvgt` ~29, `cycrz.nvgt` ~183, `savefuncts.nvgt` ~129 `read_int`, `menu.nvgt` ~967). Overflows very late with endless completions × `10*rank`. Fix: make it double end to end.
11. **Only one rank-up per frame** — `game.nvgt` ~249 and `check_rank` use `if`, not `while`. A huge XP gain trickles out at ~200 ranks/s, each paying a reward and running `check_achievements`; the rank-thinning comment assumes several ranks can arrive at once.
12. **Dead branch** — `game.nvgt` ~234-238 tests `autocookie<=0` inside a block that requires `autocookie>=1`.

## Control flow

13. **Recursive "loops".** `clickergame()` calls itself (`game.nvgt` ~71, 370, 384, 504, 539, 552; also from `show_baker_info`, `extrafuncts.nvgt` ~538); menus call each other (`mainmenu`→`gamemenu`→`mainmenu`, `clickergame`→`shopmenu`→`clickergame`, `prestige_store_category_menu` calls itself after each buy). Legacy engine stack is unlimited (`nvgt_angelscript.cpp` ~530-531) so it doesn't crash — it grows memory over a long session, and any path that does return falls through into the code after the call. Fix: long term, menus return results to one loop; short term, `return;` after every recursive call.
14. **`reload_config` re-parses events/slots/ranks without the startup `length()==0` checks**, and calls `assign_quests()` (see [[project_bugs_player_facing]] #9).

## Minigame code

15. **`stat_coins_earned` not incremented on slots wins** (~405) **or any blackjack win path** (~550, ~598, ~636); dice/highlow/roulette do. Undercounts earnings achievements/quests.
16. **Bet-flow duplication** — bet parse/×100/min/max/clamp copied 5× (`minigames.nvgt` ~260-290, 477-508, 814-845, 1063-1094, 1381-1403); confirm dialog 5×; "apply amount to chosen stat" if/else ladder ~20×; 8-line stat clamp 7×; blackjack/dice fill placeholders by hand though `highlow_fill` exists. Suggested helpers: `bet_balance(sel)`, `apply_bet_delta(sel, amt)`, `bet_item_name(sel)`, `validate_bet(...)`, `confirm_bet(...)` — would also fix #15 and the missing "canceled" messages in one place.

## Parsers / config loading

17. **Unchecked config loads at startup** (`cycrz.nvgt` ~46-47, 60-65): `parse_jacks_table`, `parse_dice_table`, `parse_combos_table`, `parse_achievements`, `parse_prestige_table`, `parse_quests_table`, `parse_lottery_table`, `parse_tickets_store`. Stores (singles/bundles/prestige) are parsed later in menus, also unchecked. Missing `prestige.store` → `all_standard_prestige_purchased()` returns true and unlocks the Endless menus; missing quests → prestige never pays. Fix: same `length()==0` alert pattern as events/slots/ranks.
18. **`=` in an item line is misread as a menu/category definition** — `single_store.nvgt` ~39, `bundle_store.nvgt` ~48, `prestige_store.nvgt` ~38, `achievements_table.nvgt` ~36 test `line.find("=")` before splitting on `:`. No shipped line trips it; a modder trap. Fix: only when `=` precedes the first `:`.
19. **Fields aren't trimmed; `string_to_int` returns 0 on any non-digit** (`extrafuncts.nvgt` ~120). `min_rank=20 ` → 0 (unlocked at rank 0); `"true "` reads false. Fix: trim each field.
20. **UTF-8 BOM** on a `[settings]` first line makes the whole settings section be ignored. Fix: strip `\xEF\xBB\xBF` from line 1.
21. **Colons in free text shift fields** — combo tier messages use `line.split(":")` (`combos_table.nvgt` ~44) so a colon truncates the message; a colon in an achievement description/hint shifts the reward fields.
22. **Duplicate ids undetected** in every parser; first match wins silently.

## Vendored deps

23. **`speech.nvgt` ~69 default arg `= ffalse`** (typo; legacy fork fixed it). Compiles only because `tts_dump_config` is never called with one argument — a latent compile-breaker.
24. **`keyhook.nvgt` is unused** and carries the stdlib bug where `uninstall()` checks `!already_installed` (~14) so it never runs. Delete the file.
25. `form.nvgt` / `virtual_dialogs.nvgt` are older 2024 copies (≈ 520 lines differ from the fork); informational — the game uses none of the newer features. `buffer`, `custom_menu`, `dlg`, `input_forms`, `bgt_compat` are deliberate local forks; don't resync from stdlib. `sound_pool`, `rotation`, `dget`, `instance` are identical to the fork. See [[project_include_tree]].

## Duplication hotspots (bug magnets, not bugs)

- Rank-up block written twice: `game.nvgt` ~249-288 and `ranks_table.nvgt` ~143+.
- Threshold formula (magic constant 4) in four places: `game.nvgt` ~252, `cycrz.nvgt` ~306, `savefuncts.nvgt` ~312, `ranks_table.nvgt` ~148.
- Speed *loss* + clicktime push-back hand-written in `baker_events.nvgt` and `ranks_table.nvgt`, while gains go through `gain_cookiespeed`.
- `resetgame` / `reset_game_state` list stats by hand — the root cause of the prestige-leak and inconsistent-reset bugs. A single stat table with a per-run/lifetime flag would prevent recurrences.
- `menu.nvgt`: purchase-level ternary 8× (~1058-1703); add-slots menus hard-code 12 items + 12 matching ifs twice (~1723-1767, ~1800-1843); minigame lock block 7× (~266-370); quest list builder 4× (~2068-2155); plural handling ~5×.
- `buffer.nvgt` status string 4× (~150-177); save/reload handlers copied between `game.nvgt` ~357-385 and `extrafuncts.nvgt` ~915-955.
