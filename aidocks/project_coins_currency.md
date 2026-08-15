---
name: project_coins_currency
description: "Internal money identifier is 'coins' (config files only); all player-facing text shows dollars/cents. Don't rename or surface 'coins' to players."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

CookieCraze's money has a split identity: the **internal identifier is `coins`** — it appears only in the `cycrz/data/config/` files (store/table price fields) as the money key — while **all player-facing output shows currency as dollars/cents** (e.g. "$1.50", "50 cents").

**Why it matters:** it's easy to assume "coins" is a separate resource or to accidentally surface the word "coins" to the player. It is neither a second currency nor a player-facing term — it's just the config key for money. This is repeatedly emphasized for modders.

**How to apply:** In config/parsers, `coins` is the money field — leave the identifier as-is. In any player-facing message, format money as dollars/cents, never as "coins." See [[project_data_driven_config]].
