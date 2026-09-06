---
name: project_endless_quests_plan
description: "Approved plan for endless (infinitely-escalating) quests — a quest that, each time you complete it and prestige, returns next cycle with a higher threshold (base x growth^count). Mirrors the endless-prestige model. Each completion permanently adds to the prestige-points multiplier."
metadata:
  node_type: memory
  type: project
  originSessionId: 6e820f7e-c923-43e9-bd1c-7c123f031f23
---

## Status: approved, ready to build. Not yet started.

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
5. **Config conversion (RANK ONLY for now)** — `quests.table`: replace the 14 `reach_rank_*` lines with one endless rank quest, full 12-field form, `difficulty_step=0` explicit; tune base threshold + growth. Other stat ladders are explicitly OUT OF SCOPE for this build (dev will evaluate converting them in a later pass).
6. **Docks** — readme (new quest fields `growth`/`infinite`/`difficulty_step`, endless quests explanation, the reward-scaling change), changelog entry, `build/version.txt` bump.

## Tuning knobs (all in quests.table)
Per endless quest: `growth` (threshold multiplier per completion) · `difficulty_step` (completions per +1 difficulty, 0 = fixed) · base `threshold`. Global: `points_per_prestige` (already exists) now multiplied by finite-completed + endless-completions.

## Version & scope (dev-confirmed)
- **Ships in 6.8** — stay in the 6.8 block until it reaches its 10-entry cap; no version bump for this feature (version.txt stays 6.8).
- **Rank quests only** — convert only the rank ladder now; other quest categories are deferred to a later evaluation.

## Nearest-slot fallback (added during Section 5 discussion)
Required quests only claimed a slot on an EXACT difficulty-tag match, so they were silently dropped when no slot targeted their tag. After the rank-ladder conversion, non-rank quests only span difficulty 1-5, so an endless rank quest with `difficulty_step > 0` climbs its tag past every slot after ~5 clears and would vanish. Fix (in `assign_quests`, the required `!completed` branch): prefer an exact match, else take the NEAREST open slot (mirrors the `completed && advance` branch, which already did nearest-slot). This also quietly fixes the latent drop bug for all required quests.

## Rank quest final parameters (LOCKED)
Single endless rank quest: base threshold **5**, growth **1.25** (>= 1.2 so floored thresholds never repeat; the +1 rule), difficulty **1**, `difficulty_step` **1** (tag climbs +1 per completed cycle, capped at 10 after 9 clears — needs the nearest-slot fallback above), required **true**, advance **false**, infinite **true**. Line:
`reach_rank_endless:Reach rank %threshold%:rank:5:false:true:false:1:1.25:true:1:Reach rank %threshold% to complete this quest. Each time you finish it and prestige, it returns at a higher rank.`

## Open/next-step decisions (resolve during the relevant section)
- (none remaining — rank quest params locked above.)
