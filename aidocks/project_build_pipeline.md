---
name: project_build_pipeline
description: "build/tools.py pipeline (commit tools + compile → package → release → website), the cycrz.py launcher, version mirroring, and config in tools.ini + ~/.game_tools/tools.ini."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

No test suite or linter. Running and building are scripted but manual; **the dev runs them, not Claude** ([[feedback_dont_run_or_build_the_game]]).

**Launcher — `cycrz/cycrz.py`:** runs `../src/cycrz.nvgt` under `C:\nvgt2\nvgt.exe` with cwd = `cycrz/` (the [[project_path_conventions]] cwd trick), `CREATE_NO_WINDOW`, hides its own console, and watches ~5s for an early compile-error exit → writes `cycrz/errors.txt` (+ a popup) on failure, otherwise detaches. An absent/empty `errors.txt` means "no errors." It mirrors `build/version.txt` → `src/includes/version.nvgt` before launch, preserving the file's line ending.

**Release — `build/tools.py`** (via `build/tools.bat`, or 5 flag args non-interactively). Interactive menu = commit tools (commit / undo / push / history / create-tag) + release stages:
- **compile:** mirror version into `version.nvgt`, run `nvgt -c -Q cycrz.nvgt` from `src/` (bundle lands in `src/cycrz`), copy `data`,`docks`,`sounds` from `cycrz/` into the bundle, move it to `releases/windows/CookieCraze_windows_portable_password_is_<pw>/cycrz`.
- **package:** a password-protected 7z portable archive **and** an Inno Setup installer (`build/installer.iss` via ISCC).
- **release:** `gh release create`, attaching archive + installer.
- **website:** `site_updater.ps1` updates the github.io page and writes the site's `version.txt`.

**Config:** per-repo `build/tools.ini` (`[game]` name/password/nvgt_file, `[installer]` iss/app_id/app_url/exe_name, `[site]` html/repo/path); shared tool paths (`nvgt`, `sevenzip`, `gh`, `iscc`) in `~/.game_tools/tools.ini`. Version source of truth is `build/version.txt` ([[feedback_update_build_version_txt]]).
