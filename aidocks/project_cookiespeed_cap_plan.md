---
name: project_cookiespeed_cap_plan
description: "Approved plan to hard-cap cookiespeed at its useful maximum (950 = 1000ms base clicktime - 50ms floor) so it can't balloon past what actually speeds baking, and so lottery/minigame/prestige awards report only the gain that helped."
metadata:
  node_type: memory
  type: project
  originSessionId: 6e820f7e-c923-43e9-bd1c-7c123f031f23
---

## Status: SHIPPED in 6.8. All sections (1, 2, 2b, 3, 4, 5, 6) built and committed. Kept as the design record. (Notable: the baker-event cookiespeed/clicktime desync was a real pre-existing bug fixed here and given its own changelog entry; the single-item shop purchase was a Section 2 gain site initially missed; rank rewards + prestige rewards keep a negative-amount branch so penalty modding still works.)

Refinement (not a bug): `cookiespeed` can grow without bound (200k, a million from the lottery) even though it stops doing anything once the bake interval hits its floor. Cap it at its useful maximum. Planned per [[feedback_plan_features_in_memory]]; the dev commits each section; docks last ([[feedback_docks_last]]).

## The mechanic (verified)

- `clicktime` (the auto-bake interval, `int`, `dec.nvgt:36`) **starts at 1000ms, is clamped to [50, 1000]** every frame (`game.nvgt:51-58`). Auto-bake fires when `clicktimer.elapsed >= clicktime` (`game.nvgt:199`).
- `cookiespeed` (`double`, `dec.nvgt:27`) is the **running total of ms shaved off**. Every gain does `cookiespeed += amount; clicktime -= amount;`. Every loss/refund does the reverse.
- Therefore once `clicktime` hits its 50ms floor, `cookiespeed` has done its ENTIRE job at **950** (1000 base − 50 floor). Every point above 950 is dead weight — it speeds up nothing. `cookiespeed` is otherwise only used as a display/bettable stat.
- Worst offender: cookie-lottery percentage prizes use current `cookiespeed` as their base (`lottery_table.nvgt:118, 231`), so a bloated stat feeds absurd "you won a million ms" awards that also do nothing.

## Locked design decisions

- **`clicktime` bounds unchanged: [50, 1000].**
- **`cookiespeed` hard cap = 950** (`= 1000 base − 50 floor`). This is "the clamp": you can't buy or receive past it, because past it does nothing. (Dev confirmed the cap is the point where clicktime bottoms out, not a round 1000 that would leave ~50 wasted points.)
- **New constant** `const double COOKIESPEED_MAX = 950;` (in `dec.nvgt`, near `clicktime`, with a comment noting it = base − floor).
- **New helper** in `extrafuncts.nvgt` — the single choke point for ALL cookiespeed *gains*:
  ```
  // Adds up to `amount` cookiespeed, never past COOKIESPEED_MAX; keeps clicktime in sync.
  // Returns the amount actually applied, so callers report the real gain, not the requested one.
  double gain_cookiespeed(double amount)
  {
      if (amount < 0) amount = 0;
      double room = COOKIESPEED_MAX - cookiespeed;
      if (room < 0) room = 0;
      double applied = min(amount, room);
      cookiespeed += applied;
      clicktime -= int(applied);
      if (cookiespeed > COOKIESPEED_MAX) cookiespeed = COOKIESPEED_MAX;
      if (cookiespeed < 0) cookiespeed = 0;
      if (clicktime < 50) clicktime = 50;
      if (clicktime > 1000) clicktime = 1000;
      cookiespeed = floor(cookiespeed);
      return applied;
  }
  ```
- **Report the applied delta:** every gain site shows/records what `gain_cookiespeed` returned, so a maxed-out player sees "+40 ms" (or nothing) instead of "+1,000,000 ms".
- **Backstop cap** in the per-frame normalizers so any missed path self-corrects: add `if (cookiespeed > COOKIESPEED_MAX) cookiespeed = COOKIESPEED_MAX;` beside the existing `cookiespeed<=0` guards (`game.nvgt:37-41` and each block in `minigames.nvgt`).
- **Losses/refunds** (bets: `cookiespeed -= X; clicktime += X`) are left as-is — they're naturally bounded by your balance and only shrink the stat.
- **Baker events** (`baker_events.nvgt:60-71`) currently clamp `cookiespeed` to `min(1000, ...)` / `max(50, ...)` and must be reconciled to the new cap; verify they keep `clicktime` in sync (they may currently desync it — check during Section 4).

## Gain sites to route through the helper (verified list)

- **Purchases** `menu.nvgt`: single (percent `:1222-1223`, flat `:1229-1230`), bulk (percent `:1312-1313`, flat `:1318-1319`), buy-all (`:1583`, `:1665-1667`). Report `speedGained` = sum of applied deltas.
- **Lottery** `lottery_table.nvgt`: single (`:125-132`), bulk (`:254`). Report only applied.
- **Achievements** `achievements_table.nvgt:180-185`.
- **Rank rewards** `ranks_table.nvgt:83-85` (`apply_rank_reward`, target `cookiespeed` → `cookiespeed += amount`) — found during the 6.8 readme pass; route through the helper in Section 4.
- **Prestige rewards** `prestige_table.nvgt:165, 202` (percent base snapshot `:158` is ≤950 after the cap).
- **Minigame wins/returns** `minigames.nvgt` (selectedBet == 3 gain paths): `:399, 543, 591, 629, 663, 938, 1176, 1240, 1478`. Bet-loss paths (`:390, 521, 887, 1105, 1435`) stay as-is.

## Build sections (commit-safe order — one per turn, pause for commit)

1. **Core** — `dec.nvgt`: add `COOKIESPEED_MAX`. `extrafuncts.nvgt`: add `gain_cookiespeed`. `game.nvgt`: add the upper-cap backstop to the per-frame normalizer. (Self-contained; nothing else calls the helper yet, so it compiles and just clamps the stat.)
2. **Speed upgrade purchases** — `menu.nvgt` single/bulk/buy-all speed branches → helper; report applied for `speedGained`. (NOTE during build: there are TWO single-shop gain blocks — the aggregated buy-all-affordable block AND the single-item purchase block ~1304-1317; the latter has no `speedGained` var so it's easy to miss. Both must route through the helper.)
2b. **Quantity cap on speed purchases** (dev chose this over letting overspend past the cap) — you shouldn't be able to *pay* for speed past 950. Add `max_useful_speed_copies(store_item@)` (= `ceil((950 - cookiespeed) / per)`, where `per` = flat amount or `cookiespeed*amount/100` for percent; 0 when maxed) and `cap_speed_maxbuy(it, maxbuy)` in `menu.nvgt`. Apply `cap_speed_maxbuy` to every `calc_max_buyable` result for target==3 (display "You can buy N", anyAffordable, buy-all overallMax + per-item quantity). In the single-item flow: if maxed, show "Your baking speed is already at its maximum." and break; otherwise clamp the entered quantity to the useful cap before charging. Bundles stay effect-capped only (buying a whole bundle shouldn't be blocked by its speed sub-item).
3. **Lottery** — `lottery_table.nvgt` single + bulk → helper; report applied.
4. **Achievements, rank rewards, prestige, baker events** — route through helper; reconcile the baker-event clamp + clicktime sync.
5. **Minigames** — all selectedBet==3 win/return paths → helper; add the upper-cap backstop to each `minigames.nvgt` normalizer block.
6. **Docks** — readme (note the 950 cap on cookie speed), changelog entry (6.8), `build/version.txt` already at 6.8.

## Tuning knobs
`COOKIESPEED_MAX` (950) is the only knob; it must stay `= base clicktime − min clicktime` if either of those ever changes.
