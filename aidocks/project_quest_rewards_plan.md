---
name: project_quest_rewards_plan
description: "7.6 feature DONE 2026-10-30 (built, tested, documented, entry 5 of 10); follow-up: lower the endless rank quest reward: quests pay an item reward (money, cookies, auto cookies, manual cookies; rank-scaled like achievement rewards) the moment they finish, on top of prestige points. Dev chose this over permanent quest bonuses (which would duplicate the prestige store). All design questions answered."
metadata:
  node_type: memory
  type: project
---

# Quest item rewards

**Origin (2026-10-29):** the dev asked whether quests ever reward the player. Today they don't directly: completions only feed prestige (points_per_prestige x total completions; require_all gates the prestige reward). Compared with the referenced game ([[feedback_referenced_game_name]] — never name it), where each of its 437 quests carries a reward type + value (the quest class extends its boost class) and finished quests are claimed on reset as permanent stacking bonuses. **Dev chose: an item reward paid the moment a quest finishes** (not permanent bonuses — those would duplicate the prestige store).

## What exists today (verified 2026-10-29)

- quests.table: 145 quest lines, format `id:name:stat:threshold:use_percent:required:advance:difficulty:growth:infinite:difficulty_step:description` (15 endless). Difficulties used 1 to 5 (41/26/26/26/26).
- **Finishing a quest mid-run is silent** — progress is checked when shown (quests screen, baker info, extrafuncts ~683) and completions are recorded at prestige (quests_table.nvgt ~417/431: completedQuestIds + stat_quests_completed). So this feature also needs a "quest complete" moment: a per-run record of quests already rewarded (saved), checked during play, with an announcement.
- Achievement rewards are the model: optional reward_target (coins/cookies/autocookie/manulcookie/cookiespeed), reward_amount with rank operators (2000*rank), reward_message with %amount%; always announced even when silent; never retroactive. **Quest rewards should NOT offer cookiespeed** (and since 2026-10-29 no shipped achievement gives speed either) ([[project_speed_bet_removal_plan]] — speed only in the store and baker events).

## Open questions (ask ONE at a time, record answers)

1. How amounts are assigned: per-difficulty defaults in [settings] (e.g. one reward per difficulty 1 to 5), optional per-quest override fields, or a reward on every line.
2. Which items (money only, or a mix).
3. Announcement: which buffer (existing ones vs a new quests buffer), sound, message wording.
4. Endless quests: pay every run they finish (likely yes, rank-scaled).
5. Saves/edge cases: quests already finished when the update loads (pay once on load vs not retroactive like achievements).
6. Version/changelog placement (7.5 has 2 entries left).

## Answers

- **Q1 (dev, 2026-10-29): option A** — one default reward per difficulty in quests.table [settings] (e.g. reward_1=coins:500*rank ... reward_5), paid by every quest of that difficulty; any quest line may add optional reward fields at the end to override its default, the way achievements add theirs. A modder's new quest gets its difficulty's reward automatically.
- **Q2 (dev, 2026-10-29): option A, money for all five defaults** (e.g. reward_1=coins:200*rank up to reward_5=coins:2000*rank; exact amounts set when writing the table, compared with achievement rewards so quests don't outshine them). Some quests override with a fitting item (manual baking quests pay manual cookies, auto baking quests auto cookies). Never cookiespeed.
- **Q3 (dev, 2026-10-29): option A, a new quests buffer, muted with F6** (after F1 achievements, F2 combos, F3 events, F4 ranks, F5 boosts), non-interrupting like achievements. Spoken the moment a quest's goal is reached, checked wherever check_achievements runs (main screen, every minigame, store, rank up). Each quest announces and pays once per run even if progress later drops; a prestige brings fresh quests that can pay again (endless at their higher goal).
- **Message + sound APPROVED (dev, 2026-10-29):** quests.table settings quest_complete_message (e.g. "Quest complete! %quest%. You received %reward%."; %quest% = name with goal filled in, %reward% = amount + item, money as dollars) and quest_complete_sound = **questcomp.ogg**, relative to sounds/misc like reroll_sound (dev chose config lines for both this and boosts.store's expire_sound, 2026-10-29) (dev's file, sounds/misc/questcomp.ogg, the quests buffer's sound; **file added by the dev 2026-10-29, renamed from gamequest.ogg to questcomp.ogg the same day**, untracked until they commit it). Override quest lines may carry their own message, like achievements. Endless quests pay every run they finish (covers original Q4).
- **Q5 (dev, 2026-10-29): option A, no back pay** — on the first load after the update (save has no rewarded-quests key), quests already finished this run are silently marked rewarded; only quests finished after updating pay. Matches achievement rewards (never retroactive).

- **Override format changed (dev, option A, 2026-10-30):** the description is the last quest field and may contain colons, so fields after it would be ambiguous. Overrides go in an optional **[rewards] section** below [quests], one line per quest: `quest_id:target:amount:message` (message optional; falls back to quest_complete_message). Quest lines stay unchanged. Like ranks.table's rewards section.

**Idea for later (dev, 2026-10-30):** achievements.table could also move its rewards into a [rewards] section — reward lines average 334 characters (max 392, slot_singularity) vs 225 for all 519 achievements; 45 have rewards. Not planned.

## Sections (APPROVED 2026-10-29; **7.6, the FIRST new feature**, before the prize wheel; on the todo list as Unfinished; not to be built until the dev says so)

1. **Table, parser and code (merged: the table alone has nothing to test)** — quests.table settings reward_1..reward_5 (coins, rank-scaled; amounts tuned against achievement rewards), quest_complete_message, quest_complete_sound=questcomp.ogg (relative to sounds/misc); optional override fields at the end of a quest line (reward_target:reward_amount:reward_message, like achievements; no cookiespeed); parser reads them; new quests buffer (create_buffer, F6 toggle on the main screen and in minigame_input, non-interrupting); a saved per-run set of rewarded quest ids, cleared at prestige and new game; a check run wherever check_achievements runs that announces + pays; the no-back-pay migration on load; Control L keeps the set. Tests: finish a quest (temporary low threshold) and hear the message and get the money; it doesn't repeat; an override pays its item; F6 mutes the buffer.
2. **Docs** — readme (quests section, quests.table reference for the new settings and override fields, F6 in the key lists, buffers list), changelog entry, memory.

## Build log

- **Section 1 BUILT 2026-10-30:** quests.table reward_1..5 = coins 100/200/400/700/1000*rank, quest_complete_message, quest_complete_sound=questcomp.ogg, [rewards] with 15 overrides (buy/enable_auto_slots tiers autocookie 2..10*rank, reach_combo tiers manulcookie 2..10*rank). quests_table.nvgt: parser + quest_reward_target_ok, quest_is_complete, mark_finished_quests_rewarded (already finished = paid silently: run start in reset_game_state, reroll, reload_config fallback, saves without questRewardedCount), check_quest_rewards (called at the end of check_achievements), grant_quest_reward (override, else default for endless_difficulty or the nearest lower). Saved questRewardedCount/questRewarded_i. quests buffer (non-interrupting) + F6 in game.nvgt and minigame_input; sound silent when muted. Test step 1 passed (no back pay on load).
- **Side change during testing (dev, 2026-10-30):** the P key now says "You have X points to spend in the prestige store." (dropped "prestige" before points); store messages unchanged. **No changelog mention (dev).**
- **Step 2 PASSED 2026-10-30** (prestige 35 save, rank ~10,575): Buy 10 scratch tickets paid $10.58 thousand (difficulty 1 default); the endless rank quest also finished and paid $105.75 thousand — its endless_difficulty climbs past 5, so it falls back to reward_5 (1000*rank).
- **TODO AFTER the docs (dev, 2026-10-30): lower the endless rank quest's reward** — it is always active and pays 10x a difficulty 1 quest late in the game. Likely a [rewards] line for reach_rank_endless with a smaller amount (to be discussed). Not done yet.
- **Steps 3-4 PASSED 2026-10-30:** no repeat after Control S + Control L; F6 muted the quests buffer (message silent, stored; Win 75 roulette spins temporarily set to 1, paid $42.3 thousand = 400*rank; quests.table restored). Note: that quest stays marked paid in the dev's save this run.
- **Docs DONE 2026-10-30:** readme F6 key + F1-F6 sentence, quests section reward paragraph, quests.table reference (reward_1..10, quest_complete_message, quest_complete_sound, Rewards section); 7.6 changelog entry 5 of 10; todo item Finished. Also split the over-long prestige store readme line (1237 chars, from the menu memory docs) in two.
