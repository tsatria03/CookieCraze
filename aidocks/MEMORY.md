# CookieCraze memory index

The `[[name]]` links in `CLAUDE.md` and across these memories resolve to `aidocks/<name>.md`. Add a one-line pointer here for every new memory. "Memory" / "memories" always means this folder. (Repo folder is CookieCraze; source/asset folder is `cycrz`; README titles it "CookyCraze".)

## Project — what the game is and how it's built
- [Game vision](project_game_vision.md) — audio idle/incremental cookie clicker: bake → sell → upgrade → rank up → automate → prestige, with rank-gated minigames; the load-bearing systems.
- [Coins currency](project_coins_currency.md) — internal money identifier is `coins` (config only); all player-facing text shows dollars/cents; don't rename or surface "coins".
- [Message buffers](project_message_buffers.md) — categorized message-buffer system (achievements/combos/critical/events/general/…), each mutable + exportable (per-buffer only, by design — no export-all key), for non-interruptive announcements.
- [Data-driven config](project_data_driven_config.md) — cycrz/data/decompiled/config/ (events/stores/tables; config.crz pack fallback via config_file) is plain-text modder-tunable; parsers in main/parsers/ load it; docks/readme.txt is the format reference.
- [Path conventions](project_path_conventions.md) — src/ (code) + cycrz/ (assets+launcher) + build/ + releases/ split; the cwd=cycrz/ trick; no #pragma asset (tools.py copies assets).
- [Include tree](project_include_tree.md) — src/includes/ = version.nvgt + main/{deps,functions,globals,menus,parsers}; wildcard glob aggregation; vendored stdlib helpers incl. dget + rotation.
- [Build pipeline](project_build_pipeline.md) — cycrz.py launcher + build/tools.py (commit tools + compile→package→release→website), version mirroring, tools.ini + ~/.game_tools/tools.ini config; cycrz/lib/ ships BASS + screen-reader DLLs.
- [Audio model](project_audio_model.md) — sound_pool + HRTF; cycrz/sounds/ layout (ambience/menu/misc/dlg/store/combos/events/minigames/buffer); no sound packs.
- [Save-data layout](project_save_data_layout.md) — writable data in AppData under tsatria03/CookieCraze/ (logs/preffs/saves); multiple save slots.
- [Repo hygiene](project_repo_hygiene.md) — .gitattributes CRLF enforcement + binary rules; what's gitignored; CLAUDE.md + aidocks/ are committed.
- [Engine pinned to nvgt](project_engine_pinned_nvgt2.md) — runs on the legacy fork at C:\nvgt (BASS); upstream C:\nvgt2 (miniaudio) is incompatible; don't target it or suggest upgrading.
- [Prestige store schema](project_prestige_store_schema.md) — prestige.store item_id suffixes count up in prestige-level order (1..11 per track); save schema key + `migrate_prestige_id_v2` migration protects already-purchased ids. Renumbered in 6.3.
- [Endless prestige plan](project_endless_prestige_plan.md) — SHIPPED 6.7. Infinite/compounding prestige upgrades: 4 flat categories (Standard/Endless × Passive/Head Start), base 1 × mult 1.01 (retuned from 5 × 1.1 to match the singles shop), per-item counts, unlock after clearing Standard at prestige 10. Field is named `infinite` in code/config. Kept as design record.
- [Cookie XP rework plan](project_cookie_xp_rework_plan.md) — SHIPPED 6.7. Ranks on a dedicated cumulative `cookieXP` (SimpleFighter-style) so selling no longer costs rank progress; the two rank sliders are now a Cookie Rank Modifier (threshold) + Cookie Experience Modifier (`cookieExpMod`, XP per bake); both mods default 1 (retuned from 5 in 6.8); `difmod` removed, threshold formula uses constant 4 (retuned from 10 in 6.8); migration seeds cookieXP from the current rank's threshold. Kept as design record.
- [Prestige points are cumulative](project_prestige_points_cumulative.md) — (7.2: now paid only if a quest is complete this run; min_rank 100) points payout = `points_per_prestige × (completedQuestIds.get_size() + total_endless_completions())`; `completedQuestIds` persists across prestiges, so points come from your LIFETIME quest tally, not this-run completions — you earn points even with zero new completions. Milestone resource reward IS gated on this-run quests, points are NOT. Readme corrected in 6.8; dev left the behavior cumulative on purpose.
- [Endless quests plan](project_endless_quests_plan.md) — SHIPPED 6.8: infinitely-escalating quests (threshold = base x growth^count) that get harder each time you complete them and prestige, mirroring endless prestige. New per-quest config fields `growth`/`infinite`/`difficulty_step`; tracked in a `questEndlessCounts` dict; each completion permanently adds +1 to the prestige-points multiplier; the rank ladder becomes one endless rank quest (Section 5).
- [Cookie speed cap plan](project_cookiespeed_cap_plan.md) — SHIPPED 6.8: hard-cap `cookiespeed` at 950 (1000ms base clicktime − 50ms floor) so it can't balloon past what actually speeds baking; a `gain_cookiespeed()` helper is the single choke point for every gain and returns the applied delta so lottery/minigame/prestige awards report only the gain that helped.
- [Number overflow cap](project_number_overflow_cap.md) — shipped 6.3: stop `Inf`/`$inf` by clamping the growing balances + `stat_*` totals to 1e308 (via `floor(safe_cap(x))` normalizers, a loop clamp, and capped display locals), not just cost formulas.

## How-to guides
- [Minigame build guide](project_minigame_build_guide.md) — repeatable steps to add a new minigame: blueprint first, plan sections, code one section per turn and wait for commit, docks last; full file/function wiring map. Every new minigame shifts later unlocks up 10 ranks (incl. baking slots manager, combos); prestige is always last.

## Ideas / backlog
- [Feature ideas](project_feature_ideas.md) — 6.4+ wish list (offline progress, golden-cookie bonus, daily streak, auto-buyer, boosts shop, QoL) + future-minigame candidates (mines, video poker, keno, audio Simon). Higher or lower + number-format shipped in 6.4; achievement rewards + roulette shipped in 6.5, roulette's rank-shift + tweaks changelogged in 6.6 (all roulette sounds delivered); rest unbuilt.

- [Design review 7.0](project_design_review_7_0.md) — 2026-10-25 evaluation, potential improvements only: runaway 1.01 economy (rank 90 in ~5 min), prestige spam, endless quests pre-done after prestige, minigames lack purpose, settings as cheats, readme onboarding + agreed readme reorganization and missing keys, small config gate/category issues; #8 store prices don't drop after losing auto/manual cookies (proposed: scale purchase count by share kept).

## Release status
- [Release status](project_release_status.md) — release timing UNDECIDED (dev, 2026-10-28), ask before assuming; V7.0 latest public release, V7.10/V7.20 bookmark tags; 7.3 in progress (9 of 10), boosts moved to 7.4.

## Active plans (7.2)
- [Config pack plan](project_config_pack_plan.md) — SHIPPED 7.2 (3 sections done): config moved to data/decompiled/config (loose, git) + data/compiled/config.crz (NVGT pack, gitignored); loose wins, pack is fallback; in-game export/import never delete; tools.py unchanged, dev deletes loose folder before compiling a release; miscellaneous menu has compile/decompile data config.

## Finished plans (7.2)
- [main.table plan](project_main_table_plan.md) — SHIPPED 7.2: new data/config/tables/main.table with the 8 "cheat" sliders (slot cost multipliers, rank/XP modifiers, event one-in chances, sell price) + 6 starting values; sliders and the two event on/off checkboxes removed, no longer saved in gamesets.crz; prerequisite for the prestige-spam fix (min_rank 100); 3 sections, docs last.

## Finished plans (7.1), continued
- [Quest baseline plan](project_quest_baseline_plan.md) — SHIPPED 7.1: prestige keeps all minigame stats for life; every count-stat quest measures from a run-start baseline (rank + the two record stats excluded); old-save rule; fixes exploit #8 + the readme prestige-stats line; 3 sections, docs last.

## Finished plans (7.1)
- [Rank burst plan](project_rank_burst_plan.md) — SHIPPED 7.1: one shared rank-up function, milestones use the main-screen rule everywhere, every earned rank in one pass + [summary] message in ranks.table, rank 21M reward overflow + 2B int(rank) overflow + prestige history rank as double; 4 sections, docs last.

## Finished plans (7.0)
- [Lottery value prizes plan](project_lottery_value_prizes_plan.md) — SHIPPED 7.0: item prizes defined by money value, converted at current prices (cookies via sell price, auto/manual via the basic store item's current price), money fallback; balance all four tiers to ~85% total; 5 sections, docs last; fixes exploit #5 properly.

## Finished plans (6.9)
- [Slots scaled payouts plan](project_slots_scaled_payouts_plan.md) — SHIPPED 6.9: scale slot payouts by checked-symbol count to a fixed house edge (default 5%); house_edge + min_symbols config, %amount%/%multiplier% tokens, check-payouts button, readme docs for how symbols work; 4 sections, docs last.
- [High bugs fix plan](project_high_bugs_fix_plan.md) — SHIPPED 6.9 (7 fixed, backup saves skipped by dev); kept as design record: 8 High player-facing bugs, one section per commit (new-game prestige leak → achievements saved → settings Save guard → (safe save+backup SKIPPED, dev declined .bak files) → menu search → settings Cancel undo → mid-round Escape → Ctrl+L quests), docs last; open decisions flagged per section.

## Known bugs — full-game audit of 6.8 (2026-09-30); mark entries FIXED as they ship
- [Player-facing bugs](project_bugs_player_facing.md) — "Open after 6.9" at the top = remaining 10 Medium + 12 Low backlog in the dev's wording. Originally 31 bugs a player can hit: new game leaks prestige, achievements not saved, settings Save overwrites last slot, unsafe save write, Ctrl+S dead in minigames, F3 search rejects letters, settings Cancel doesn't undo, UX convention violations.
- [Exploits and balance](project_bugs_exploits_balance.md) — minigame EV table; dice always pays 8x, blackjack pays 3x, highlow/slots/gold+diamond tickets are +EV; prestige spam, hold-Enter combos, lopsided speed events, pacing notes.
- [Code bugs](project_bugs_code.md) — save/load ordering, int overflow in reward rolls + prestige_points, recursive game loop/menus, missing stat increments, parser robustness, unchecked config loads, stale vendored deps, duplication hotspots. Re-verified against 7.0 on 2026-10-25 (only #2 fixed, #6/#14 partly; priority list at the top). Plus #26–#31 found 2026-10-25 (Scratch all drops leftover tickets, reroll charges before checking candidates, Ctrl+L on unsaved slot, Ctrl+L partial reload, Shift+M/C double-fire (NOT a bug, verified in engine source), int(rank) overflow).
- [Build and docs bugs](project_bugs_build_docs.md) — site updater x.0 regex, tools.py deletes release first + import-time KeyError, hardcoded paths, gitignore gaps, installer mutex, readme inaccuracies/typos, two stale memory notes. Re-verified against 7.0 on 2026-10-25 (only #8 fixed, sounds now tracked) + #19: cycrz/lib was removed then restored (untracked by design); no Tolk.dll is intentional (excluded in C:\nvgt\config.properties).

## Tooling gotchas
- [Git Bash CRLF gotcha](project_gitbash_crlf_gotcha.md) — `sed -i` strips CR from CRLF files and `grep -c $'\r$'` miscounts; edit with the Edit tool, check endings with Python byte counts or the Grep tool.

## NVGT / AngelScript gotchas — these cause compile failures (game won't launch)
- [AngelScript braceless if](project_angelscript_braceless_if.md) — a braceless if/else governs one statement; a second orphans the else → compile error.
- [AngelScript reserved words](project_angelscript_reserved_words.md) — never name a variable `out` (or in/inout/shared/final/from…); reserved keyword → compile error.
- [NVGT key_pressed one-shot](project_nvgt_key_pressed_oneshot.md) — key_pressed() is consumed on first read each frame, and key_repeating() consumes the same pressed flag; read a multi-purpose key once and branch inside.
- [Legacy NVGT source](reference_legacy_nvgt_source.md) — engine C++ source at tsatria03\misc\Legacy-NVGT (src/input.cpp…); C:\nvgt is binaries only.
- [NVGT sound preload cache](project_nvgt_sound_preload_cache.md) — sound.load caches by filename; reusing a name for changed audio replays the old clip; use a fresh name or allow_preloads=false.

## Feedback — how the dev wants you to work
- [Confirm before implementing](feedback_confirm_before_implementing.md) — a design discussion or any `?` is a request for a plan, not a green light to edit; wait for explicit go-ahead.
- [Plan features in memory first](feedback_plan_features_in_memory.md) — before coding any new feature, write the agreed plan (locked decisions + numbered build sections) into an aidocks/ memory file and index it; only then build, section by section.
- [Flag todo prerequisites](feedback_flag_todo_prerequisites.md) — dev picks todo items at random; say up front if one needs another item or a decision first.
- [Test steps one at a time](feedback_test_steps_one_at_a_time.md) — give testing steps one per message ("Step 1 of N"), wait for the result, then the next; never the whole list at once.
- [Flag sections without tests](feedback_flag_sections_without_tests.md) — say plainly when a build section has no testing steps (plan and summary), and offer to merge it into a testable neighbour.
- [Pause after each fix](feedback_pause_after_each_fix.md) — after a fix + its docs, end the turn with the summary for review; never start the next item (reading, analysis, or its question) in the same reply, even in a batch.
- [Bug list format](feedback_bug_list_format.md) — when listing bugs, split player-facing vs code (internal/modder/build), group each by game section, one line per bug with its memory id.
- [Ask one question at a time](feedback_ask_one_question_at_a_time.md) — surface ONE question per turn and wait; don't batch a numbered list.
- [Ignore bang commands](feedback_ignore_bang_commands.md) — ignore the user's in-session `!` command runs and their output; act only on the user's typed prose unless they explicitly reference them.
- [List modified files](feedback_list_modified_files.md) — end every editing turn with a bare-filename "Files changed:" list; then note whether a relaunch is needed.
- [Don't run or build the game](feedback_dont_run_or_build_the_game.md) — never launch/compile (cycrz.py, tools.py, nvgt -c); edit and report, the dev runs and verifies.
- [Verify code while fixing](feedback_verify_code_while_fixing.md) — re-locate by symbol not line number, confirm the finding is true, flag adjacent bugs.
- [Check git log for commits](feedback_check_git_log_for_commits.md) — the dev commits between turns; check git log/status before assuming commit state; don't commit unless asked.
- [Stage commits before big changes](feedback_stage_commits_before_big_changes.md) — flag a commit break point before a risky stage so safe pieces land first.
- [CLAUDE.md length limit](feedback_claudemd_length.md) — keep CLAUDE.md a dispatcher under 40,000 chars; move detail into memory files.
- [Docks last](feedback_docks_last.md) — in any multi-section build plan, reserve the final section for dock updates (readme + changelog + version); code/config first, docs last. EXCEPTION: bug-fix batches get changelog + todo updates per fix, as soon as the dev confirms it works.
- [Update readme with fixes](feedback_update_readme_with_fixes.md) — always update readme.txt wherever a fix/change touches what it describes, in the same pass as changelog + todo; don't ask first.
- [Changelog rules](feedback_changelog_rules.md) — docks/changelog.txt: player-facing prose, reverse-chronological, a record not a manual; bump version.txt with each block; cap 10 entries per minor block, 20 per major.
- [Todo list format](feedback_todo_list_format.md) — docks/todo_list.txt: `**Finished. …` for done, `****Unfinished. …` for pending; plain-text sentences, no markdown/numbers; unfinished section on top.
- [Dock line length 1024](feedback_dock_line_length_1024.md) — keep every line in cycrz/docks/ at or under 1024 chars; the screen reader splits longer lines.
- [Dock checks use Grep](feedback_dock_checks_use_grep.md) — verify dock line length (`.{1025,}`) and CRLF (`[^\r]\n`, multiline) with the Grep tool, never a PowerShell command that prompts the dev.
- [Game message length](feedback_one_sentence_game_messages.md) — in-game spoken messages are at most THREE sentences (was one until 2026-09-30); some, like minigame errors, need more than one.
- [Menus say canceled](feedback_menus_say_canceled.md) — every menu/input escape/Back/No path speaks "canceled".
- [Yes/no menu labels](feedback_yes_no_menu_labels.md) — label items exactly Yes/No (Yes first); context goes in the prompt.
- [Multiline comment style](feedback_multiline_comment_style.md) — multi-line comments use one /* */ block, not stacked //.
- [Don't flag indentation](feedback_dont_flag_indentation.md) — AngelScript ignores indentation; don't flag whitespace or reformat.
- [No CRLF normalization](feedback_no_crlf_normalization.md) — don't run a manual normalizer; preserve each file's ending on edit; .gitattributes + git handle it.
- [Update build/version.txt](feedback_update_build_version_txt.md) — version.txt is the single source of truth; never hand-edit the generated version.nvgt.
- [Store prices after losses plan](project_loss_price_plan.md) — SHIPPED 7.2: proportional scaling of autocookie/manulcookie_purchases on loss (events, quest reroll, minigame bet losses only at round end); gains never raise; 3 sections. Also notes the negative rank reward bug.
- [Slots min_symbols 6 plan](project_slots_min_symbols_plan.md) — SHIPPED 7.2: min_symbols 10→6 (set when built; dev reverted the early edit), checklist starts with 6 checked; 3 open questions; readme line ~303.
- [Edit config for tests](feedback_edit_config_for_tests.md) — make temporary events/stores/tables edits for test steps yourself (CRLF-safe), revert as soon as the dev says the step passed.
- [Combo repeat plan](project_combo_repeat_plan.md) — SHIPPED 7.2: past the last tier the tier sounds cycle (same spacing) + optional repeat_message; multiplier holds at 16x; settings checkbox (on, after exit sound) turns repeat announcements off; 2 sections.
- [Settings vs tables](feedback_settings_vs_tables.md) — main.table numbers only; player-taste on/off goes in game settings, design switches stay in tables, cheats are removed.
- [Session stats plan](project_session_stats_plan.md) — SHIPPED 7.2: Session/Lifetime statistics screens; Copy/Export/Close on those + achievement stats (exports replace stats/*_statistics.txt in AppData); exit results screen + settings toggle; snapshot-and-diff; 4 sections.
- [Double or nothing plan](project_double_or_nothing_plan.md) — SHIPPED 7.3: rank 50 red-or-black card gamble with a suit call (4x), 2 jokers as the edge, bank between rounds; 7 sections (achievements and quests separate), docs last.
- [Boosts plan](project_boosts_plan.md) — AGREED, moved to 7.4 (2026-10-28), not built (6 sections): buy boosts in the cookie store (inventory), buy boost slots + activate like auto slots, no free slots; references surveyed; open questions listed.
