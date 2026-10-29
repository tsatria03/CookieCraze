---
name: project_speed_bet_removal_plan
description: "7.5 change DONE (2026-10-29, all 4 tests passed, docs written): remove baking speed as a bet item in every minigame, its two cookie flipper events, and the lottery's three speed prizes (weights rebalanced, payback unchanged); keep speed in the cookie store, bundles and baker.event. Changelog entry 8 of 10."
metadata:
  node_type: memory
  type: project
---

# Remove baking speed from minigames (7.5)

**Why (dev, 2026-10-29):** baking speed caps at 950 ms (95 purchases of 10 ms; interval floor 50 ms), so betting or winning it is mostly pointless — wins past the cap are wasted. Keep it only in the cookie store (singles, bundles) and the main game events (baker.event, incl. speed_rush/speed_drag). NOT in the cookie flipper.

## Decisions

- **Bets:** remove "Auto Cookie Speeds" from bet_add_items (betfuncts.nvgt), which every betting minigame uses (blackjack, dice, double or nothing, higher or lower, roulette, slots). Items were list positions 0 cookies, 1 money, 2 auto cookies, 3 speed, 4 manual cookies — manual cookies becomes 3. Plan: named constants (BET_COOKIES 0, BET_MONEY 1, BET_AUTO 2, BET_MANUAL 3) in betfuncts, used by bet_balance, bet_item_name, take_bet, pay_bet, bet_lost_purchases (extrafuncts, was `== 4`) and the minigames' `selectedBet == 1` money stat checks. The chosen bet item is not saved (local selectedBet), so no save migration. Leave the speed clamps in bet_clamp_balances (harmless).
- **Cookie flipper:** delete increase_cookiespeed and decrease_cookiespeed from flipper.event. Flipper check states aren't saved by index; history entries keep their old event names (harmless). The parser still accepts a cookiespeed target, so modders can add speed events back.
- **Lottery (dev APPROVED weights, 2026-10-29):** remove silver_speed, gold_speed, diamond_speed (weight 40 each); move that weight to the tier's auto and manual cookie prizes (+20 each) and raise the loss weight so average payback is unchanged: silver_loss 400 to 440, autocookie/manulcookie 60 to 80 (total 1060, payback 85.8%); gold_loss 300 to 323, auto/manual 60 to 80 (968, 85.7%); diamond_loss 200 to 223, auto/manual 60 to 80 (833, 85.6%). Bronze unchanged (no speed prize). Payback figures exclude the old speed prizes, which had no money value. The parser still accepts cookiespeed prizes for modders.
- Prize wheel plan ([[project_prize_wheel_plan]]) bet items drop speed too.

## Sections (proposed)

1. **Code and config** — betfuncts constants + removal, bet_lost_purchases, minigame money checks, flipper.event, lottery.table. Tests: a minigame's bet list has 4 items; a manual cookie bet wins and loses correctly; the flipper's event list has no speed events; a silver/gold/diamond scratch still works.
2. **Docs** — readme (bet item list line ~235, flipper and lottery sections where they mention speed), 7.5 changelog entry (block 7 to 8 of 10), memory; prize wheel plan item list.

## Build log

- **Section 1 BUILT and all 4 steps PASSED 2026-10-29:** BET_COOKIES/BET_MONEY/BET_AUTO/BET_MANUAL constants in betfuncts; speed gone from bet_add_items, bet_balance, bet_item_name, take_bet (incl. the clicktime bump), pay_bet; bet_lost_purchases uses BET_AUTO/BET_MANUAL; 8 minigame money checks use BET_MONEY; flipper.event and lottery.table edited as planned. Tests: 4-item bet list in all 6 games; manual cookie dice bet lost exactly 1000; flipper has no speed events; 1,308 silver scratches, 41.7% losses (weight 41.5%). Note: value prizes too small to buy one item pay money instead (pre-existing), so manual cookie wins can show as money.
- **Section 2 (docs) DONE 2026-10-29:** readme bet list sentence (blackjack section, line ~235) says speed cannot be bet and why; config references left alone (flipper.event and lottery.table still accept cookiespeed for modders); random events section unchanged (baker.event keeps speed). 7.5 changelog entry 8 of 10.
