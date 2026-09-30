---
name: project_slots_scaled_payouts_plan
description: "PLANNED (6.9): scale slot machine payouts by how many symbols are checked so every symbol count has the same house edge — few symbols = frequent small wins, all symbols = rare big wins. Fixes the +18% exploit at the 10-symbol minimum and the -65% default. Adds house_edge + min_symbols config, %amount%/%multiplier% message tokens, a check-payouts button, and full readme docs for how symbols work."
metadata:
  node_type: memory
  type: project
---

# Slot machine scaled payouts plan

Agreed direction 2026-09-30 (dev chose the recommended option over dropping symbol selection). Source: exploit #4 in [[project_bugs_exploits_balance]]. Build one section per turn; the dev tests and commits between sections ([[feedback_plan_features_in_memory]]). Docs are the last section ([[feedback_docks_last]] — this is a feature, not a bug batch). Line numbers are 6.9 — re-locate by symbol.

## Why (the numbers)

5 reels, table payouts 5:4x, 4:3x, 3:2x, 2:0.5x, symbols equally likely. The game pays by the **largest group of matching symbols** (`maxMatch` in `slotsgame`). Payouts ignore how many symbols are checked, so fewer symbols is strictly better:

| Checked | Lose | EV per bet |
|---|---|---|
| 2 | 0% | +244% |
| 10 (current minimum) | 30% | **+18%** |
| 12 | 38% | ~+2% |
| 40 (all, the default) | 77% | **−65%** |

The 10 minimum (added 1.9, `changelog.txt` "at least 10 out of the remaining symbols") only caps the exploit. The player readme (Slot machine section) never explains symbols; the modder readme's "at least 10 symbols defined" is a different rule (startup check in `cycrz.nvgt`).

## Locked decisions (dev-confirmed 2026-09-30, incl. keeping the check-payouts button)

Configurable after the build, as explained to the dev: new `house_edge` + `min_symbols`; payout `multiplier` column becomes the relative tier size; new `%amount%` / `%multiplier%` message tokens; everything else in `slots.table` unchanged. Button label "Check the &payouts" (Alt+P is free on the slots form), read-only, speaks one sentence into the buffer.

1. **Scaling rule.** Per spin, with N checked symbols and R reels: compute the exact probability P_k that the largest match is k (k = 1..R). Let W = Σ P_k × m_k over payout rows with multiplier > 0, and P_lose = P_1 (plus any k whose row has multiplier 0). Scale factor **s = (P_lose − h) / W**, where h is the house edge. Every paid multiplier becomes m_k × s. Result: expected return is exactly −h at every N. A k with no payout row still refunds (existing behavior), contributing 0.
2. **Default house edge 5%**, new `house_edge=5` field in `slots.table` (percent). For comparison, roulette is 2.7%.
3. **Minimum symbols stays 10**, moved to a new `min_symbols=10` field. Guard: if P_lose ≤ h for the chosen N (possible with many reels and few symbols), scaling can't reach the target; refuse the spin with one sentence asking to check more symbols.
4. **Messages announce the real win.** New tokens `%amount%` (the winnings, formatted as currency or items) and `%multiplier%` (the scaled multiple, 2 decimals) in payout messages. The shipped messages switch from "You won double your bet!" to e.g. "Three of a kind! You won %amount%, %multiplier% times your bet." The loss row is unchanged.
5. **Check payouts button** in the slots form (e.g. "Check the &payouts"): speaks, for the currently checked symbols, what each match pays and the chance of losing, in one sentence per tier or one summary sentence ([[feedback_one_sentence_game_messages]]). Blind players can't see a paytable, so this makes the choice informed.

## Math implementation note

P(largest match ≤ m) = R! × [x^R] (Σ_{j=0..m} x^j / j!)^N ÷ N^R. Compute with doubles by repeated polynomial multiplication truncated at degree R (R is small, N ≤ number of symbols), then P_k = P(≤k) − P(≤k−1). Verified against the table above with a Python check. Cheap enough to compute on every spin or when the button is pressed.

## Build sections

1. **DONE (code, awaiting dev test/commit)** — globals `slotHouseEdge` (5.0) / `slotMinSymbols` (10) in `dec.nvgt` beside the other slot globals; parser resets them each parse (so Ctrl+L reload picks up edits) and reads `house_edge` (clamped 0–99) and `min_symbols` (at least 2); startup check split in two (dev asked for a clearer error): empty symbols/payouts → the generic "Could not load slot machine data" alert; fewer symbols than `slotMinSymbols` → a specific alert naming min_symbols, its value, and how many symbols are defined (checked second so a missing file isn't misreported as a min_symbols problem). Any symbol count above 40 is fine — no cap; `slots.table` gained `house_edge=5` and `min_symbols=10` after `confirm_threshold`. The in-game "select at least 10" check still uses the literal until section 3. **Config + parser.** `house_edge` and `min_symbols` in `slots.table` + `parse_slots_table` (new globals, e.g. `slotHouseEdge`, `slotMinSymbols`; defaults 5 and 10; clamp house edge 0–99, min symbols ≥ 2). Startup check in `cycrz.nvgt` uses `slotMinSymbols` instead of the literal 10.
2. **DONE (code, awaiting dev test/commit)** — in `slots_table.nvgt` after the parser: `double[] slot_match_distribution(int symbolCount, int reels)` (P of largest match == k, index 0 unused, last step forced to 1 to kill rounding drift, differences clamped at 0); `payout_item@ slot_payout_for_match(int maxMatch)` (same first-match lookup as `slotsgame`, for section 3 to reuse); `double slot_payout_scale(int symbolCount)` (returns -1 if infeasible, 1 if no paying rows). Algorithm mirrored in Python first: matches brute-force odds, probabilities sum to 1, EV exactly -5% at N=10/12/40/100, -1 at N=2/5 and at reels=10,N=10. Not called anywhere yet — no gameplay change. **Math helpers.** `slot_match_distribution(N, R)` → `double[]` of P_k, and `slot_payout_scale(N)` → s (or −1 when infeasible), in `slots_table.nvgt`.
3. **Game wiring + table messages.** In `slotsgame`: replace the literal 10 with `slotMinSymbols`; infeasibility guard; `change = betAmount * payout.multiplier * scale`; fill `%amount%` / `%multiplier%`; add the check-payouts button. Update the payout messages in `slots.table` to use the tokens.
4. **Docs (last).** Readme player section: how symbols work (checking/unchecking, the minimum, few = frequent small wins vs many = rare big wins, the check-payouts button). Readme modder section: `house_edge`, `min_symbols`, the tokens, and that `multiplier` now sets the *relative* size of each tier (scaled per spin). 6.9 changelog entry (the block's 10th and last slot — [[feedback_changelog_rules]]). Todo line finished. Mark exploit #4 in [[project_bugs_exploits_balance]] FIXED and this plan SHIPPED.
