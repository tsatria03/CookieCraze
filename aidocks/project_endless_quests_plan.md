---
name: project_endless_quests_plan
description: "Approved plan for endless (infinitely-escalating) quests — a quest that, each time you complete it and prestige, returns next cycle with a higher threshold (base x growth^count). Mirrors the endless-prestige model. Each completion permanently adds to the prestige-points multiplier."
metadata:
  node_type: memory
  type: project
  originSessionId: 6e820f7e-c923-43e9-bd1c-7c123f031f23
---

## Status: SHIPPED in 6.8. All sections (1-6) built and committed, plus the nearest-slot fallback and the combos_broken removal. Kept as the design record. 13 categories converted (see the locked parameter table below); 18 stats stay finite.

Endless quests: an infinitely-escalating quest type so quest progression never dead-ends the way the finite tier ladders do. Modeled on endless prestige ([[project_endless_prestige_plan]]) and slotting into the existing quest `advance`/tier system. Planned per [[feedback_plan_features_in_memory]]; the dev commits each section; code/config first, docks last ([[feedback_docks_last]]).

## Why it fits

Quests already escalate via the `advance` flag + hand-authored tiers (`reach_rank_5 -> 10 -> 20 -> ...`, each a separate line; `find_next_tier()` promotes on completion). The ladder is FINITE — finish the top tier and `find_next_tier` returns null, so that stat is done forever. An endless quest is a **self-tiering advance quest**: one definition auto-generates the next threshold by multiplying, so it never runs out.

Mapping to endless prestige: `infinite` flag = `infinite` flag; `growth` multiplier = `cost_multiplier`; `questEndlessCounts` dict = `prestigeEndlessCounts`; threshold `base x growth^count` = cost `base x mult^count`.

## Locked design decisions

- **Per-quest config fields** (all in `quests.table`, all data-driven): add `growth` (double, threshold multiplier), `infinite` (bool), and `difficulty_step` (int) — inserted BEFORE `description` so descriptions can still contain colons (same approach the prestige store used). Field-count branch keeps all existing 9-field lines parsing.
  - New format: `id:name:stat:threshold:use_percent:required:advance:difficulty:growth:infinite:difficulty_step:description` (12 fields / split on first 11 colons).
  - Old format: `...:difficulty:description` (9 fields) still parses -> defaults `growth=1`, `infinite=false`, `difficulty_step=0`.
- **Escalation cadence = PER PRESTIGE (dev-chosen), never mid-run.** An endless quest holds `base_threshold x growth^count` for the whole run. If you complete it that run and then prestige, its `count` ticks up by one and it returns next cycle harder. Prestige without completing it -> unchanged. (Mid-run re-arm was rejected: most quest stats are lifetime-cumulative, so re-arming mid-run would insta-complete a cascade of tiers.)
- **Each completion counts toward the prestige payout (dev-chosen).** Endless quests are tracked in a NEW `questEndlessCounts` dict (id -> times completed), NOT in the `completedQuestIds` set. Payout becomes `points_per_prestige x (completedQuestIds.get_size() + sum(questEndlessCounts values))`, so every harder clear permanently adds +1 to the multiplier. (A set keyed by id would only ever count an endless quest once, no matter how hard it got — that undercuts the whole feature.)
- **`difficulty_step`** (default 0): completions before the quest's difficulty rises by 1 (capped at 10). `0` = fixed difficulty (the growth lives in the threshold, not the slot). `N` = every N completions it bumps up one difficulty tier, migrating into harder slots as it escalates. **Written explicitly as `0` on the converted endless quest lines in `quests.table`** so the dev can see and tune it (accessibility) even though the parser defaults it.
- **Name reflects the live tier:** let the quest `name` accept `%threshold%` (currently only `description` is templated), so "Reach rank %threshold%" shows the current escalated goal, not a stale number.
- **Rank ladder -> one endless rank quest:** convert the 14 `reach_rank_*` lines into a single endless rank quest (base + growth tuning) as the showcase. Config step, AFTER the engine work.

## The escalated threshold (how it's computed)

`quest_item.threshold` from config is the BASE. At `assign_quests()`, an infinite quest's ACTIVE threshold = `floor(base_threshold * pow(growth, count))` where `count = questEndlessCounts[id]` (0 if absent). Write the escalated value into the `activeQuests` copy (loadedQuests keeps the base). Effective difficulty for slotting = `min(10, difficulty + (difficulty_step > 0 ? count / difficulty_step : 0))`.

Endless quests must stay PERMANENTLY ELIGIBLE — never added to `completedQuestIds`, so `completedQuestIds.exists(id)` stays false and they re-appear every cycle at the escalated threshold. `find_next_tier`/advance promotion does not apply to them (they self-generate).

## Prestige flow changes (`cycrz.nvgt`, around lines 172-183)

Order matters — endless completions this run must count toward THIS prestige's payout (same as finite quests via `mark_completed_quests` before the points calc):
1. Before the points calc, for each active endless quest whose stat >= its active threshold, increment `questEndlessCounts[id]`.
2. `pointsEarned = floor(perQuestPoints * (completedQuestIds.get_size() + sum(questEndlessCounts values)))`.
Reset: `questEndlessCounts.delete_all()` alongside `completedQuestIds.delete_all()` on a full new game (cycrz.nvgt ~224 and the readdata/reset paths).

## Build sections (commit-safe order — one per turn, pause for commit)

1. **Parser + state** — `quests_table.nvgt`: add `double growth; bool infinite; int difficulty_step;` to `quest_item`; extend the split to 11 colons; branch on `parts.length()` (>=12 new, else old 9-field defaults). Add `dictionary questEndlessCounts;`. (Additive; nothing consumes it yet, so it compiles and all current quests keep working.)
2. **Assignment + escalation** — `assign_quests()` / `find_next_tier` handling: compute the escalated threshold + effective difficulty for infinite quests; keep them permanently eligible (bypass the completed-out skip); apply `%threshold%` templating to `name` (in the display path, `get_quest_detail`/menu labels).
3. **Completion counting + reward** — `cycrz.nvgt` prestige flow: increment `questEndlessCounts` for completed endless quests before the points calc; change the payout to include the endless sum. Ensure `mark_completed_quests` does NOT add endless ids to `completedQuestIds`.
4. **Save / load / reset** — `savefuncts.nvgt`: save/load `questEndlessCounts` (mirror `prestigeEndlessCounts`); clear on new game / full reset (cycrz.nvgt + reset paths).
5. **Config conversion (13 categories)** — `quests.table`: for each chosen category, replace its 5-tier ladder (rank = 14 tiers) with ONE endless quest in full 12-field form; tune base threshold + growth per category (rank done: base 5, growth 1.25, difficulty 1, difficulty_step 1). May be split across turns/commits by category group (economy first, then per-minigame). combos_broken already removed.
6. **Docks** — readme (new quest fields `growth`/`infinite`/`difficulty_step`, endless quests explanation, the reward-scaling change), changelog entry, `build/version.txt` bump.

## Tuning knobs (all in quests.table)
Per endless quest: `growth` (threshold multiplier per completion) · `difficulty_step` (completions per +1 difficulty, 0 = fixed) · base `threshold`. Global: `points_per_prestige` (already exists) now multiplied by finite-completed + endless-completions.

## Version & scope (dev-confirmed)
- **Ships in 6.8** — stay in the 6.8 block until it reaches its 10-entry cap; no version bump for this feature (version.txt stays 6.8).
- **Endless categories chosen (13):** economy — `rank`, `cookies_baked`, `coins_earned`, `coins_spent`, `singles_purchased`; activity (one "play" quest per minigame) — `cookie_flips`, `slot_spins`, `blackjack_hands`, `dice_rolls`, `lottery_tickets_scratched`, `highlow_games`, `roulette_games`, `combos_started`. Each converts its 5-tier ladder into ONE endless quest; per-category base threshold + growth tuned at conversion time (rank locked at base 5, growth 1.25, difficulty 1, difficulty_step 1).
- **Not chosen (stay finite / excluded):** capped peak stats `highlow_highest_streak`, `highest_combo_reached` (❌ can't escalate); redundant/weak `bundles_purchased`, `auto_slots_purchased`/`auto_slots_enabled`, `baker_events_fired`/`flipper_events_fired` (passive), the per-minigame `*_wins` (redundant with plays) and luck stats `blackjack_pushes`/`roulette_straight_hits`/`roulette_breaks`, and `penny_flips`/`lottery_tickets_bought` (redundant with the chosen sibling).
- **combos_broken quests REMOVED** (5 lines, done): breaking combos is a negative/failure goal, which violates the positive/neutral-only quest rule (same reason blackjack losing quests were cut). The `combos_broken` stat still exists; only its quests are gone.

## Nearest-slot fallback (added during Section 5 discussion)
Required quests only claimed a slot on an EXACT difficulty-tag match, so they were silently dropped when no slot targeted their tag. After the rank-ladder conversion, non-rank quests only span difficulty 1-5, so an endless rank quest with `difficulty_step > 0` climbs its tag past every slot after ~5 clears and would vanish. Fix (in `assign_quests`, the required `!completed` branch): prefer an exact match, else take the NEAREST open slot (mirrors the `completed && advance` branch, which already did nearest-slot). This also quietly fixes the latent drop bug for all required quests.

## All 13 endless quests — final parameters (LOCKED, for Section 5)

Every quest: growth **1.25**, `use_percent` false, `advance` false, `infinite` true, **`difficulty_step` 1** (all climb their difficulty tag as they're beaten). Only rank is `required` (always on); the other 12 are random-pool. All start at difficulty 1. Field order: `id:name:stat:threshold:use_percent:required:advance:difficulty:growth:infinite:difficulty_step:description`.

| # | stat | id | base | required | diff | diff_step |
|---|---|---|---|---|---|---|
| 1 | rank | reach_rank_endless | 5 | true | 1 | 1 |
| 2 | cookies_baked | bake_cookies_endless | 100 | false | 1 | 1 |
| 3 | coins_earned | earn_coins_endless | 500 ($5.00) | false | 1 | 1 |
| 4 | coins_spent | spend_coins_endless | 500 ($5.00) | false | 1 | 1 |
| 5 | singles_purchased | buy_singles_endless | 5 | false | 1 | 1 |
| 6 | cookie_flips | flip_cookie_endless | 5 | false | 1 | 1 |
| 7 | slot_spins | spin_slots_endless | 10 | false | 1 | 1 |
| 8 | blackjack_hands | play_blackjack_endless | 10 | false | 1 | 1 |
| 9 | dice_rolls | roll_dice_endless | 10 | false | 1 | 1 |
| 10 | lottery_tickets_scratched | scratch_lottery_endless | 10 | false | 1 | 1 |
| 11 | highlow_games | play_highlow_endless | 10 | false | 1 | 1 |
| 12 | roulette_games | play_roulette_endless | 10 | false | 1 | 1 |
| 13 | combos_started | start_combos_endless | 5 | false | 1 | 1 |

Each replaces its stat's 5-tier finite ladder (rank replaces 14 tiers) with this one line. Names/descriptions use `%threshold%` and end with "Each time you finish it and prestige, it returns at a higher goal." (rank uses "at a higher rank.").

## Rank quest final parameters (LOCKED)
Single endless rank quest: base threshold **5**, growth **1.25** (>= 1.2 so floored thresholds never repeat; the +1 rule), difficulty **1**, `difficulty_step` **1** (tag climbs +1 per completed cycle, capped at 10 after 9 clears — needs the nearest-slot fallback above), required **true**, advance **false**, infinite **true**. Line:
`reach_rank_endless:Reach rank %threshold%:rank:5:false:true:false:1:1.25:true:1:Reach rank %threshold% to complete this quest. Each time you finish it and prestige, it returns at a higher rank.`

## Open/next-step decisions (resolve during the relevant section)
- (none remaining — rank quest params locked above.)
