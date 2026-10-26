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
- **`config/tables/`** — `ranks.table`, `achievements.table`, `quests.table`, `combos.table`, `slots.table`, `jacks.table` (blackjack), `dice.table`, `highlow.table`, `roulette.table`, `lottery.table`, `prestige.table`, and (7.2) **`main.table`**. Loaded by the matching `*_table.nvgt`.
  - **`main.table`** ([[project_main_table_plan]]) holds the former game-settings "cheat" sliders and the run starting values: `[starting_values]` starting_cash / starting_autocookie / starting_manualcookie, `[slots]` three base costs + three cost multipliers, `[ranks]` rank_modifier / experience_modifier, `[events]` auto_event_chance / manual_event_chance (percent of bakes, 0 = off), `[selling]` sell_price. **Money values are in dollars** (converted to cents by `dollars_to_cents`), unlike prices elsewhere in config, which are in cents. Every value is optional and falls back to its default; its headers are organisational only. These settings are no longer in the game settings menu or gamesets.crz.
- **`config/stores/`** — `singles.store`, `bundles.store`, `tickets.store`, `prestige.store`. Loaded by `single_store.nvgt` / `bundle_store.nvgt` / `ticket_store.nvgt` / `prestige_store.nvgt`.
- **`config/events/`** — `baker.event` (main baking events) and `flipper.event` (cookie-flipper minigame events). Loaded via the event parsers.

**Config files hold data only — no readme-style explanatory comments (dev, 2026-10-26).** All format/meaning documentation lives in the readme (an old changelog entry: "Moved all configuration file documentation out of the config files and into the readme"). The only comments allowed are the existing short `; Required header — removing or renaming this will break the file.` warnings under functional headers, and short `;` group labels like `; Random pool`. main.table was first shipped with explanatory comments and the dev had them stripped — don't add any to new or existing config files.

**Readme file-reference style (dev, 2026-10-26 — match it for any new config file):** the references are grouped events → stores → tables (main.table sits first in the tables group, right before achievements.table). Each reference: the file name alone on a line, blank, `Location: data/config/…`, blank, a "Defines …" paragraph; then per section a sub-heading like `Settings section.` / `Tiers section.` (no square brackets); then each setting as its **name alone on one line, its description on the next line**, blank line between settings, with defaults phrased "Defaults to X". Never "name = description" on one line.

The player-facing **`cycrz/docks/readme.txt` is the authoritative modding/format reference** — keep it in sync when you change a parser's accepted fields, and mind the dock line limit ([[feedback_dock_line_length_1024]]). Watch the currency naming when editing store/table prices — [[project_coins_currency]]. Repo-root `README.md` is just a stub; the real readme is the dock.
