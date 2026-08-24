---
name: project_feature_ideas
description: "Backlog of candidate features for 6.4+ (offline progress, golden-cookie bonus, a 6th minigame — higher or lower, auto-buyer, boosts shop, achievement rewards, QoL). Ideas only, none built yet."
metadata:
  node_type: memory
  type: project
---

# Feature ideas backlog (6.4+)

Brainstormed 2026-08-23, right after 6.3 shipped. **None of these are built** — this is a wish list to pull from. All should follow the data-driven, modder-tunable ethos ([[project_data_driven_config]]) and the audio-only UX. See [[project_game_vision]] for the systems these extend.

**Suggested first-release trio (cohesive, hits idle + meta + active play):** offline progress (headline) → daily streak (cheap, rides its timestamp) → golden-cookie bonus (the "wow").

## Signature idle mechanics

- **Offline progress** ⭐ — the defining idle feature, currently absent (no timestamp/away logic exists in the code as of 6.3). On load, diff current time vs a saved timestamp and award cookies for time away, scaled by auto-bake rate. Config: cap hours (e.g. 8h), efficiency % (e.g. 50%). Show a "Welcome back, you baked X while away" dialog. *Medium.*
- **Golden cookie / listen-and-catch bonus** — a rare random audio cue (distinct chime, pannable via HRTF [[project_audio_model]]); press a key within a few seconds to claim a timed bonus (production multiplier / windfall / frenzy). Cookie Clicker's signature moment, reimagined for audio. Rank-gated; config chance/window/reward table. *Medium.*
- **Daily bonus / play streak** — escalating reward for playing on consecutive days; reuses the offline-progress timestamp, so cheap once that exists. *Low.*

## New content

- **6th minigame: higher or lower** — detailed spec below. Also viable: memory/Simon audio-sequence, wheel of fortune. *Medium-high.*
- **Auto-buyer automation** — late-game unlock that auto-purchases the cheapest affordable upgrade on a timer (the classic idle "manager"); extends automation from baking into spending. Rank-gated, toggleable. *Medium.*
- **Temporary boosts shop** — spend cookies/coins on timed power-ups (2x production 60s, auto-bake burst, combo-window extension); adds active decisions to the idle loop; reuses store/config machinery. *Low-medium.*

## Meta / progression

- **Achievement rewards** — achievements only unlock today; let some grant a small permanent bonus (production %, prestige points) via a config-driven reward field, turning the 200+ achievements into a progression layer. *Low-medium.*

## Quality of life

- **Number-format setting** — `format_number` already has a `scientific` flag; expose a preference (suffix / scientific / raw). *Low.*
- **Session summary on exit** — "This session: baked X, earned Y, ranked up Z" from stats already tracked. *Low.*

## Higher or lower — detailed design

A press-your-luck card game, deliberately different from blackjack (hand-based): here you **build a streak or bank it**.

**Core loop:**
1. Pick bet currency + amount — same multi-currency selector as blackjack (cookies / coins / autocookie / cookiespeed / manulcookie; the `selectedBet` pattern in `minigames.nvgt`).
2. Draw and announce the first card (speech + card-flip sound).
3. Menu: **Higher** / **Lower** (and **Cash out** once a streak is running). Follow [[feedback_yes_no_menu_labels]] spirit — clear labels, context in the prompt.
4. Draw the next card, announce it, compare.
   - Correct guess → streak++, pot grows by the streak multiplier; player may guess again (risk it) or cash out (bank pot).
   - Wrong guess → lose the bet, streak resets.
   - Tie → configurable: push (re-draw, streak intact), win, or lose.
5. Cash out pays `bet * accumulated multiplier` in the bet currency.

**Config (`data/config/tables/higher_lower.table`)** — mirror the other minigame tables (jacks/slots/dice):
- `unlock_rank` (gate it at an ascending rank like the others — dice 40, slots 50, so maybe 60).
- `min_bet`, `max_bet`, and a `confirm_threshold` (big-bet confirmation, like slots/dice).
- Deck definition: card names + numeric values (Ace high/low configurable); default a standard 52.
- `payout_per_correct` / streak multiplier growth curve.
- `tie_rule` = push | win | lose.
- Sounds: card flip, correct, wrong, streak-up, cash-out (subfolder-prefix syntax supported, e.g. `higher_lower/flip.ogg`).
- Messages with tokens (`%card%`, `%streak%`, `%multiplier%`, `%payout%`).
- Auto-reveal toggle, matching the per-minigame auto settings (`jackmode`/`slotmode`/etc.).

**Wiring (follow the existing minigame conventions):**
- New parser `parse_higher_lower_table(...)` called from `main()` in `src/cycrz.nvgt`, plus a load-failure guard like the others.
- Game loop calls `minigame_input()` (buffer nav + info keys + the new F1–F4 mutes already work there).
- Announce every card value clearly; announce streak count and current pot each round (audio-first).
- Add stats (`stat_hl_games`, `stat_hl_wins`, `stat_hl_losses`, `stat_hl_highest_streak`) with matching achievements + quests, mirroring how every other minigame ships tiered sets.
- Cap all payouts with `safe_cap` per [[project_number_overflow_cap.md]] (bet * multiplier can grow fast on a long streak).
- Add to the readme minigames section (in correct unlock order) and the changelog when built.

**Why it's a good fit:** reuses the entire minigame scaffold (table + parser + `minigame_input` + auto-toggle + stats/achievements/quests), but the streak/cash-out tension gives it a distinct feel from the existing five.
