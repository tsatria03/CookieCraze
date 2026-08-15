---
name: project_path_conventions
description: "The src/ (code) + cycrz/ (assets+launcher) + build/ + releases/ split, the cwd=cycrz/ trick, and how in-code paths resolve."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

CookieCraze separates code from runtime assets into top-level folders (the SimpleFighter layout). Note the naming quirk: the repo folder is **CookieCraze**, but the source file and asset folder are **`cycrz`** (and the README titles it "CookyCraze").

- **`src/`** — code only. Entry `src/cycrz.nvgt`, plus `src/includes/` (`includes.nvgt`, `version.nvgt`, and the `main/` subtree — see [[project_include_tree]]). No assets here.
- **`cycrz/`** — runtime assets + the launcher: `cycrz/cycrz.py`, `cycrz/data/config/`, `cycrz/docks/`, `cycrz/sounds/`.
- **`build/`** — the build/release pipeline (`tools.py`, `tools.ini`, `version.txt`) — see [[project_build_pipeline]].
- **`releases/`** — compiled output + archives (gitignored).

**The cwd trick (the key mechanism):** `cycrz/cycrz.py` runs `../src/cycrz.nvgt` through `C:\nvgt2\nvgt.exe` but sets **cwd = `cycrz/`**. So two path classes coexist in the code:
- `#include"includes/..."` resolves relative to the **script** → `src/includes/`.
- bare `sounds/...`, `data/...`, `docks/...` strings resolve relative to **cwd** → `cycrz/`.

**No in-code asset path needs to change** for the split — only the launcher's cwd (runtime) and `tools.py`'s asset-copy (build) know about it. There are deliberately **no `#pragma asset`/`#pragma document`** lines (nvgt2 resolves those against the output dir, which was brittle); `tools.py` copies `data/docks/sounds` into the compiled bundle instead.

**Writable user data** is absolute, unaffected by the split: `DIRECTORY_APPDATA + "tsatria03/CookieCraze/..."` (logs/preffs/saves) — see [[project_save_data_layout]].
