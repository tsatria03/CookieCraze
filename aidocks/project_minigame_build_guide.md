---
name: project_minigame_build_guide
description: "Repeatable step-by-step guide for adding a new minigame to CookieCraze: discuss then write a blueprint, plan sections, code one section per turn and WAIT for the dev's commit before the next, docks last. Includes the full file/function wiring map learned from the higher or lower build."
metadata:
  node_type: memory
  type: project
---

# How to add a new minigame

Derived from the higher or lower build (6.4/6.5). Every minigame reuses the same scaffold: config table + parser + runtime state/stats + a `*game()` loop + menu button/rank gate + an auto-mode setting + achievements/quests + docks. Follow the data-driven ethos ([[project_data_driven_config]]) and audio-only UX ([[project_game_vision]]).

## Workflow (the process the dev wants)

1. **Discuss/plan the blueprint first, THEN write it down.** Talk through the design (mechanic, config fields, sounds, unlock rank) before writing anything; then record the full blueprint in a memory (higher or lower lived in [[project_feature_ideas]]). A design chat or a `?` is not a green light — [[feedback_confirm_before_implementing]].
2. **Plan each section carefully and write the ordered section list down** (in the blueprint memory). 
3. **Code one section per turn, then WAIT for the dev to commit before starting the next.** The dev runs, verifies, and commits ([[feedback_dont_run_or_build_the_game]], [[feedback_check_git_log_for_commits]]). End each turn with a bare-filename "Files changed:" list + relaunch note ([[feedback_list_modified_files]]).
4. **Docks are ALWAYS the last section** ([[feedback_docks_last]]).
5. Consider a **prep refactor** before Section 1 if the new game would duplicate an inline block across the existing games (higher or lower factored the rank-up block into `check_rank()` in `ranks_table.nvgt` first, as its own commit). Whether to isolate the prep refactor (or the rank-ladder shift) into its own commit is the dev's **case-by-case call, not a rule** — don't assume separate commits every time.

## Section order (proven)

1. **Config table** — `data/config/tables/<game>.table`. Ships in the minimal style (just `; Section` headers + `; Required header` markers, no explanatory prose — that goes in the readme). Draft it, get sign-off, commit. This is the blueprint made concrete.
   - **Done when:** table written in the minimal shipped style (section headers + required-header markers, no prose), draft reviewed and signed off.
2. **Parser** — `src/includes/main/parsers/<game>_table.nvgt` (auto-included via the `main/parsers/*` glob — no include edit). Follow the dice pattern: a self-contained global `<game>Config` + `parse_<game>_table(filepath)`. Hook it in `src/cycrz.nvgt`'s `main()` with a load-failure guard (exit + alert if a required section is empty). New `.nvgt` files must be CRLF ([[project_repo_hygiene]]).
   - **Done when:** parser file created (CRLF), `parse_<game>_table()` called in `main()` with a load-failure guard, every config field read with a sensible default, launches clean.
3. **State & stats** — in `dec.nvgt`: the auto-mode bool (default true, e.g. `highlowmode`) + `STAT_*` const ids and `stat_*` doubles (games/wins/losses/plus the game's signature stat). Persist in `savefuncts.nvgt` (readdata/writedata for stats; writepreffs/readpreffs for the auto bool) and the stat-reset block in `cycrz.nvgt`. Add a stats block to the in-game stats screen in `extrafuncts.nvgt`. (Leave the `reset_game_settings()` default for the settings section.)
   - **Done when:** auto bool + `STAT_*` consts + `stat_*` doubles in dec.nvgt; stats in readdata/writedata and the cycrz.nvgt reset block; auto bool in preffs; in-game stats-screen block added.
4. **The minigame** — `<game>game()` + `<game>_game_input()` in `minigames.nvgt` (dicegame/jackgame are the closest models). Clone the shape: `form.reset()`→window→controls→`set_disallowed_chars` on the bet field→loop with `wait(5)`+`form.monitor()`+balance clamps+focus-guarded `<game>_game_input()`+`ESC`+`check_rank()`+`check_achievements(...)`. 5-currency `betChoice` selector, money ×100, `confirm_threshold`, every payout `floor(safe_cap(bet*mult))` ([[project_number_overflow_cap]]). Auto vs manual reveal via the game's bool (`mini_wait_async` + `speak_buffer` vs `dlg_buffer`). Watch the compile-breakers: [[project_angelscript_braceless_if]], [[project_angelscript_reserved_words]], [[project_nvgt_key_pressed_oneshot]], [[project_nvgt_sound_preload_cache]].
   - **Done when:** `<game>game()` + `<game>_game_input()` written; bet validation + confirm; every payout `floor(safe_cap(...))`; `check_rank()` + `check_achievements()` each loop; auto/manual reveal branches; stats increment; braces balance and it launches clean.
5. **Menu & rank gating** — panel button in `mingamsmenu()` (`menu.nvgt`) in its **alphabetized** slot with a hardcoded `rank < N` gate; the `"<game>"` case in `handle_rank_unlock` (`ranks_table.nvgt`); a rank line in `ranks.table`; the `"<game>"` dispatch case in `minigame_input()` (`extrafuncts.nvgt`, Ctrl+S/L). **Rank ladder ripple:** the panel is alphabetized, so the new game's unlock rank must fit its alphabetical position — inserting it usually shifts later games up 10 ranks. That means editing the hardcoded gates in `menu.nvgt`, the ranks in `ranks.table`, the combo gate in `combos.table` (`enabled=true:N`), and knowing `slotManagerUnlockRank` auto-derives from the ranks.table slotmanager line. Existing high-rank saves see shifted features temporarily re-lock — call this out.
   - **Done when:** alphabetized panel button + `rank<N` gate; `handle_rank_unlock` case; `minigame_input` dispatch case; ranks.table line + ladder shifts + combos.table gate applied; re-lock consequence flagged to the dev.
6. **Settings toggle** — auto-reveal checkbox in `gamsetsmenu` (`settings_menu.nvgt`, NOT menu.nvgt) + its `form.is_checked` read-back + the default in `reset_game_settings()` (`savefuncts.nvgt`).
   - **Done when:** checkbox + read-back added in settings_menu.nvgt; `reset_game_settings()` default set.
7. **Achievements & quests** — see the rules below. Wire `get_stat_value` in `achievements_table.nvgt` (BOTH systems read progress through it) and `get_stat_display_name` in `quests_table.nvgt` (the `%stat%` label, for quest-referenced stats).
   - **Done when:** category registered; achievements added (unique prefixed ids, losses allowed); quests added (wins-only, ties ok, no losses); `get_stat_value` maps every new stat; `get_stat_display_name` labels the quest-referenced stats; colon/field counts verified.
8. **Docks (LAST)** — see below.
   - **Done when:** up to 3 changelog entries (newest-on-top, within the entry cap) + version.txt bumped if a new block; readme gameplay blurb (unlock order) + statistics block (+ section-count updated) + config modding reference; all dock lines ≤1024 chars.

## Docks (Section 8)

**Changelog** — 3 entries if possible ([[feedback_changelog_rules]], newest-on-top within the block): (1) the minigame itself, folding in its game-specific setting; (2) the achievements; (3) the quests. Mind the **entry cap** (10 per minor block, 20 per major) — if the block would overflow, a related change like the rank-ladder shift can go in the NEXT version block (higher or lower's shift shipped as a separate 6.5 entry). Bump `build/version.txt` with each new block ([[feedback_update_build_version_txt]]).

**Readme** (`cycrz/docks/readme.txt`) — document EVERYTHING, three places, all within 1024 chars/line ([[feedback_dock_line_length_1024]]):
- **Gameplay "how to play" blurb** in the minigames section, inserted in unlock order (this is what forces the rank-header shifts of later games). Format: `Name. Unlocked at rank N.` / difficulty tier + one-line note / one-sentence hook / detail paragraphs (betting with the dollar-value note, the core mechanic, the config-file pointer, the manual/auto toggle, the confirmation prompt).
- **Statistics section** — add the game's block to the "Baker statistics" list (match the in-game screen order) AND update the "divided into N sections" count.
- **Config modding guide** — the `<game>.table` reference, placed alphabetically among the table sections. Format mirrors dice.table/jacks.table: `Location:` line, one-line description, each field name on its own line + prose, `Format: a:b:c` lines for row/section formats, and `%token% is replaced with …` lines for message placeholders.

## Cross-cutting rules (easy to forget)

- **Quests track wins and neutral/play actions only — never losses.** Ties are allowed; losses belong to achievements. Quest format: `id:Name:stat:threshold:false:false:true:tier:Description with %threshold%`. Play-count + wins (+ the game's signature positive stat) tracks, 5 tiers each.
- **Achievements track everything including losses.** Give the game its own **category** (add to the `; Categories` block in `achievements.table`). Format: `category:id:stat:threshold:false:true:Name:Description:Flavor`. Minigames use the `false:true` flags. **Achievement ids are GLOBALLY unique** (keyed by id alone in `unlocked.set(id,...)`) — prefix every new id (e.g. `hl_`) to avoid collisions with existing ones. Probability-bounded stats (streaks) need small thresholds; grindable counts (games/wins/losses) climb high.
- **Stats live in TWO player-facing places:** the in-game stats screen (`extrafuncts.nvgt`) and the readme statistics section — keep both in sync, and update the readme's section count.
- **Sounds** go in `sounds/minigames/<game>/`; config sound fields support the `subfolder/name(1,2).ogg` random-pick syntax via `resolve_sound`. List the exact files the dev must add.
- Money is internally `coins` but always shown as dollars/cents ([[project_coins_currency]]); use `convert_to_currency` for the currency bet and `format_number` otherwise.
- Currency-neutral wording in player text (you can bet cookies/speeds/etc., not just money) — e.g. "bank your earnings", not "cash out".

## File/function map (where each piece goes)

- `data/config/tables/<game>.table` — config (Section 1)
- `src/includes/main/parsers/<game>_table.nvgt` — parser + `<game>Config` global (Section 2)
- `src/cycrz.nvgt` — `parse_<game>_table()` call + load guard (Section 2); stat-reset block (Section 3)
- `src/includes/main/globals/dec.nvgt` — auto bool, `STAT_*` consts, `stat_*` doubles (Section 3)
- `src/includes/main/functions/savefuncts.nvgt` — stat save/load, preffs for the bool, `reset_game_settings()` default (Sections 3 & 6)
- `src/includes/main/functions/extrafuncts.nvgt` — in-game stats-screen block (Section 3); `minigame_input()` dispatch (Section 5)
- `src/includes/main/globals/minigames.nvgt` — `<game>game()` + `<game>_game_input()` (Section 4)
- `src/includes/main/menus/menu.nvgt` — `mingamsmenu()` button + rank gate; ladder gate shifts (Section 5)
- `src/includes/main/menus/settings_menu.nvgt` — auto-reveal checkbox + read-back (Section 6)
- `src/includes/main/parsers/ranks_table.nvgt` — `handle_rank_unlock` case; `check_rank()` lives here (Section 5 / prep)
- `data/config/tables/ranks.table` — unlock ladder line + shifts (Section 5)
- `data/config/tables/combos.table` — combo gate `enabled=true:N` shift (Section 5)
- `src/includes/main/parsers/achievements_table.nvgt` — `get_stat_value` mapping (Section 7)
- `src/includes/main/parsers/quests_table.nvgt` — `get_stat_display_name` label (Section 7)
- `data/config/tables/achievements.table` — category + achievement lines (Section 7)
- `data/config/tables/quests.table` — quest lines (Section 7)
- `cycrz/docks/changelog.txt`, `cycrz/docks/readme.txt`, `build/version.txt` — docks (Section 8)
