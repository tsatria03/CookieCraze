---
name: project_prestige_slot_plan
description: "7.5 design IN DISCUSSION (2026-10-28): prestige store upgrades for the baking slots manager and boost slot manager — feature 1 = cheaper slots (decided: two passive tracks, baking slot discount + boost slot discount); feature 2 = stronger/longer boosts (not yet discussed). Planned one feature at a time."
metadata:
  node_type: memory
  type: project
---

# Prestige store slot and boost upgrades (7.5)

Two todo items (added 2026-10-28). Dev: "sort of like the items that already exist for the other categories" — they go in the existing **Standard/Endless Head Start/Passive** categories of prestige.store. **Plan each feature one at a time** (dev).

How prestige items work today: prestige.store lines `menu:item_id:cost:cost_multiplier:min_level:hidden:infinite:amount:description`; 11 numbered one-time tiers per track in Standard (e.g. rank_discount_1..11), plus a repeatable endless_ item in Endless Passive/Head Start (unlocked at prestige 10 after clearing Standard). Effects are routed by id substring in `add_prestige_effect` (extrafuncts.nvgt) into accumulators (prestige_cookie_multiplier, prestige_rank_discount, prestige_starting_* …), reset and re-summed in `apply_all_prestige_store_upgrades`. Percent accumulators add (rank discount 1+2+… percent). Nothing slot- or boost-related exists yet.

## Feature 1 — cheaper slots

- **Decided (dev, 2026-10-28): option A, two passive tracks** — `baking_slot_discount` (auto slots, manual slots and automation all cheaper) and `boost_slot_discount` (boost slots and boost activations cheaper). 11 Standard Passive tiers each + one Endless Passive item each.
- **Head start items: YES, all three (dev, 2026-10-28):** starting auto slots (bought AND automated), starting manual slots, starting boost slots — 11 Standard Head Start tiers each + an Endless Head Start item each, like starting coins/auto cookies.
- **Discount tiers APPROVED (dev, 2026-10-28):** copy rank_discount exactly — percents 1,2,2,3,4,5,6,7,8,9,10 (57% total), costs 10,25,20,50,40,100,75,80,90,95,100 prestige points, min_level 0..10; Endless Passive item 1% per purchase (cost 1, multiplier 1.01, level 10). **Cap 90% off per track in total.** **Combines with boost discounts by multiplying** (57% prestige + Oven sale 10% → 38.7% of price).
- **Head start tiers APPROVED (dev, 2026-10-28):** costs copied from starting_autocookie (5,10,15,25,35,50,60,70,80,90,100 points, min_level 0..10); per-tier slots (they add up) — starting auto slots (bought + automated) 1,1,2,2,3,3,4,5,6,7,8 = 42; starting manual slots same = 42; starting boost slots 1,1,1,1,1,2,2,2,3,3,3 = 20; each gets an Endless Head Start item worth +1 slot per purchase (cost 1, multiplier 1.01, level 10).
- **Free starting slots do NOT raise later prices (dev, option A, 2026-10-28):** like free auto/manual cookies from events. Baking slots: grant without multiplying slotBaseCost/manualSlotBaseCost/automationBaseCost. Boost slots: the price must count BOUGHT slots separately from owned (new saved `boostSlotsBought`), since boost_slot_cost uses boostSlotCount today. Starting slots should not count toward the purchase stats either (auto/manual/boost slots purchased).

## Feature 1 build sections (APPROVED by the dev 2026-10-28 — NOT to be built yet; plan feature 2 first)

1. **Discounts** — prestige.store: `baking_slot_discount_1..11` + `boost_slot_discount_1..11` (Standard Passive), `endless_baking_slot_discount` + `endless_boost_slot_discount` (Endless Passive); accumulators prestige_baking_slot_discount / prestige_boost_slot_discount in add_prestige_effect + apply_all reset; applied (capped 90%) in calc_slot_cost(count), the manual slot prices, calc_automation_cost, boost_slot_cost, boost_activation_cost — multiplying with boost discounts. Tests: prices drop by the bought percent (temporary cheap prestige costs).
2. **Starting slots** — prestige.store `starting_auto_slots_1..11`, `starting_manual_slots_1..11`, `starting_boost_slots_1..11` (Standard Head Start) + three endless_ items; accumulators; granted in reset_game_state after the slot/boost reset (auto slots bought + automated, manual slots, boost slots) without raising prices or purchase stats; boostSlotsBought added (saved) for the boost slot price. Tests: after a prestige you start with the slots, and the next slot costs the base price.
3. **Docs** — readme prestige store section + prestige.store reference, 7.5 changelog entries, todo → Finished, memory.

## Feature 2 — stronger / longer boosts

- **Decided (dev, 2026-10-28): two passive tracks, NO head start item (no starting boosts).** `boost_power` (every boost's strength up by a percentage of itself, e.g. 20% power turns a 10% boost into 12%; cash jar pays proportionally more) and `boost_duration` (every boost runs a percentage longer, e.g. 20% turns 5 minutes into 6). 11 Standard Passive tiers each + an Endless Passive item each.
- **Tiers APPROVED (dev, 2026-10-28):** copy cookie_multiplier exactly — percents 1,2,3,5,6,10,12,15,18,20,25 (117% total), costs 5,10,15,25,35,50,60,70,80,90,100, min_level 0..10; Endless Passive item 1% per purchase (cost 1, multiplier 1.01, level 10). **Cap: any single discount boost is capped at 90% off after boost power**; no other caps (Turbo bake already has the 50 ms floor).

## Feature 2 build sections (APPROVED 2026-10-28)

1. **Boost power + duration** — prestige.store `boost_power_1..11` + `boost_duration_1..11` (Standard Passive) and `endless_boost_power` + `endless_boost_duration` (Endless Passive); accumulators in add_prestige_effect + apply_all reset; power multiplies every boost's amount (boost_multiplier_now, boost_cost_factor with the 90% discount cap, cash jar payout); duration multiplies the time set in start_boost; boost_description shows the boosted amount and length so the store and activate lists tell the truth. Tests: a boost's description and effect grow after buying tiers (temporary cheap prestige costs).

## Combined order (proposed)

Feature 1 sections 1–2, then feature 2 section 1, then ONE docs section for both (readme prestige store + prestige.store reference, 7.5 changelog entries, both todo items → Finished, memory). **APPROVED (dev, 2026-10-28) with one change: each feature gets its own changelog entry, or several if the feature warrants it** — planned: feature 1 = two entries (slot discounts; starting slots), feature 2 = **two entries (boost power; boost duration) — dev, 2026-10-28** → 7.5 block 1 → 5 of 10. Not to be built until the dev says so.
