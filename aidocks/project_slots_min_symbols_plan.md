---
name: project_slots_min_symbols_plan
description: "PLAN (7.2, requested 2026-10-27, not built, queued after the store-prices-after-losses plan): slots min_symbols 10 → 6 permanently, and the symbol checklist starts with 6 checked instead of all. Readme + code default to update."
metadata:
  node_type: memory
  type: project
---

# Slot machine: min_symbols 6, six checked by default (7.2)

Requested by the dev on 2026-10-27, mid-way through [[project_loss_price_plan]] section 2. Kept as a separate plan so the two don't mix. **Start only after the loss-price plan is finished.**

## What the dev decided

- **min_symbols = 6, permanent.** The dev first edited slots.table to 6, then put it back to 10 (2026-10-27) so it stays out of the loss-price work; the dev also deliberately moved the `min_symbols` line up to sit right after `max_bet`, above `confirm_threshold` and `house_edge` — keep that order. Building this plan sets `min_symbols=6` in `cycrz/data/decompiled/config/tables/slots.table`.
  - Why 6: with the default 5 reels and 5 percent house edge, fewer than 6 checked symbols makes `slot_payout_scale` return below 0. `slot_payouts_summary` (slots_table.nvgt) and the spin check (minigames.nvgt, ~line 320) then say "With 5 reels, please check more symbols so the payouts can be balanced." So 6 is the lowest count that always works with the shipped table.
- **The checklist starts with 6 symbols checked**, not every symbol in the table. Today `slotsgame()` (minigames.nvgt) builds `bool[] symbolToggles(symbols.length(), true)`, so everything starts checked.

## Open questions (ask the dev, one at a time)

1. Which 6 start checked: the first 6 in list order (after the `symbols=asc:/dsc:/none:` sort), or something else?
2. Should the starting number follow `min_symbols` (so a modder who sets 8 gets 8 checked), or be a fixed 6?
3. Should the code fallback for a missing `min_symbols` line also become 6? It's 10 today in `dec.nvgt` (`int slotMinSymbols = 10;`) and the `parse_slots_table` reset (`slotMinSymbols = 10;`).

## Docs to update when built

- Readme line ~303 (slot machine section): "You must keep at least 10 symbols checked to spin" and "every symbol starts checked".
- Readme slots.table reference: `symbols` ("at least as many symbols as min_symbols, which defaults to 10") and `min_symbols` ("defaults to 10"); these only change if question 3 is yes.
- Changelog 7.2 entry.
