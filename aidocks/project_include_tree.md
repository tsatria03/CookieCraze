---
name: project_include_tree
description: "src/includes/ layout: version.nvgt + main/{deps,functions,globals,menus,parsers}, wildcard glob aggregation, vendored stdlib helpers (incl. dget and rotation)."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

Entry `src/cycrz.nvgt` does `#include"includes/includes.nvgt"`. `src/includes/` holds `includes.nvgt` (aggregator) and `version.nvgt` at the top level; everything else lives under **`main/`**:

- **`main/deps/`** — vendored stdlib/helper scripts + shared UI: `bgt_compat`, `instance`, `keyhook`, `sound_pool`, `rotation`, `dget`, `form`, `speech`, `custom_menu`, `dlg`, `input_forms`, `buffer`, `savedata`, `virtual_dialogs`.
- **`main/functions/`** — `extrafuncts`, `savefuncts`.
- **`main/globals/`** — `dec` (globals), `game`, `minigames`, `baker_events`, `flipper_events`.
- **`main/menus/`** — `menu`.
- **`main/parsers/`** — the data-driven loaders: `ranks_table`, `single_store`, `bundle_store`, `ticket_store`, `prestige_table`, `prestige_store`, `achievements_table`, `quests_table`, `combos_table`, `slots_table`, `jacks_table` (blackjack), `dice_table`, `lottery_table` (see [[project_data_driven_config]]).

**Aggregation is by wildcard glob.** `includes.nvgt` is `#include"version.nvgt"` then `#include"main/deps/*"`, `#include"main/functions/*"`, `#include"main/globals/*"`, `#include"main/menus/*"`, `#include"main/parsers/*"`. A new file in a `main/<subdir>/` is auto-included — no wiring. There are **no bare stdlib includes and no `#pragma asset/document`** lines (the stdlib helpers are vendored into `main/deps/` and picked up by the deps glob).

**Vendoring gotcha:** a vendored helper's own `#include` resolves against its folder (`main/deps/`), NOT the engine include path. `sound_pool.nvgt` does `#include "rotation.nvgt"` and needs `rotation.nvgt` (defines `pi`/`calculate_theta`) present in `main/deps/` — it's vendored there. `dget.nvgt` (the dictionary helper — `dgets`/`dgetn`/`dgetb`/`dgetsl`) is a real CookieCraze dependency (used in `cycrz.nvgt` and elsewhere), also vendored in deps. If you vendor another engine helper, bring its transitive includes too. Engine: [[project_engine_pinned_nvgt2]].
