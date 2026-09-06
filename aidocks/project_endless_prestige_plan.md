---
name: project_endless_prestige_plan
description: "Approved plan for endless (repeatable, compounding) prestige-store upgrades — 4 flat categories, per-item purchase counts, unlock after clearing Standard at prestige 10; build sections listed."
metadata:
  node_type: memory
  type: project
  originSessionId: 6e820f7e-c923-43e9-bd1c-7c123f031f23
---

**STATUS: SHIPPED in 6.7.** All six build sections completed, documented, and committed. This file is kept as the design record.

Endless prestige upgrades: a repeatable endgame sink so leftover prestige points always have somewhere to go after the finite store is cleared. Extends the prestige store ([[project_prestige_store_schema]]). Approved 2026 (targeting a 6.7+ block). The dev commits each section; code/config first, docks last ([[feedback_docks_last]]).

## Locked design decisions

- **Four flat categories** (no submenu support needed — reuse the existing flat prestige-menu system):
  - **Standard Passive** — existing one-time set (cookie mult, coin mult, rank discount). Only a display rename; alias `passivemenu` stays.
  - **Standard Head Start** — existing one-time set (starting coins / autocookie / manualcookie). Alias `headstartmenu` stays.
  - **Endless Passive** — repeatable `endless_cookie_multiplier` + `endless_coin_multiplier`. **No rank discount** (it caps near 100%, can't be endless).
  - **Endless Head Start** — repeatable `endless_starting_coins` / `endless_starting_autocookie` / `endless_starting_manualcookie`.
- **Naming:** the non-endless side is **"Standard"** (NOT "temporary" — those upgrades are permanent; "temporary" would mislead and collide with the real temporary-prestige concept).
- **Cost = compounding, not flat.** Flat + endless = runaway (constant value-per-point). Compounding gives automatic diminishing returns via the existing `calc_total_upgrade_cost` geometric-sum helper.
  - **base_cost = 5 points, cost_multiplier = 1.1** (locked). Accessible early, meaningfully climbs (5 → ~12–20 by the tenth → ~500+ by the fiftieth). Standard items use cost_multiplier = 1 so their math collapses to today's flat one-time price.
- **Effect per purchase (amount field):**
  - Endless multipliers: **+1%** each (matches the small-percent stacking of the Standard passives).
  - Endless head start, grounded in the Standard level-10 top-tier amounts: **+10000 (¢ = +$100.00) coins, +150 auto cookies, +150 manual cookies** per purchase. (Standard, all tiers bought, totals $383.50 / 689 / 689 as the base these stack on.)
- **Unlock gate (STRICTER rule enforced):** Endless categories require BOTH prestige level 10 (menu `min_level=10`) AND every Standard upgrade already purchased. Add an `all_standard_prestige_purchased()` check.

## Config format change

New prestige item format (backward compatible — parser distinguishes by field count):
`menu:item_id:base_cost:cost_multiplier:min_level:hidden:repeatable:amount:description` (9 fields / split on first 8 colons).
Old 7-field lines (`menu:id:cost:min_level:hidden:amount:description`) still parse → default cost_multiplier=1, repeatable=false.

Effect type is still encoded in the item_id prefix (`cookie_multiplier` / `coin_multiplier` / `rank_discount` / `starting_coins` / `starting_autocookie` / `starting_manualcookie`), matched by `apply_prestige_store_upgrade` + the recompute function in extrafuncts.nvgt. Endless ids reuse those prefixes (e.g. `endless_cookie_multiplier` — note the prefix substring match must still catch it, so either keep the recognizable substring in position or match on the meaningful token).

## Build sections (commit-safe order — one section per turn, pause for commit)

1. **Parser** — `prestige_store.nvgt`: add `double cost_multiplier` + `bool repeatable` to `prestige_store_item`; extend split to 8 colons; branch on `parts.length()` so old 7-field lines still parse with defaults. (Parser first so the current config keeps working before the rewrite.)
2. **Config** — `cycrz/data/config/stores/prestige.store`: rewrite all lines to the 9-field format; rename the two menus' display names to "Standard Passive" / "Standard Head Start"; add "Endless Passive" + "Endless Head Start" menus (`min_level=10`) and their repeatable items (base 5, mult 1.1, amounts above).
3. **State + save** — `dec.nvgt`: add `dictionary prestigeEndlessCounts` (item_id → count). `savefuncts.nvgt`: save/load it alongside `prestigePurchased`; reset on new game / settings reset. Mind the existing migration ([[project_prestige_store_schema]]).
4. **Effects** — `extrafuncts.nvgt`: in the recompute function add `amount × count` for repeatable items (standard still += amount once); update `apply_prestige_store_upgrade` for the repeatable case; add `all_standard_prestige_purchased()` helper.
5. **Menu / purchase** — `menu.nvgt` (`prestige_store_category_menu`): endless items skip the already-bought gate; show "you own N, next costs X, buy how many?"; quantity prompt like the singles shop — cost via `calc_total_upgrade_cost(count, qty, base_cost, cost_multiplier)`, default max via `calc_max_buyable`; deduct `prestige_points` (compare as double so big counts don't overflow the int cost field); bump count; apply amount×qty. Gate the two Endless menus on prestige 10 AND `all_standard_prestige_purchased()`.
6. **Docks** — readme (document endless upgrades + the new 9-field config format), changelog entry, `build/version.txt` bump.

## Tuning knobs (all config, retune anytime)
base_cost 5 · cost_multiplier 1.1 · +1% per multiplier buy · head start +10000¢ / +150 / +150.
