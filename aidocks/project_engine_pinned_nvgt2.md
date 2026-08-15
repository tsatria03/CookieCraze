---
name: project_engine_pinned_nvgt2
description: "CookieCraze runs on the pinned legacy NVGT fork at C:\\nvgt2 (BASS audio); upstream C:\\nvgt (miniaudio) is incompatible — don't target it or suggest upgrading."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

The game is pinned to the **legacy NVGT fork at `C:\nvgt2\nvgt.exe`** (BASS audio). The launcher `cycrz/cycrz.py` and `build/tools.py` both use it. A newer stock NVGT at `C:\nvgt` (miniaudio) exists only for testing other people's games — **do not target it or treat upstream NVGT as authoritative**, and don't suggest upgrading the engine.

**Why:** CookieCraze depends on behavior specific to the nvgt2 fork; upstream NVGT changed the audio backend and other internals and is incompatible.

**Consequences to watch:**
- The interpreted relaunch (`restart()` in `cycrz.nvgt`) must run `c:\nvgt2\nvgtw.exe` with the script path `../src/cycrz.nvgt` (cwd is `cycrz/`), never bare `cycrz.nvgt` and never `c:\nvgt`.
- The nvgt2 stdlib includes live under `C:\nvgt2` and in the source mirror at `misc\Legacy-NVGT\release\include`; helper scripts are vendored into `src/includes/main/deps/` (see [[project_include_tree]]).
