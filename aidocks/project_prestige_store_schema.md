---
name: project_prestige_store_schema
description: "prestige.store item_id suffixes must count up in prestige-level order (1..11 per track); a save schema key + migration protects already-purchased ids. Renumbered in 6.3."
metadata:
  node_type: memory
  type: project
---

# Prestige store id ordering + save schema

`cycrz/data/config/stores/prestige.store` ([[project_data_driven_config]]) lists prestige upgrades in `; Prestige level N` blocks, ascending by unlock level, six tracks per block: `cookie_multiplier`, `coin_multiplier`, `rank_discount`, `starting_coins`, `starting_autocookie`, `starting_manualcookie`.

**Convention (set in 6.3):** each track's `item_id` numeric suffix must count up **in step with the prestige level** — level 0 → `_1`, level 1 → `_2`, … level 10 → `_11`. The player-facing label is built straight from the id (`menu.nvgt` does `string_replace(item_id, "_", " ")`), so a scrambled suffix reads as e.g. "cookie multiplier 6" at level 2. Before 6.3 the suffixes were out of order (tiers `_6`–`_11` had been inserted between the original `_1`–`_5`); the 6.3 renumber fixed the ordering. **When adding a new tier, insert it at the right level and keep the suffixes contiguous** — don't append with the next free number.

**Why the suffix is safe to renumber:** bonus logic keys off the id *prefix* only (`apply_prestige_store_upgrade` / `apply_all_prestige_store_upgrades` in `extrafuncts.nvgt` match `cookie_multiplier` etc. by `substr`, then add the item's `amount`). The suffix is just an identity/label token.

**Save migration:** purchased upgrades are stored in `prestigePurchased` as **item_id strings** (`savefuncts.nvgt`). Renumbering would repoint old ids at different tiers, so a schema guard protects saves: `writedata` writes `prestigeStoreSchema = 2`; `readdata` reads it (absent = 1 = pre-6.3) and, if < 2, remaps each purchased id via `migrate_prestige_id_v2()` (old suffix → new suffix). Migration always runs on freshly-disk-loaded ids, so it's safe under Ctrl+L reload and idempotent once a save writes schema 2. **Any future prestige.store id renumber needs a new schema bump + migration step.**

**Bug fixed alongside (6.3):** the `starting_autocookie` / `starting_manualcookie` prefix checks used `substr(0,17)` / `substr(0,19)` but those prefixes are 19 / 21 chars, so the comparisons were never true and those two upgrades applied no bonus. Corrected to `substr(0,19)` / `substr(0,21)`. Watch these lengths if the prefix-match pattern is ever copied for a new track.
