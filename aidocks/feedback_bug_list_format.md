---
name: feedback_bug_list_format
description: "When listing bugs to the dev, split them into player-facing vs code (internal/modder/build), then group each by game section (saves, ranks, quests, lottery…), one line per bug with its memory-file id."
metadata:
  node_type: memory
  type: feedback
---

When listing bugs to the dev, use two top-level categories, then group by section inside each:

1. **Player-facing** — something a player can notice in play (wrong payout, lost progress, wrong stat, odd speech), even if only late game.
2. **Code** — internal defects, modder traps (config parsing), latent risks, and build/release tooling.

Inside each category, group by game section (for example Saves and Control L, Ranks, Quests and prestige, Lottery, Minigames, Store, Keys, Docs; and for code: Save/load, Numeric, Control flow, Config parsers, Vendored deps, Build/release). One line per bug, with its id from the memory files (code #9, exploit #7, build #2) so it can be found in [[project_bugs_code]], [[project_bugs_exploits_balance]], [[project_bugs_build_docs]], [[project_bugs_player_facing]].

**Why:** the dev asked for this on 2026-10-25 after receiving bug lists as flat, mixed lists in severity order; grouping makes it easy to see which area each bug lives in and whether players are affected.

**How to apply:** any time you report, summarize or list bugs (audits, re-checks, "what's left"). Leave out fixed and won't-fix items unless asked. The memory files themselves keep their own numbering; this is about how lists are presented.
