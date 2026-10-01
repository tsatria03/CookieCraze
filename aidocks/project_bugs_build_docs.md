---
name: project_bugs_build_docs
description: "Bug audit 2026-09-30 (v6.8): build/release pipeline bugs (site updater x.0 regex, tools.py release deletion + import-time KeyError, hardcoded paths, gitignore gaps, installer mutex), player-doc inaccuracies and typos, stale memory notes. Unfixed unless marked."
metadata:
  node_type: memory
  type: project
---

# Build, release and docs bugs (audit of 6.8, 2026-09-30)

Companion to [[project_bugs_player_facing]], [[project_bugs_exploits_balance]], [[project_bugs_code]]. Pipeline context: [[project_build_pipeline]]. Mark **FIXED (version)** when shipped.

## Build / release (medium)

1. **Site updater can't match a version ending in 0.** `build/site_updater.ps1` ~2 uses `V\d+\.\d+(?<!0)`; the x.0 release itself updates fine, but from the NEXT release on, the title (e.g. V7.0) is never replaced again and stays stale forever (details in [[project_release_status]]). Fix: replace exact old strings, or drop the lookbehind and use anchored patterns.
2. **`tools.py` crashes at import if `~/.game_tools/tools.ini` is missing** — `_tools["tools"]` lookups at ~30-33 raise `KeyError`, so even the commit menu won't open. Fix: `.get()` + a friendly error only when a release stage runs.
3. **`tools.py` deletes the GitHub release before creating the new one** (~507-512) and ignores return codes of `git tag -f` / `push -f`. If `gh release create` fails the old release is gone. Fix: check return codes; create first then delete, or `gh release upload --clobber`.
4. **Hardcoded engine/python paths** — `cycrz/cycrz.py` ~19 `C:\nvgt\nvgt.exe`, `src/cycrz.nvgt` `restart()` `c:\nvgt\nvgtw.exe`, `build/tools.bat` ~3 per-user Python path. tools.py reads nvgt from `~/.game_tools`, so a build can use a different engine than dev test runs. Fix: one shared config value; `py -3` in the bat. (Engine must stay the legacy fork — [[project_engine_pinned_nvgt2]].)

## Build / release (low)

5. **`cycrz.py` `COMPILE_WAIT = 5`** — a compile error after 5 s, or a runtime throw, goes to a temp log nobody reads; one log leaks per launch. Fix: configurable wait or poll the log for "Compilation error".
6. **`origin/HEAD..HEAD`** (`tools.py` ~84, ~164) fails when `origin/HEAD` isn't set → menu shows 0 unpushed commits. Fix: `@{u}..HEAD`.
7. **`.gitignore` gaps** — `cycrz/errors.txt` and a leftover `src/cycrz/` bundle folder aren't ignored (confirmed with `git check-ignore`); `do_commit` runs `git add -A`, so a failed launch/compile gets committed. Fix: ignore both. See [[project_repo_hygiene]].
8. **`sounds/` and `lib/` are gitignored** (`.gitignore` ~7-8), so a fresh clone has no audio or BASS/Tolk DLLs and can't be played or built. Likely intentional — document it.
9. **Installer mutex mismatch** — `build/installer.iss` ~21 `AppMutex=CookieCraze_Mutex` doesn't match the game's `crc32(NVGT_VERSION_COMMIT_HASH + "cycrz")` (`deps/instance.nvgt` ~17), so the installer can't detect a running game. Fix: remove the line or give the game a matching named mutex.
10. Informational: the `tools.ini` password isn't secret (appears in every published filename).

## Player docs (`cycrz/docks/`)

11. **FIXED (2026-09-30)** — readme said Shift+Backslash exports all buffers; it now documents the intentional per-buffer export — see [[project_bugs_player_facing]] #12.
12. **Minigame status keys (C, A, S, M, O) undocumented**, and M conflicts with the main game — see [[project_bugs_player_facing]] #13.
13. **Readme blackjack `win_multiplier` description** ("2 means a $1 bet returns $2 total", ~1279) disagrees with the code (returns 3×) — fix the code, not the doc ([[project_bugs_exploits_balance]] #2).
14. **No quick start for new blind players.** The keyboard reference starts ~line 502 of 1841; ~70% of the file is modder reference. Missing: first-run profile setup, how to reach/press the Bake button (its Alt+B accelerator from `&Bake` is never mentioned), menu arrow keys. Fix: short "Getting started" + "Keys" at the top; move config reference below or to its own file.
15. **FIXED (2026-09-30) — all dock typos from the audit:** changelog "truely" → "truly" (6.8) and "BUG WHERE" → "bug where" (6.6); `credits.txt` "feadbacks were" → "feedback was"; and all `todo_list.txt` typos — "Ffinished" (broke the prefix), "quikly", "hier", "sertain", "anouncements". Also "brows" → "browse" in the prestige store intro (`menu.nvgt` ~835, in-game text).
16. Verified OK at audit: no dock line > 1024 chars ([[feedback_dock_line_length_1024]]); changelog blocks within caps and newest-first ([[feedback_changelog_rules]]); version matches `build/version.txt` (6.8); rank unlocks 10–90, 50-cent sell default, 8 buffers, Alt+F4, F1–F4, L/P/R/M/C/F/J all match the code.

## Stale memory notes

17. **FIXED (2026-09-30)** — [[project_build_pipeline]] said `cycrz/lib/` is absent; it exists (BASS, Tolk, phonon…) and tools.py bundles it. Memory updated with the DLL list.
18. **FIXED (2026-09-30)** — [[project_feature_ideas]] said roulette sounds were still owed; all exist (`spin`, `land`, `bet1-3`, `win`, `lose`, `break`). Also annotated higher or lower's draft sound names with the shipped ones.
