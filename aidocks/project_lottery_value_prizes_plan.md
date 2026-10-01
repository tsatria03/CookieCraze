---
name: project_lottery_value_prizes_plan
description: "PLANNED (7.0): value-based scratch ticket prizes — item prizes (cookies, auto/manual cookies) defined by a money value and converted to items at current prices when scratched, so every ticket tier can be balanced exactly (~85% total return) at any stage of the game. Fixes exploit #5 properly. 5 sections, docs last."
metadata:
  node_type: memory
  type: project
---

# Lottery value-based prizes plan (option B)

Agreed 2026-09-30 after the first ticket fix (coin prizes only, 85% cash) turned out incomplete: cookie prizes sell for `cookieSellPrice` (default 50c, player slider 1–100c) and auto/manual cookie prizes are worth the current store price, which rises 1% per purchase (≈ $14.47 per auto cookie after 500 buys). Counting cookies at 50c, every tier still returned 170–253% (see exploit #5 in [[project_bugs_exploits_balance]]). The dev chose B over cash-only tickets to keep prize variety, and asked for it **in sections** ([[feedback_plan_features_in_memory]], one section per turn, dev tests and commits between — [[feedback_pause_after_each_fix]]). Docs last ([[feedback_docks_last]]).

## Locked design
- **New prize mode `value`.** The 5th prize field in `lottery.table` (`use_percent`, today `true`/`false`) gains a third value, `value`. Then `min_amount`/`max_amount` are a **money value in cents**. Existing `true`/`false` lines are unchanged, so modded tables keep working (field count unchanged, parse stays `parse_delimited_line(line, 7)`).
- **Conversion at scratch time:** items = floor(rolled cents ÷ unit price). Unit prices:
  - cookies → `cookieSellPrice` (what one cookie sells for),
  - auto cookies → current price of the basic `auto_cookie` single (`singles.store`: base 10c, ×1.01, amount 1, level `autocookie_purchases`) ÷ its amount,
  - manual cookies → current price of the basic `manual_cookie` single (base 25c, ×1.01, amount 1, level `manulcookie_purchases`) ÷ its amount.
  **Decided 2026-09-30 (explained to the dev):** use the basic item. In the shipped `singles.store` it is also the cheapest per unit at every stage — all items of a target share one purchase counter and the same 1.01 growth, and per-unit base prices are auto 10c (basic) vs 20c–$15 for the rest, manual 25c (basic) vs $1.50–$100. So "basic" and "cheapest" coincide. If a modder adds a cheaper-per-unit item, prizes are priced slightly high (give a bit less) — the safe direction.
- **Fallback:** if the value buys less than one item, pay the rolled value as money instead, so a prize is never empty.
- **Speed prizes stay flat** (cookiespeed is capped at 950 — [[project_cookiespeed_cap_plan]]); `coins` prizes stay flat cents.
- **Balance target:** every tier (bronze, silver, gold, diamond) returns about **85% of its price in total** (coins + value prizes), verified in Python before writing. Speed prizes are a small uncounted bonus.
- Messages keep `%amount%` (the item count actually given) and `%item%`.

## Build sections
1. **DONE (code, awaiting dev test/commit)** — `lottery_prize_item.value_mode` (bool) set by `parse_lottery_table` when the 5th field is exactly `value`; `use_percent` stays `== "true"`, so a `value` line has use_percent false. Not read anywhere yet. **Prize format.** Parser accepts `value` in the 5th field (new `value_mode` flag on `lottery_prize_item`, `use_percent` false). No table changes yet; nothing uses it, so no gameplay change.
2. **DONE (code, awaiting dev test/commit)** — `double lottery_unit_price(string target)` in `lottery_table.nvgt`: cookies → `cookieSellPrice`; autocookie/manulcookie → parses `singles.store`, finds the `auto_cookie` / `manual_cookie` item (non-percent, amount > 0), price = `buy_item(level, base, mult) / amount` with level = `autocookie_purchases` / `manulcookie_purchases` (same as the store menu), × `cookieSellPrice` if that item's menu currency is cookies (both shipped menus are coins). Returns 0 for any other target or a missing item → caller pays money. Not called yet. **Price helpers.** Functions for the unit price of a cookie / auto cookie / manual cookie at the current state (read the reference items from `singles.store`, mirroring how the store computes price). Settle the open decision above.
3. **Apply value prizes** in `apply_lottery_prize` (scratch one) and `lottery_scratch_all` (uses mid values, current prices once), with the money fallback.
4. **Rebalance `lottery.table`.** Switch item prizes in all four tiers to `value`, then tune coin + value prizes so each tier totals ~85%. Remove/rename the first attempt's flat `diamond_fortune_cookies` (or make it a value prize).
5. **Docs (last).** Readme: player ticket-shop paragraph + `lottery.table` reference for `value`. **Correct the existing 7.0 changelog entry** ("Fixed a bug where gold and diamond scratch tickets paid back more…") so it describes the real fix for all tiers. Todo line. Mark exploit #5 FIXED and this plan SHIPPED.

## Status
- First attempt (coin-only rebalance of gold/diamond to 85% cash + flat fortune prizes) is already in `lottery.table`; section 4 builds on/replaces it.
