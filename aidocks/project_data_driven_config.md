---
name: project_data_driven_config
description: "cycrz/data/config/ (events/, stores/, tables/) is plain-text, modder-tunable; parsers in main/parsers/ load it. readme.txt is the format reference."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

Almost all of CookieCraze's balance is **data-driven** via plain-text files under `cycrz/data/config/`, so ranks, shops, minigames, quests, events, and prestige can be tuned without touching code. The loaders live in `src/includes/main/parsers/` (one parser per file type).

Config layout:
- **`config/tables/`** — `ranks.table`, `achievements.table`, `quests.table`, `combos.table`, `slots.table`, `jacks.table` (blackjack), `dice.table`, `lottery.table`, `prestige.table`. Loaded by the matching `*_table.nvgt`.
- **`config/stores/`** — `singles.store`, `bundles.store`, `tickets.store`, `prestige.store`. Loaded by `single_store.nvgt` / `bundle_store.nvgt` / `ticket_store.nvgt` / `prestige_store.nvgt`.
- **`config/events/`** — `baker.event` (main baking events) and `flipper.event` (cookie-flipper minigame events). Loaded via the event parsers.

The player-facing **`cycrz/docks/readme.txt` is the authoritative modding/format reference** — keep it in sync when you change a parser's accepted fields, and mind the dock line limit ([[feedback_dock_line_length_1024]]). Watch the currency naming when editing store/table prices — [[project_coins_currency]]. Repo-root `README.md` is just a stub; the real readme is the dock.
