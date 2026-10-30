---
name: project_build_pipeline
description: "build/tools.py pipeline (commit tools + compile → package → release → website), the cycrz.py launcher, version mirroring, and config in tools.ini + ~/.game_tools/tools.ini."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

No test suite or linter. Running and building are scripted but manual; **the dev runs them, not Claude** ([[feedback_dont_run_or_build_the_game]]).

**Launcher — `cycrz/cycrz.py`:** runs `../src/cycrz.nvgt` under `C:\nvgt\nvgt.exe` with cwd = `cycrz/` (the [[project_path_conventions]] cwd trick), `CREATE_NO_WINDOW`, hides its own console, and watches ~5s for an early compile-error exit → writes `cycrz/errors.txt` (+ a popup) on failure, otherwise detaches. An absent/empty `errors.txt` means "no errors." It mirrors `build/version.txt` → `src/includes/version.nvgt` before launch, preserving the file's line ending.

**Release — `build/tools.py`** (via `build/tools.bat`, or 5 flag args non-interactively). Interactive menu = commit tools (commit / undo / push / history / create-tag) + release stages:
- **compile:** mirror version into `version.nvgt`, run `nvgt -c -Q cycrz.nvgt` from `src/` (bundle lands in `src/cycrz`), copy the `ASSET_FOLDERS` list — `data`,`docks`,`lib`,`sounds` — from `cycrz/` into the bundle, move it to `releases/windows/CookieCraze_windows_portable_password_is_<pw>/cycrz`. `lib` is OPTIONAL in the copy loop (skipped when absent), but **this game ships `cycrz/lib/`** (briefly removed, restored by the dev 2026-10-25). It is **gitignored and untracked by design** — it exists only on the dev's machine, so a fresh clone lacks it. Contents as of 2026-10-25: `bass.dll`, `bass_fx.dll`, `bassmix.dll` (the legacy-fork BASS audio — [[project_engine_pinned_nvgt2]]), `nvdaControllerClient64.dll`, `SAAPI64.dll`, `zdsrapi.dll` (screen-reader bridges), `phonon.dll` (HRTF). **No `Tolk.dll`, on purpose:** the engine's `C:\nvgt\config.properties` sets `build.windows_bundle=1` and `build.shared_library_excludes=plist TrueAudioNext GPUUtilities systemd_notify sqlite git2 curl opus bassflac bassmidi unicode Tolk`, so `nvgt -c` leaves those libraries out of every bundle ([[project_bugs_build_docs]] #19). That file is engine-wide config outside the repo; check it first when a DLL seems missing from a build. `sounds/` is tracked in git (commit 5eeba37).
- **package:** a password-protected 7z portable archive **and** an Inno Setup installer (`build/installer.iss` via ISCC).
- **release:** `gh release create`, attaching archive + installer.
- **website:** `site_updater.ps1` updates the github.io page and writes the site's `version.txt`. The page is `misc/tsatria03.github.io/projects/games/CookieCraze/index.html` (sibling of the `games/` folder); it shows only the release-tag form `V<version>0` (e.g. V6.80) in its headings and download/release URLs, which `tools.py` builds as `f"V{version}0"`.

**Release checklist for the config pack (dev, 2026-10-26, 7.2+):** tools.py does not build `config.crz`, it just copies the whole `cycrz/data` folder. Before compiling a release: in game, main menu → miscellaneous menu → compile data config; then compile. **Since 2026-10-29 tools.py handles the rest (dev request):** before compiling it refuses with a warning if `cycrz/data/decompiled` exists but `data/compiled/config.crz` does not ("make the pack first"); after copying assets it deletes `data/decompiled` from the BUNDLE only (repo loose files untouched), so no manual delete / git restore is needed. Constants CONFIG_LOOSE / CONFIG_PACK. **Stale pack check (added same day, dev asked):** stale_config_files() lists loose files with a modified time newer than config.crz; if any, the compile step names up to 10 and asks "Compile anyway with the older pack?" (non-interactive mode refuses). Compares modified times only, so a decompile in game, a git checkout/restore, or re-saving a file unchanged also triggers it — recompiling the pack clears it. [[project_config_pack_plan]].

**Config:** per-repo `build/tools.ini` (`[game]` name/password/nvgt_file, `[installer]` iss/app_id/app_url/exe_name, `[site]` html/repo/path); shared tool paths (`nvgt`, `sevenzip`, `gh`, `iscc`) in `~/.game_tools/tools.ini`. Version source of truth is `build/version.txt` ([[feedback_update_build_version_txt]]).
