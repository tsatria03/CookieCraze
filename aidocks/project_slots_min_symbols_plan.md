---
name: project_slots_min_symbols_plan
description: "SHIPPED 7.2 (both sections built, all 4 steps passed, 2026-10-27): slots min_symbols 10 → 6 permanently, and the symbol checklist starts with 6 checked instead of all. Readme + code default to update."
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

- **Which symbols start checked (dev, 2026-10-27, final): the first ones in the symbol order** — i.e. after the `symbols=` ordering prefix is applied (asc alphabetical, dsc reverse, none/omitted as written), the first N in that sorted list start checked. The dev briefly chose a separate default list in slots.table, then went back to this; no new setting.

## Open questions (ask the dev, one at a time)

1. ~~Which start checked~~ — answered: the first ones in symbol order.
2. ~~Fixed 6 or follow min_symbols~~ — answered (dev, 2026-10-27): **N = min_symbols**, so the first `slotMinSymbols` symbols in order start checked.
3. ~~Code fallback~~ — answered (dev, 2026-10-27): **yes, 6.** Change `int slotMinSymbols = 10;` in `dec.nvgt` and the `slotMinSymbols = 10;` reset in `parse_slots_table` to 6.

All questions answered; plan AGREED 2026-10-27, not built.

## Build sections

**Section 1 BUILT and all 4 steps PASSED (2026-10-27; 6 checked by default, payouts/spin work, min_symbols 8 → 8 checked, missing line → 6).** Section 2 (docs) DONE the same day: readme slot section + symbols/min_symbols references (min_symbols now also notes it sets how many start checked and that below 6 with 5 reels/edge 5 spins are refused), changelog 7.2 entry 5 of 10. Dev will commit sections 1 and 2 together, so when section 1's last step passes, go straight to section 2 instead of saying "commit now".

1. **Code + table.** slots.table `min_symbols=6` (keep the dev's line position, right after max_bet); the two code fallbacks 10 → 6; in `slotsgame()` replace `bool[] symbolToggles(symbols.length(), true)` with all false then the first `slotMinSymbols` set true (symbols is already in the sorted order). Tests: slots opens with bell, chime, heart, pebble, star, clover checked and the rest unchecked; check payouts and a spin work with those 6; set min_symbols 8 + Control L → first 8 checked; remove the line → back to 6.
2. **Docs.** Readme slot machine section (~line 303: "every symbol starts checked", "at least 10 symbols"), slots.table reference (`symbols` and `min_symbols` "defaults to 10" → 6, mention the first min_symbols in order start checked), changelog 7.2 entry 5 of 10, memory.

## Docs to update when built

- Readme line ~303 (slot machine section): "You must keep at least 10 symbols checked to spin" and "every symbol starts checked".
- Readme slots.table reference: `symbols` ("at least as many symbols as min_symbols, which defaults to 10") and `min_symbols` ("defaults to 10"); these only change if question 3 is yes.
- Changelog 7.2 entry.
