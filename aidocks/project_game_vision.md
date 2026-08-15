---
name: project_game_vision
description: "CookieCraze is an audio idle/incremental cookie clicker: bake → sell → upgrade → rank up → automate → prestige, with rank-gated minigames."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

CookieCraze is an audio-only **idle/incremental "cookie clicker."** Core loop: manually bake cookies → sell for money → buy upgrades → rank up → unlock features → automate production → prestige → repeat, progressively until cookies bake themselves.

**Load-bearing systems (the game's identity):**
- **Ranks** (`ranks.table`): money + milestone stat rewards; ranks gate features.
- **Shops:** a singles shop, a bundle shop (prices off the singles), and a ticket shop; compounding cost scaling; categories spend money or cookies.
- **Automation:** auto-baking plus a baking-slots manager (auto + manual slots).
- **Rank-gated minigames:** blackjack, cookie flipper, cookie lottery/scratch tickets, dice roller, slot machine (unlocked at ascending ranks).
- **Combos:** timed consecutive manual presses multiply output.
- **Random events:** baker events + separate flipper events.
- **Meta progression:** 200+ achievements, quests (difficulty-based assignment + rerolls), and a full **prestige** system with a prestige-points store.
- **UX:** several categorized message **buffers** with mute/export ([[project_message_buffers]]); multiple save slots. Single-player, single-instance.

Nearly every system is **data-driven** and modder-tunable — see [[project_data_driven_config]]. **Currency quirk:** the internal money identifier is `coins` (config-only), but all player-facing text shows dollars/cents — [[project_coins_currency]]. It's a solo hobbyist project (long changelog, v2.7→6.x); expect occasional spelling quirks.
