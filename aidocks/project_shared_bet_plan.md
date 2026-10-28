---
name: project_shared_bet_plan
description: "7.4 refactor AGREED 2026-10-28 (consistent wording/sounds) (code #16): one shared set of bet functions in a new functions/betfuncts.nvgt, replacing the bet code copied across the 6 betting minigames (blackjack, slots, dice, double or nothing, higher or lower, roulette); converted a few games per section; awaiting the dev's approval."
metadata:
  node_type: memory
  type: project
---

# Shared bet functions (7.4, code #16)

The dev wants the copied bet code replaced by the same functions in every minigame (2026-10-28). Six minigames take bets: blackjack, slot machine, dice roller, double or nothing, higher or lower, roulette. Cookie flipper is free and the lottery buys tickets in a shop, so neither has a bet.

## Copied today (minigames.nvgt)

- 5-item list (Cookies, Currency, Auto Cookies, Auto Cookie Speeds, Manual Cookies).
- Loop balance clamps (autocookie/coins/cookies/cookiespeed/manulcookie/clicktime).
- Parse: floor(text, ×100 for currency); errors: invalid (≤0), below min_bet, capped at max_bet, clamp to balance, "not enough".
- Confirm threshold (amount or percent of balance) + yes_no "Are you sure you want to bet …".
- Item name ("cookies", "" for currency, …) and amount text (convert_to_currency vs format_number).
- Take the bet (cookiespeed also adds to clicktime); pay out (gain_cookiespeed for speeds; money profit to stat_coins_earned).

## Differences to keep

- Slots: its errors play NO fail sound and use "Invalid bet amount. Please enter a valid number!"; it checks symbols between the amount checks and the confirm; it speaks "You placed a bet of …".
- Roulette: the per-type amount is not clamped to the balance; the TOTAL stake (amount × bet types) is checked against the balance with its own error, and the confirm is about the total stake.
- Blackjack: its item name for currency is "currency", others use "".
- Profit accounting for stat_coins_earned differs per game (blackjack/dice/slots/highlow/don) — keep each game's existing numbers.

## Proposed helpers (new src/includes/main/functions/betfuncts.nvgt, picked up by the functions/* glob)

bet_add_items(list), bet_clamp_balances(), bet_balance(item), bet_item_name(item), bet_amount_text(item, amount), bet_fail(message), read_bet(item, text, min, max, clampToBalance) → amount or 0, confirm_bet(item, amount, threshold, usePercent, question) → bool, take_bet(item, amount), pay_bet(item, amount).

## Sections (proposed; one per turn, test, commit)

1. Helpers + convert double or nothing (newest, cleanest) — tests: every bet error, confirm, a win and a loss. **BUILT 2026-10-28:** betfuncts.nvgt has bet_add_items, bet_clamp_balances, bet_balance, bet_item_name, bet_amount_text, bet_amount_with_item, bet_fail, read_bet(item,text,min,max,clampToBalance=true), confirm_bet(item,amount,threshold,usePercent,question=""), take_bet, pay_bet; dongame uses them, don_amount removed (→ bet_amount_text), don_bank uses pay_bet and keeps its own money-earned line. No behaviour change for double or nothing.
2. Convert higher or lower and dice. **BUILT 2026-10-28** with scratchpad converter bet_conv.py (exact-text block matches, asserted counts): items, clamps, read_bet, confirm_bet, bet_item_name, take_bet, pay_bet (dice refund + win; highlow forced bank + bank, each keeps its own money-earned line), amount text → bet_amount_text / bet_amount_with_item. No behaviour change.
3. Convert blackjack and slots. **BUILT 2026-10-28** (scratchpad bet_s3.py): blackjack — items, clamps, read/confirm/take, both win pays (+ its own stat_coins_earned += winnings), push pay, amount text, betItemName now "" for currency (itemStr lines simplified; nothing spoken changes). Slots — items, clamps, read_bet (now fail sound + standard wording "Error! Please enter a valid bet amount." / "Error! You don't have enough of that item to bet!"; focus back to spin kept), confirm_bet, "You placed a bet of" via bet_amount_with_item, take, win pay + its own profit line, refund, payout %amount%; the two symbol errors now use bet_fail (fail sound added) for consistency. PLAYER-VISIBLE: slots error sounds/wording → changelog in docs. **Added at the dev's request (2026-10-28): B key in slots** speaks "Your current bet is …" from globals slotLastBet/slotLastItem (minigames.nvgt, speak_slot_bet()), set only when a spin takes the bet; the key is handled in minigame_input for game "slots" so it also works during the spin's mini_wait_async waits (dev found it silent mid-spin) (dev: it must change only after a spin happens) or "You have not placed a bet yet." — focus-guarded off the bet box. Dice and roulette still have no B key — **the dev wants B added to BOTH dice and roulette in section 4 (2026-10-28)** — put it in minigame_input like slots so it works during their waits too. Changelog + readme in docs.
4. Convert roulette (total-stake path). **Section 4 scope also (dev, 2026-10-28):** B key in dice and roulette (new), AND move the existing B keys of blackjack, double or nothing and higher or lower into minigame_input so B works during every game's mini_wait_async waits — needs each game's B-state (blackjack bet; highlow streak/pot/last card; don pot/rides) moved out of the game function, like slotLastBet. Section 3 (blackjack + slots) all steps PASSED 2026-10-28.
5. Docs: changelog only if wording/sounds change for players.

**Decided (dev, 2026-10-28): make the shared functions CONSISTENT where possible.** Slots gets the fail sound and the standard error wording ("Error! Please enter a valid bet amount.", "Error! You don't have enough of that item to bet!"); blackjack's currency item name becomes "" like the others (check every message that uses it). Roulette's total-stake check and slots' symbol checks stay, since they are real rules, not wording. These player-visible changes get a changelog entry in the docs section. Plan approved in outline; ask "ready for section 1?" before building.
