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

**Config — a DRAFT table exists at `data/config/tables/highlow.table`** (mirrors jacks/slots/dice conventions; config only, nothing reads it yet). **The draft carries verbose field-by-field `;` comments for review only — the shipped table must strip those and match the other minigame tables' minimal style: just `; Section` headers and `; Required header` markers, no explanatory prose.** The field explanations instead go in the readme's **"Configuration files for modders"** reference, matching the `dice.table`/`jacks.table` format there: `Location:` line, a one-line description, then each field name on its own line followed by a prose explanation; row/section formats get a `Format: a:b:c:d` line with per-column notes; message placeholders get `%token% is replaced with …` lines. Also add a **player-facing gameplay blurb** to the readme's minigames section (this is separate from the config reference — every minigame has both). Follow the house format: `Higher or lower. Unlocked at rank 50.` / a difficulty tier + one-line note (Beginner/Intermediate/Advanced) / a one-sentence hook / then detail paragraphs covering betting (item choice; money entered as a dollar value like "type 1 for $1.00", other items as whole numbers), the streak/cash-out mechanic, the tie rule, ace high/low, a pointer to `highlow.table`, the manual/auto reveal toggle, and the confirmation-prompt note. Insert it in unlock order **between the dice roller (rank 40) and the slot machine** — and because of the rank shuffle, the slot machine, baking slots manager, and combos headers in this gameplay section also move (slots 50 → 60, manager 60 → 70, combos 70 → 80).
- `unlock_rank` — **rank 50** (decided; see the "Unlock rank arrangement" note below). The minigame access gates are **hardcoded** in `menu.nvgt`, not read from a table, so higher or lower needs a hardcoded `rank < 50` check like the other five — and the slot machine's own hardcoded check moves `50 → 60`.
- `min_bet`, `max_bet`, and a `confirm_threshold` (big-bet confirmation, like slots/dice).
- Deck definition: card names + numeric values (Ace high/low configurable); default a standard 52.
- `payout_per_correct` / streak multiplier growth curve.
- `tie_rule` = push | win | lose.
- Sounds live in `sounds/minigames/higher_or_lower/` (subfolder-prefix + `(1,2)` random-pick syntax supported, like the other minigame tables). Draft fields → files: `draw_sound` → `player_draw(1,2).ogg` (card reveal, 2 random variants, analogous to blackjack's `player_draw`/`dealer_draw`), `correct_sound` → `correct.ogg`, `wrong_sound` → `wrong.ogg`, `streak_sound` → `streak.ogg` (optional flavor), `win_sound` → `win.ogg` (cash-out). **Files needed: `player_draw1.ogg`, `player_draw2.ogg`, `correct.ogg`, `wrong.ogg`, `streak.ogg`, `win.ogg`.**
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

**Unlock rank arrangement (decided, NOT yet applied — do it as part of the build, not standalone).** Higher or lower unlocks at **rank 50**. **Rationale:** the minigames panel is **alphabetized**, so "Higher or lower" (H) sorts before "Slot machine" (S) and must unlock at an *earlier* rank than slots, otherwise unlock order wouldn't match the panel order. So slots and everything after it each shift up 10.
- Current `ranks.table` ladder: 10 blackjack, 20 flipper, 30 lottery, 40 dice, **50 slots, 60 slot manager, 70 combos**.
- New ladder: 10 blackjack, 20 flipper, 30 lottery, 40 dice, **50 higher or lower, 60 slots, 70 slot manager, 80 combos**.

Concrete edits when building:
- `ranks.table`: change the slots line to rank 60, the slotmanager line to rank 70, the combos line to rank 80, and add a rank-50 `higherlower` unlock line (same format as the others).
- `combos.table`: change `enabled=true:70` → `enabled=true:80` (the combos *gate* lives here; the ranks.table line is only the announcement).
- Slot-manager gate needs no code change: `slotManagerUnlockRank` auto-derives from the ranks.table slotmanager line (`ranks_table.nvgt`).
- **Hardcoded minigame gates in `menu.nvgt`** ("rank X or higher" checks): move the **slot machine** check `50 → 60`, and add a new `rank < 50` check for higher or lower.

**Why bundle it with the build, not now:** doing the shift standalone would (because gates re-derive on launch) **temporarily re-lock the slot machine for existing saves at rank 50–59, the slot manager at 60–69, and combos at 70–79** until they rank up, and leave rank 50 with no new unlock until the minigame ships. Shipping the shift together with the rank-50 unlock avoids that.
