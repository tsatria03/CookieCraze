# CookieCraze memory index

The `[[name]]` links in `CLAUDE.md` and across these memories resolve to `aidocks/<name>.md`. Add a one-line pointer here for every new memory. "Memory" / "memories" always means this folder. (Repo folder is CookieCraze; source/asset folder is `cycrz`; README titles it "CookyCraze".)

## Project — what the game is and how it's built
- [Game vision](project_game_vision.md) — audio idle/incremental cookie clicker: bake → sell → upgrade → rank up → automate → prestige, with rank-gated minigames; the load-bearing systems.
- [Coins currency](project_coins_currency.md) — internal money identifier is `coins` (config only); all player-facing text shows dollars/cents; don't rename or surface "coins".
- [Message buffers](project_message_buffers.md) — categorized message-buffer system (achievements/combos/critical/events/general/…), each mutable + exportable, for non-interruptive announcements.
- [Data-driven config](project_data_driven_config.md) — cycrz/data/config/ (events/stores/tables) is plain-text modder-tunable; parsers in main/parsers/ load it; docks/readme.txt is the format reference.
- [Path conventions](project_path_conventions.md) — src/ (code) + cycrz/ (assets+launcher) + build/ + releases/ split; the cwd=cycrz/ trick; no #pragma asset (tools.py copies assets).
- [Include tree](project_include_tree.md) — src/includes/ = version.nvgt + main/{deps,functions,globals,menus,parsers}; wildcard glob aggregation; vendored stdlib helpers incl. dget + rotation.
- [Build pipeline](project_build_pipeline.md) — cycrz.py launcher + build/tools.py (commit tools + compile→package→release→website), version mirroring, tools.ini + ~/.game_tools/tools.ini config.
- [Audio model](project_audio_model.md) — sound_pool + HRTF; cycrz/sounds/ layout (ambience/menu/misc/dlg/store/combos/events/minigames/buffer); no sound packs.
- [Save-data layout](project_save_data_layout.md) — writable data in AppData under tsatria03/CookieCraze/ (logs/preffs/saves); multiple save slots.
- [Repo hygiene](project_repo_hygiene.md) — .gitattributes CRLF enforcement + binary rules; what's gitignored; CLAUDE.md + aidocks/ are committed.
- [Engine pinned to nvgt](project_engine_pinned_nvgt2.md) — runs on the legacy fork at C:\nvgt (BASS); upstream C:\nvgt2 (miniaudio) is incompatible; don't target it or suggest upgrading.
- [Prestige store schema](project_prestige_store_schema.md) — prestige.store item_id suffixes count up in prestige-level order (1..11 per track); save schema key + `migrate_prestige_id_v2` migration protects already-purchased ids. Renumbered in 6.3.
- [Endless prestige plan](project_endless_prestige_plan.md) — SHIPPED 6.7. Infinite/compounding prestige upgrades: 4 flat categories (Standard/Endless × Passive/Head Start), base 1 × mult 1.01 (retuned from 5 × 1.1 to match the singles shop), per-item counts, unlock after clearing Standard at prestige 10. Field is named `infinite` in code/config. Kept as design record.
- [Cookie XP rework plan](project_cookie_xp_rework_plan.md) — SHIPPED 6.7. Ranks on a dedicated cumulative `cookieXP` (SimpleFighter-style) so selling no longer costs rank progress; the two rank sliders are now a Cookie Rank Modifier (threshold) + Cookie Experience Modifier (`cookieExpMod`, XP per bake); both mods default 1 (retuned from 5 in 6.8); `difmod` removed, threshold formula uses constant 4 (retuned from 10 in 6.8); migration seeds cookieXP from the current rank's threshold. Kept as design record.
- [Number overflow cap](project_number_overflow_cap.md) — shipped 6.3: stop `Inf`/`$inf` by clamping the growing balances + `stat_*` totals to 1e308 (via `floor(safe_cap(x))` normalizers, a loop clamp, and capped display locals), not just cost formulas.

## How-to guides
- [Minigame build guide](project_minigame_build_guide.md) — repeatable steps to add a new minigame: blueprint first, plan sections, code one section per turn and wait for commit, docks last; full file/function wiring map.

## Ideas / backlog
- [Feature ideas](project_feature_ideas.md) — 6.4+ wish list (offline progress, golden-cookie bonus, daily streak, auto-buyer, boosts shop, QoL) + future-minigame candidates (mines, video poker, keno, audio Simon). Higher or lower + number-format shipped in 6.4; achievement rewards + roulette shipped in 6.5, roulette's rank-shift + tweaks changelogged in 6.6; rest unbuilt.

## NVGT / AngelScript gotchas — these cause compile failures (game won't launch)
- [AngelScript braceless if](project_angelscript_braceless_if.md) — a braceless if/else governs one statement; a second orphans the else → compile error.
- [AngelScript reserved words](project_angelscript_reserved_words.md) — never name a variable `out` (or in/inout/shared/final/from…); reserved keyword → compile error.
- [NVGT key_pressed one-shot](project_nvgt_key_pressed_oneshot.md) — key_pressed() is consumed on first read each frame; read a multi-purpose key once and branch inside.
- [NVGT sound preload cache](project_nvgt_sound_preload_cache.md) — sound.load caches by filename; reusing a name for changed audio replays the old clip; use a fresh name or allow_preloads=false.

## Feedback — how the dev wants you to work
- [Confirm before implementing](feedback_confirm_before_implementing.md) — a design discussion or any `?` is a request for a plan, not a green light to edit; wait for explicit go-ahead.
- [Plan features in memory first](feedback_plan_features_in_memory.md) — before coding any new feature, write the agreed plan (locked decisions + numbered build sections) into an aidocks/ memory file and index it; only then build, section by section.
- [Ask one question at a time](feedback_ask_one_question_at_a_time.md) — surface ONE question per turn and wait; don't batch a numbered list.
- [Ignore bang commands](feedback_ignore_bang_commands.md) — ignore the user's in-session `!` command runs and their output; act only on the user's typed prose unless they explicitly reference them.
- [List modified files](feedback_list_modified_files.md) — end every editing turn with a bare-filename "Files changed:" list; then note whether a relaunch is needed.
- [Don't run or build the game](feedback_dont_run_or_build_the_game.md) — never launch/compile (cycrz.py, tools.py, nvgt -c); edit and report, the dev runs and verifies.
- [Verify code while fixing](feedback_verify_code_while_fixing.md) — re-locate by symbol not line number, confirm the finding is true, flag adjacent bugs.
- [Check git log for commits](feedback_check_git_log_for_commits.md) — the dev commits between turns; check git log/status before assuming commit state; don't commit unless asked.
- [Stage commits before big changes](feedback_stage_commits_before_big_changes.md) — flag a commit break point before a risky stage so safe pieces land first.
- [CLAUDE.md length limit](feedback_claudemd_length.md) — keep CLAUDE.md a dispatcher under 40,000 chars; move detail into memory files.
- [Docks last](feedback_docks_last.md) — in any multi-section build plan, reserve the final section for dock updates (readme + changelog + version); code/config first, docs last.
- [Changelog rules](feedback_changelog_rules.md) — docks/changelog.txt: player-facing prose, reverse-chronological, a record not a manual; bump version.txt with each block; cap 10 entries per minor block, 20 per major.
- [Todo list format](feedback_todo_list_format.md) — docks/todo_list.txt: `**Finished. …` for done, `****Unfinished. …` for pending; plain-text sentences, no markdown/numbers; unfinished section on top.
- [Dock line length 1024](feedback_dock_line_length_1024.md) — keep every line in cycrz/docks/ at or under 1024 chars; the screen reader splits longer lines.
- [One-sentence game messages](feedback_one_sentence_game_messages.md) — in-game spoken feedback is exactly one sentence; no trailing advice.
- [Menus say canceled](feedback_menus_say_canceled.md) — every menu/input escape/Back/No path speaks "canceled".
- [Yes/no menu labels](feedback_yes_no_menu_labels.md) — label items exactly Yes/No (Yes first); context goes in the prompt.
- [Multiline comment style](feedback_multiline_comment_style.md) — multi-line comments use one /* */ block, not stacked //.
- [Don't flag indentation](feedback_dont_flag_indentation.md) — AngelScript ignores indentation; don't flag whitespace or reformat.
- [No CRLF normalization](feedback_no_crlf_normalization.md) — don't run a manual normalizer; preserve each file's ending on edit; .gitattributes + git handle it.
- [Update build/version.txt](feedback_update_build_version_txt.md) — version.txt is the single source of truth; never hand-edit the generated version.nvgt.
