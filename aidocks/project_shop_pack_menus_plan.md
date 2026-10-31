---
name: project_shop_pack_menus_plan
description: "IDEA RECORDED (2026-10-30), not approved or scheduled: overhaul the buying shops to use pack menus like the 7.7 sell cookies menu — per item, Buy 1 / 5 / 10 ... / Buy max with real prices, buying instantly — one store at a time. Expect several changelog entries and a big readme update. Version undetermined; wait for the dev. On the todo list as Unfinished (2026-10-30); the dev moved it to the TOP of the unfinished items, ahead of the prize wheel, offline progress and the daily bonus."
metadata:
  node_type: memory
  type: project
---

# Shop pack menus overhaul (idea, one store at a time)

**Origin (dev, 2026-10-30):** after the sell cookies menu became a pack menu in 7.7 (sell 1 / 5 / ... / 10,000, sell all before Back, real payout labels, instant, stays on the line; see [[project_release_status]]), the dev asked how hard the same machinery would be for the buying shops. "I'm considering if they should have something like this too, but one store at a time." Then: "Please record this shop overhaul plan. I have a feeling this may require several change log entries, plus a big readme update." **Not approved or scheduled; the dev decides when and which store first.**

## The idea

- Today every shop asks "how many?" in an input box after you choose an item, already filled with the most you can afford.
- Pack style: choosing an item opens a menu instead: Buy 1 for $X, Buy 5 for $X, ... (the seller uses 1, 5, 10, 25, 50, 100, 250, 500, 1,000, 2,500, 5,000, 10,000), then **Buy max** (with its count and price) before Back. Labels come from each shop's existing bulk cost code, so they include rising prices, discount boosts, the 1 cent floor, and caps. Choosing a line buys instantly, speaks the result, stays on that line (remembered like the seller's sell_last_id).
- Only the input-box question is replaced; each shop's purchase logic already handles any amount.
- Trade-off to raise when planning: one extra menu level per item. Option: a "Buy a custom amount" line among the packs that opens the old input box.

## Per-store difficulty (assessed 2026-10-30)

- **Boosts** (easy): simple rising price; max_boosts hold limit.
- **Scratch tickets** (easy): rising price; max_tickets limit.
- **Single upgrades** (medium): money or cookie prices by category, percent vs flat items, the baking speed cap (cap_speed_maxbuy), and the existing category-level buy all.
- **Bundles** (medium): price is the sum of several items' rising prices; speed inside bundles respects its cap; existing buy all.
- Suggested order: boosts or tickets first to settle the pattern, then singles and bundles; after the first, a shared pack-menu helper so later shops plug in their price and buy functions.

## Existing buy-all options (keep them)

- Singles: "Buy all affordable items" at the end of a category's item list (menu.nvgt ~1421); asks "How many of each item would you like to buy?" prefilled with the max.
- Bundles: "Buy one of every affordable bundle" (~1869); despite the name asks "How many of each affordable bundle would you like to buy?".
- Boosts and tickets have none.
- Plan: keep them at the end before Back (the shops' version of sell all). Per shop, decide whether their input box also becomes a pack menu (Buy 1 of each ... Buy max of each, with total prices; consistent but long labels) or keeps the input box (simpler to type "how many of each").

## Docs expectations

Several changelog entries (likely one per store, maybe more), and a big readme update: every shop's buying description in the cookie store section, plus the getting started purchase instructions.
