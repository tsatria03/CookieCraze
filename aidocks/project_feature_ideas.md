---
name: project_feature_ideas
description: "Backlog of candidate features for 6.4+ (offline progress, golden-cookie bonus, new minigames incl. roulette/mines/video poker, auto-buyer, boosts shop, achievement rewards, QoL). Higher or lower and the number-format setting shipped in 6.4; the rest are unbuilt ideas."
metadata:
  node_type: memory
  type: project
---

# Feature ideas backlog (6.4+)

Brainstormed 2026-08-23, right after 6.3 shipped. **Most are unbuilt** — this is a wish list to pull from — but **higher or lower** and the **number-format setting** have since shipped in 6.4. All should follow the data-driven, modder-tunable ethos ([[project_data_driven_config]]) and the audio-only UX. See [[project_game_vision]] for the systems these extend, and [[project_minigame_build_guide]] for how a new minigame gets built.

**Suggested first-release trio (cohesive, hits idle + meta + active play):** offline progress (headline) → daily streak (cheap, rides its timestamp) → golden-cookie bonus (the "wow").

## Signature idle mechanics

- **Offline progress** ⭐ — the defining idle feature, currently absent (no timestamp/away logic exists in the code as of 6.3). On load, diff current time vs a saved timestamp and award cookies for time away, scaled by auto-bake rate. Config: cap hours (e.g. 8h), efficiency % (e.g. 50%). Show a "Welcome back, you baked X while away" dialog. *Medium.*
- **Golden cookie / listen-and-catch bonus** — a rare random audio cue (distinct chime, pannable via HRTF [[project_audio_model]]); press a key within a few seconds to claim a timed bonus (production multiplier / windfall / frenzy). Cookie Clicker's signature moment, reimagined for audio. Rank-gated; config chance/window/reward table. *Medium.*
- **Daily bonus / play streak** — escalating reward for playing on consecutive days; reuses the offline-progress timestamp, so cheap once that exists. *Low.*

## New content

- **6th minigame: higher or lower** — ✅ **SHIPPED in 6.4** (unlocks at rank 50). Detailed spec + build record below; more minigame ideas in the candidates section that follows.
- **Auto-buyer automation** — late-game unlock that auto-purchases the cheapest affordable upgrade on a timer (the classic idle "manager"); extends automation from baking into spending. Rank-gated, toggleable. *Medium.*
- **Temporary boosts shop** — spend cookies/coins on timed power-ups (2x production 60s, auto-bake burst, combo-window extension); adds active decisions to the idle loop; reuses store/config machinery. *Low-medium.*

## Future minigame candidates (7th minigame and beyond)

Brainstormed 2026-08-24 after higher or lower shipped. **The bar:** reuse the wagering scaffold (bet selector, reveal toggle, confirmation prompt, `safe_cap` payouts, stats/achievements/quests, config table — see [[project_minigame_build_guide]]) but add **one genuinely new decision**, so it feels distinct rather than a reskin (the way higher or lower added the streak/bank tension). Ranked by fit:

- **Roulette** ⭐⭐ — reuses the bet selector + spin/reveal (slots) + a payout table keyed by bet type. **The standout new mechanic: MULTIPLE simultaneous bets on a single spin** (some on red, some on a single number, some on even, etc.). *Every current minigame is one wager per round*, so multi-betting is roulette's real justification, not a reskin. Also brings bet-type-driven odds (single number pays big, red/black/even/odd pays small) — a discrete cousin of dice's target-setting.
  - **Decided (dev, 2026-08-24): multi-bet is retained, but constrained to a SINGLE item per round.** You pick one currency at the start of the round, then stack multiple bets — all in that same item — on different outcomes; you cannot mix items across bets in one spin. This keeps the multi-bet hook while collapsing resolution to one currency bucket (no per-item payout splitting or five-way balance checks — much simpler than per-bet currency).
  - Betting flow: pick item → place a bet (type + number/amount) → add to a running placed-bets list → stack more → **Spin** resolves all placed bets against the winning pocket and reports one net result in the chosen item.
  - Bet types (audio-friendly, menu-selectable): red/black, even/odd, low/high (1:1); dozen/column (2:1); straight single number (35:1). Consider a "custom number set" bet with auto-computed `floor(36/count):1` odds as an audio-native inside-bet generalization. Wheel defaults to European (37 pockets, single zero); `pocket_count` + red/black sets + payout table in `roulette.table`.
  - Still needs a new betting UI (the placed-bets list + add/remove/clear + spin) — the one part that genuinely extends the shared betting shell rather than filling in "the middle." *Medium-high.*
- **Mines / cash-out grid** ⭐ — reuses higher or lower's streak + bank-or-risk + `safe_cap`. Reveal safe tiles one at a time; each safe pick grows the pot, one mine busts the round, cash out anytime. New mechanic: spatial safe/unsafe picking. Reads great in audio ("tile three, safe, pot is now X"). *Medium.*
- **Video poker** — reuses the card deck (blackjack / higher or lower) + payout-by-rank tiers (dice). New mechanic: **hold and redraw** — choose which of five dealt cards to keep before the second draw; payout by final hand rank. *Medium-high.*
- **Keno** — reuses lottery's weighted draw + slots' match-count payout. New mechanic: **pick your numbers** before the draw; payout scales with how many you match. *Medium.*
- **Audio Simon / memory** ⭐ (signature) — reuses higher or lower's streak payout. New mechanic: remember and repeat a growing sound sequence. The one idea where audio-only is an **advantage**, not a constraint — a sighted and a blind player play it identically. *Medium.*
- **Wheel of fortune** — reuses slots spin + lottery weighted prizes; a single weighted spin lands on a prize/multiplier segment. Thin on new mechanics unless paired with a risk choice. *Low-medium.*
- **Craps** — reuses the dice roller; adds a pass / don't-pass rule layer over rolls. Rules-heavy to convey in audio. *Medium.*
- **Skip — War / high card:** it's higher or lower with the streak removed. Too redundant to justify a slot.

**Unlock-rank note:** the minigames panel is alphabetized, so any new game's unlock rank must fit its alphabetical position and will shift later games up 10 ranks (see [[project_minigame_build_guide]], Section 5).

## Meta / progression

- **Achievement rewards** — achievements only unlock today; let some grant a **one-time, rank-scalable reward of one of the five items** (cookies, money/coins, auto cookies, manual cookies, baking speed) on unlock, turning the achievements into a progression layer. **Fully specced with the dev 2026-08-24.** *Low-medium.*
  - **Grant mechanic:** `eval_event_amount(amount, rank)` for scaling (`100*rank` syntax; plain numbers work too) → `apply_amount_effect` for coins/cookies/autocookie/manulcookie; `cookiespeed` needs the rank-style special-case (`cookiespeed += amount; clicktime -= amount;`). Applied in `show_achievement` on unlock; **not retroactive** (already-unlocked achievements in the saved dict never re-fire). NOT prestige points (quests own that), NOT a percentage modifier (one-time grants only).
  - **Announcement — Option B (chosen):** each rewarded achievement gets its OWN custom reward sentence in config, with an `%amount%` token (e.g. `The dealer tosses you %amount% cookies for a hand well played.`); it's appended after the achievement's name + description. **Rewards ALWAYS announce — even silent achievements speak their reward line** (the silent flag still suppresses non-rewarded unlocks as today).
  - **Config format:** append 3 optional trailing fields → `…:Flavor:reward_target:reward_amount:reward_message`. Existing lines (no reward) stay untouched. Current field order is `category:id:stat:threshold:silent:hidden:name:description:hint`, parsed via `parse_delimited_line(line, 8)` — bump that so the reward fields don't get absorbed into the hint.
  - **Target set (LOCKED): the 38 achievements with threshold >= 5000, EXCLUDING the `baking` and `economy` categories** — the endgame Eternal/Infinite/Singularity/Deity/Legend tier (baking_slots 8, lottery 9, blackjack 4, slots 4, combos 4, baker_events 3, dice 2, quests 2, flipper 1, highlow 1). Raising the numeric bar barely trims baking/economy (their thresholds run to the billions), so excluding those two categories is what curates it. Regen: `awk -F: '$4+0>=5000 && $1!="baking" && $1!="economy"'`. NOTE: highlow streak achievements (Untouchable 15, Impossible Run 20) fall below the 5000 bar and are NOT in the 38 — hand-add if streak feats should be rewarded.
  - **Build sections (4, docks last):** 1) Parser + reward format (class fields + parse optional trailing fields). 2) Apply on unlock + announce (eval-scale → grant → speak the custom message). 3) Populate & balance (write the 38 reward messages, pick each target item + amount curve). 4) Docks (readme config reference for the reward fields + changelog).

## Quality of life

- **Number-format setting** — ✅ **SHIPPED in 6.4** (Raw / Suffix short / Suffix long / Scientific, in game settings).
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

**Build sections (in order; docks always last per [[feedback_docks_last]]):**
- *Prep (done):* `check_rank()` refactor in `ranks_table.nvgt` — the five minigames call it; higher or lower adds the sixth caller.
1. **Config table** — `highlow.table`. ✅ done/committed.
2. **Parser** — `highlow_table.nvgt` + `parse_highlow_table(...)` + `main()` hookup with load-failure guard.
3. **State & stats** — runtime globals, `stat_hl_*`, `highlowmode` bool; save/load in `savefuncts.nvgt`.
4. **The minigame** — `highlowgame()` + `highlow_game_input()`; round logic (draw/guess/streak/cash-out), `safe_cap` payouts, `check_rank()`+`check_achievements()`, sounds.
5. **Menu & rank gating** — panel button in `mingamsmenu` (alphabetized) + `rank < 50` gate; move slot machine gate `50 → 60`; `handle_rank_unlock` `"highlow"` case; `ranks.table` (slots→60, mgr→70, combos→80, add rank-50 higherlower) + `combos.table` (`enabled` 70→80); `minigame_input()` `"highlow"` dispatch case.
6. **Settings toggle** *(easy)* — `highlowmode` auto-reveal checkbox in `settings_menu.nvgt` + save/read + `reset_game_settings()` default.
7. **Achievements & quests** *(harder)* — new tracks in `achievements.table` + `quests.table` for the `stat_hl_*` stats, mirroring the other minigames.
8. **Dock updates** *(LAST)* — readme gameplay blurb + config reference + rank/order header shifts; `changelog.txt` 6.4 entry.

(6 and 7 were swapped from the original plan: settings is a one-checkbox change, achievements/quests is the heavier lift, so do the easy one first.)

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

**Minigame code template (reviewed from the existing five; use blackjack as the closest model).** File split: game loops in `minigames.nvgt`; the panel `mingamsmenu()` in `menu.nvgt` (lines ~245-340); the settings screens (`gamsetsmenu`/`soundsetsmenu`/`usersetsmenu`/`preffsmenu` + the minigame auto-mode checkboxes) in **`settings_menu.nvgt`** (moved out of menu.nvgt); persistence + `reset_game_settings()` in `savefuncts.nvgt`; shared `minigame_input()` in `extrafuncts.nvgt` (~842-897). Existing game functions: `flipgame()` 1, `slotsgame()` 202, `jackgame()` 472, `lotterygame()` 763, `dicegame()` 843 — each paired with a one-line `*_game_input()` wrapper that calls `minigame_input("<name>")`.

Every loop follows the same shape (clone it):
- `form.reset()` → `create_window(...)` → controls → `set_disallowed_chars` on the bet field → `form.focus(...)`.
- **5-currency bet selector** `betChoice` list, identical everywhere: `0 Cookies, 1 Currency, 2 Auto Cookies, 3 Auto Cookie Speeds, 4 Manual Cookies`. Money entered as dollars → ×100 (`selectedBet == 1 ? value * 100 : value`).
- `while(true)`: `wait(5)` + `form.monitor()`; a balance clamp block; the `*_game_input()` call — **guarded by `if focus != betInput`** when there's a text bet field (blackjack/dice/slots), unguarded otherwise (flipper/lottery); `ESC → mingamsmenu()`; then **`check_rank(); check_achievements(loadedAchievements, achievementsUnlocked);`** — the rank-up block was factored into `check_rank()` (in `ranks_table.nvgt`) as a prep commit, so **call it, don't paste** (the five minigames already call it; higher or lower just adds a sixth call). Note the main clicker loop in `game.nvgt` keeps its own inline `bakemode`-aware variant, so `check_rank()` is minigame-behavior only (always `speak_buffer`). Then round resolution.
- Round resolution: deduct bet, resolve, add winnings per `selectedBet` branch; **all payouts wrapped in `safe_cap`** (`floor(safe_cap(bet * multiplier))`) — matters most here since the streak pot compounds; the `selectedBet == 3` (speeds) branch adjusts `clicktime` inversely.
- Auto-mode branch: read the game's bool → if on, `mini_wait_async(...)` reveals after a delay; if off, `dlg_buffer("… Press enter or space to reveal.")`.

Higher-or-lower-specific wiring beyond the clone:
- Add a dispatch case `"highlow"` in `minigame_input()` so `Ctrl+S`/`Ctrl+L` re-enter the game (it currently routes flipper/slots/blackjack/lottery/dice by name).
- `mingamsmenu()` (`menu.nvgt`): add the panel button in its **alphabetized** slot with a hardcoded `rank < 50` gate, and **move the slot machine's gate `rank < 50` → `rank < 60`** (the "rank 50 or higher" check at ~line 326/329).
- `highlowmode` auto-reveal bool: declare in `dec.nvgt` (default true), add a checkbox in `gamsetsmenu` (**`settings_menu.nvgt`**, next to `jackautodraw`/etc.), save/read in `writepreffs`/`readpreffs` and default it in `reset_game_settings()` (`savefuncts.nvgt`).
- `stat_hl_*` stats: declare in `dec.nvgt`, save/load in `savefuncts.nvgt`.
- Settings persistence for the auto toggles is the `flipmode`/`jackmode`/`lotterymode`/`dicemode`/`slotmode`/`orderedSyms` pattern (all default true, saved in the game-settings file).

**Unlock rank arrangement (decided, NOT yet applied — do it as part of the build, not standalone).** Higher or lower unlocks at **rank 50**. **Rationale:** the minigames panel is **alphabetized**, so "Higher or lower" (H) sorts before "Slot machine" (S) and must unlock at an *earlier* rank than slots, otherwise unlock order wouldn't match the panel order. So slots and everything after it each shift up 10.
- Current `ranks.table` ladder: 10 blackjack, 20 flipper, 30 lottery, 40 dice, **50 slots, 60 slot manager, 70 combos**.
- New ladder: 10 blackjack, 20 flipper, 30 lottery, 40 dice, **50 higher or lower, 60 slots, 70 slot manager, 80 combos**.

Concrete edits when building:
- `ranks.table`: change the slots line to rank 60, the slotmanager line to rank 70, the combos line to rank 80, and add a rank-50 `higherlower` unlock line (same format as the others).
- `combos.table`: change `enabled=true:70` → `enabled=true:80` (the combos *gate* lives here; the ranks.table line is only the announcement).
- Slot-manager gate needs no code change: `slotManagerUnlockRank` auto-derives from the ranks.table slotmanager line (`ranks_table.nvgt`).
- **Hardcoded minigame gates in `menu.nvgt`** ("rank X or higher" checks): move the **slot machine** check `50 → 60`, and add a new `rank < 50` check for higher or lower.

**Why bundle it with the build, not now:** doing the shift standalone would (because gates re-derive on launch) **temporarily re-lock the slot machine for existing saves at rank 50–59, the slot manager at 60–69, and combos at 70–79** until they rank up, and leave rank 50 with no new unlock until the minigame ships. Shipping the shift together with the rank-50 unlock avoids that.
