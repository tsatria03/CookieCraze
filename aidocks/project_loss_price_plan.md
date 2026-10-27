---
name: project_loss_price_plan
description: "SHIPPED 7.2 (all 3 sections built and tested, 2026-10-27): store prices drop when you lose auto/manual cookies — design review #8. Proportional: a loss scales autocookie_purchases / manulcookie_purchases by after/before; free gains never raise them; minigame bets count only when a round ends as a loss. 3 sections."
metadata:
  node_type: memory
  type: project
---

# Store prices after losses (7.2, design review #8)

Forum player request ([[project_design_review_7_0]] #8, todo `****Unfinished.` line 3). Dev chose option A, then the proportional version (2026-10-26).

## Locked decisions

- **Proportional scaling (dev):** the counters count purchases, not cookies (auto_cookie gives 1 per purchase, conveyor_belt 10, mega_oven 100, percent items vary, all into one counter per target), so a loss can't subtract cookies from it. Instead: `purchases *= after / before` when the count drops from `before` to `after`. Lose half → counter halves → price is what it was at half the purchases. Never below 0.
- **Keep fractions:** don't floor the scaled counter. Prices only use it through `buy_item` / `calc_max_buyable` (`pow(multiplier, level)`), which take fractional levels; flooring would let a 1-cookie loss knock off a whole purchase, repeatable.
- **Free gains never raise the counter** (unchanged; the player's way back). An old todo item already moved prices from stat values to purchase counts — don't go back to counting owned items.
- **Bets count only at a real loss (dev):** the 5 betting minigames take the bet up front and hand it back on a win/push; lowering the price when the bet is taken would let a player bet, win back, and keep the cheaper price. Scale only when the round ends with less than was staked, using before = the count before the bet was taken.

## Loss sites (verified 2026-10-26)

1. `apply_amount_effect` (extrafuncts.nvgt) polarity 2 for autocookie/manulcookie: every baker event and cookie flipper event goes through it (`apply_event` in baker_events.nvgt; flipper_events.nvgt calls the same). One central hook.
2. Quest reroll paid in auto/manual cookies (`quests_table.nvgt`, the `reroll_target` deduction, ~line 492); only when a modder sets reroll_target to autocookie/manulcookie.
3. Minigame bets with `selectedBet == 2` (auto) / `4` (manual) in minigames.nvgt: slots, blackjack, dice, higher or lower, roulette (roulette stakes `totalStake` and can return part of it). The `if(autocookie<=0) ... = 0` lines in each game loop are guards, not losses.

Not losses: rank rewards, lottery prizes, achievements (all gains, polarity 1). Prestige/new game reset the counters already.

## Adjacent bug found while mapping (not part of this plan, flag to the dev)

`apply_rank_amount` (ranks_table.nvgt) passes a negative amount to `apply_amount_effect(target, amount, 1, false)`, which does `max(1.0, floor(actual))`, so a negative rank reward on coins/cookies/autocookie/manulcookie ADDS 1 instead of deducting. The readme promises negative values deduct (ranks.table section). Only cookiespeed handles negatives.

## Build sections

**Section 1 BUILT and all 3 steps PASSED (2026-10-26; loss event lowered the price, gain event left it at 27 cents, a 20 auto cookie reroll dropped it to 22 cents); told the dev to commit.** Committed 6f504a9.

**Section 2 BUILT and all 6 steps PASSED (2026-10-27: a loss in slots, blackjack, dice, higher or lower and roulette each lowered the price; a win left it unchanged); told the dev to commit.** `bet_lost_purchases(selectedBet, lost)` above `apply_amount_effect` (before = current + lost, after = current; ignores lost <= 0; selectedBet 2 → target 1, 4 → target 2). Called after a round settles, 10 sites in minigames.nvgt: slots loss; blackjack bust + lose (betAmount) and push (betAmount - returned); dice loss (betAmount) and win (betAmount - winAmount, covers sub-1x payouts); higher or lower wrong guess (betAmount) and both bank paths (betAmount - pot); roulette (totalStake - totalReturn). `scale_purchases_after_loss(target, before, after)` sits right above `apply_amount_effect` in extrafuncts.nvgt; called from its polarity-2 autocookie/manulcookie branches (before = `base`) and from both reroll_target branches in `reroll_quest` (quests_table.nvgt).

1. Helper `scale_purchases_after_loss(int target, double before, double after)` (target 1 auto, 2 manual) + hook it into `apply_amount_effect` and the quest reroll. Test: force a loss event (main.table auto_event_chance 100), compare a store price before/after.
2. Minigame bet losses in the 5 games. Test one game per step.
3. **DONE 2026-10-27:** readme note after "Costs scale up the more you buy" in the singles shop section; changelog 7.2 entry 4 of 10 ("Made it so store prices…"); todo item moved to the top of Finished. Original: Docs: readme store pricing note, changelog 7.2 entry (would be 4 of 10), todo line 3 → Finished.
