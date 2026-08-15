---
name: project_save_data_layout
description: "Writable user data lives in AppData under tsatria03/CookieCraze/ (logs/preffs/saves); multiple independent save slots."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

All writable player data is stored **absolutely** under `DIRECTORY_APPDATA + "tsatria03/CookieCraze/"`, so it's independent of the src/asset layout ([[project_path_conventions]]). `main()` creates the dirs at startup:

- `preffs/` — settings + saved profile (player first/last name, gender, options).
- `saves/` — save-slot game state; CookieCraze supports **multiple independent save slots**.
- `logs/` — exported message-buffer logs (see [[project_message_buffers]]).

**Shipped read-only data** stays in the bundle: `cycrz/data/config/` (the modding tables/stores/events) and `cycrz/sounds/`. Don't confuse the two: `data/`/`sounds`/`docks` are cwd-relative read-only assets; the AppData tree is the read/write user state. Version is read from `src/includes/version.nvgt` at runtime (the old `docks/version.txt` read was removed).
