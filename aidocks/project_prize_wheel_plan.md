---
name: project_prize_wheel_plan
description: "Planned minigame (7.6 or later, IN DISCUSSION 2026-10-29): a prize wheel that stands apart from the cookie flipper — fixed weighted wheel, spin-again pot with Bankrupt and Lose a Turn, prizes the flipper can't give; achievements and quests wanted. Draft blueprint + open questions; nothing approved or built."
metadata:
  node_type: memory
  type: project
---

# Prize wheel minigame (planned for 7.6 or later)

Origin: dev wanted a wheel game built on their WheelSpinner app's spin (same decelerating click loop roulette uses — see [[project_feature_ideas]]). Researched the TV show, the casino Big Six money wheel and carnival prize wheels (2026-10-29). **Dev decisions so far:** no word puzzles; the prize wheel (not Big Six); it must **stand apart from the cookie flipper**; **wants the spin-again pot** (Bankrupt / Lose a Turn); **wants achievements and quests**. Follow [[project_minigame_build_guide]]: blueprint first, then sections, docs last, achievements and quests as two separate sections.

## How it stands apart from the flipper (draft)

The flipper is free, 50/50 good or bad, you pick its events, and its outcomes are flat stat gains/losses. The prize wheel instead:
- **Fixed wheel** from the config, not player-picked; a wheel info button tells each wedge's share (like the slot machine payouts button) so the odds are honest.
- **Costs something per round** (how is an open question) so each round is a decision.
- **Spin-again pot:** prize wedges add to a round pot; after each spin you spin again or bank. **Bankrupt** empties the pot and ends the round; **Lose a Turn** ends the round but you keep (bank) the pot — the TV show's real rules.
- **Prizes the flipper can't give:** a rare **jackpot**, a **free boost** copy, a **scratch ticket**, a **double** wedge that doubles the pot, plus rank-scaled money/cookies/auto cookies/manual cookies/speed.

## Draft config (wheel table)

Settings: unlock/entry cost fields, spin timing (ticks, delay, growth — as roulette), sounds, messages with tokens. Wedges section, one line per wedge kind: `id:Name:count:type:target:min:max:sound:message` — count = how many wedges of the wheel it covers (its odds = count / total); type = prize | jackpot | double | boost | ticket | nothing | bankrupt | lose_turn; amounts use rank operators like flipper.event.

## Draft stats (achievements and quests)

Rounds played, spins, rounds banked, bankrupts, lose a turns, jackpots (signature), doubles, plus probably best pot. Quests: rounds played, spins, rounds banked, jackpots (never bankrupts — quests never reward losing). Achievements: everything, own category.

## Open questions (ask ONE at a time, record each answer here)

1. How a round is paid for (entry fee per round vs per spin; which item/currency; flat rank-scaled vs a typed amount like a bet).
2. What the pot holds (mixed prizes kept as a list, or everything converted to one item).
3. The wedge list and counts (default wheel size, e.g. 24).
4. Max spins per round, or unlimited until bank/Bankrupt.
5. Name — sets its alphabetical slot in the minigames panel and so the unlock-ladder shift ("Wheel of fortune" would go after slot machine at rank 90 and shift baking slots manager, boosts, combos, prestige by 10; "Prize wheel" would go between higher or lower and roulette).
6. Auto/manual spin setting like roulette (likely yes); include in reload_config (likely yes, like the newer games).
7. Sounds the dev will add.

## Answers

- **Q1 + Q2 (dev, 2026-10-29): option A, a bet.** Pick an item (money, cookies, auto cookies, manual cookies; baking speed was removed from all minigame bets in 7.5, see [[project_speed_bet_removal_plan]]) and type an amount, using the shared bet functions (betfuncts.nvgt: read/confirm/take/pay, B key status) like the other casino minigames. Prize wedges pay multiples of the bet into the pot, so the pot stays in the bet item; the free boost and scratch ticket are extras won alongside it.
- **Q3 (dev, 2026-10-29): default wheel APPROVED, 24 wedges:** small prize 5, medium prize 6 (about half the bet), big prize 3 (about 1x), huge prize 1 (about 2x), jackpot 1 (big multiple, signature stat), double 1 (doubles the pot, worth nothing on the first spin), free boost 1 (random boost held, on top of the pot), scratch ticket 1 (random ticket, on top of the pot), nothing 1 (spin again allowed), lose a turn 2 (round ends, keep pot), bankrupt 2 (round ends, pot emptied). **Exact multipliers set when writing the table, by simulating rounds so even best play loses a little over time** (the spin-again pot can turn a too-generous wheel into a profit).
- **Q4 (dev, 2026-10-29): option A, a spin limit in the config** — a max_spins setting (proposed default 10); reaching it banks the pot automatically; 0 = no limit (like double or nothing's max_rides).
- **Q5 (dev, 2026-10-29): name "Prize wheel"** (generic; avoids the TV show's registered name). Panel slot between higher or lower and roulette, button "Play &prize wheel" (Alt+P). **Ladder:** prize wheel 70, roulette 70 to 80, slot machine 80 to 90, baking slots manager 90 to 100, boosts 100 to 110, combos 110 to 120, prestige 120 to 130 — six unlocks shift; the shift gets its own changelog entry.
- **Q6 (dev, 2026-10-29): yes, works like roulette** — the same spin settings in the table (spin_base_ticks, spin_random_range, spin_base_delay, spin_growth: roulette ships 50/50/20/2) and an auto/manual spin checkbox in the game's window, default on (auto = full decelerating spin then result; manual = "The wheel is spinning. Press enter or space..." then land sound + result). Saved in preffs, default in reset_game_settings. Also in reload_config with launch-style empty checks (default, not objected to).
- **Q7 (dev, 2026-10-29): sound list APPROVED, and NO roulette (or other) placeholder sounds** — the table points only at sounds/minigames/prize_wheel/: spin.ogg, land.ogg, bet(1,3).ogg, prize.ogg, jackpot.ogg, double.ogg, bonus.ogg (boost or ticket won), nothing.ogg, lose.ogg (renamed from lose_turn.ogg by the dev in the example table, 2026-10-29), bankrupt.ogg, bank.ogg. Wedge sounds live in each wedge line's sound field (like slots.table payout rows); only spin, land, bet, bank are in the Sounds section. **Lose a Turn plays only lose.ogg (after land.ogg), never bank.ogg, even though it banks the pot (dev, 2026-10-29).** bank.ogg is for banking yourself and the spin limit. **Bankrupt only empties the pot (dev, option A, 2026-10-29):** boosts and scratch tickets won from wedges go straight into your inventory and are kept even if you later hit Bankrupt that round. Also for section 1: a wedge count of 0 must leave it off the wheel (example table comment promises this). The dev adds the files; remind them before testing that they must exist.

## Example table (2026-10-29, dev asked; written to repo-root New File.txt, not the real config)

Format: settings (min_bet, max_bet, confirm_threshold, max_spins=10, roulette spin timing), sounds (spin, land, bet(1,3), bank), messages (start, bank, limit), then [wedges] `Name:count:type:amount:sound:message` (types prize/double/boost/ticket/nothing/lose_turn/bankrupt; amount = multiple of the bet, or copies for boost/ticket; tokens %wedge% %amount% %item% %pot% %bet% %spins% %boost% %ticket%). **Simulation (best play by dynamic programming, 10-spin limit):** the "about half / 1x / 2x" sizes were far too generous (best play returned 1.3 to 1.7 times the bet). Example uses small 0.05, medium 0.1, big 0.3, huge 0.6, jackpot 3 = best play returns about 0.94 (6% house edge). **Finding to raise in section 1:** with only 2 Bankrupt wedges in 24, best play is nearly always to spin to the limit (banking after one spin returns about 0.22); banking only pays off once the pot passes roughly 5 times the bet, so the spin-or-bank choice rarely matters. Options: more Bankrupt wedges, a bigger double/jackpot pull, or accept it.

## Section plan (APPROVED by the dev 2026-10-29; for 7.6, not to be built until the dev says so; on the todo list as Unfinished)

1. **Table + parser** — prize_wheel.table (settings incl. max_spins, spin timing, sounds, messages; [wedges] with the approved 24-wedge wheel, multipliers set by a simulation so best play loses a little); parsers/prize_wheel_table.nvgt; parse call + load guard in cycrz.nvgt; reload_config with empty checks. Tests: launches; Control L with a broken table is refused.
2. **The game** — state and stats (dec, save/load, new-game reset, SESSION_STATS, quest_baseline_stats, statistics screen block), prizewheelgame() + input (bet via betfuncts, spin loop, pot, spin again / bank, Bankrupt, Lose a Turn, double, jackpot, boost and ticket prizes, max_spins auto-bank, B key, wheel info button, auto/manual checkbox), minigame_input dispatch, panel button at rank 70, reset_game_settings default. Tests: several.
3. **Ladder shift** — roulette 80, slot machine 90, baking slots manager 100, boosts 110, combos 120, prestige 130 (menu gates, ranks.table, combos.table, prestige.table, boosts unlock, handle_rank_unlock). Tests: locked messages.
4. **Achievements** (own category). 5. **Quests** (wins/play only). 6. **Docs** (7.6 block: ladder shift entry, minigame, achievements, quests; readme blurb, statistics, prize_wheel.table reference).
