---
name: project_quest_rewards_plan
description: "7.6 first feature, design + 2-section plan APPROVED (2026-10-29), not built: quests pay an item reward (money, cookies, auto cookies, manual cookies; rank-scaled like achievement rewards) the moment they finish, on top of prestige points. Dev chose this over permanent quest bonuses (which would duplicate the prestige store). Draft + open questions; nothing approved or built."
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

## Sections (APPROVED 2026-10-29; **7.6, the FIRST new feature**, before the prize wheel; on the todo list as Unfinished; not to be built until the dev says so)

1. **Table, parser and code (merged: the table alone has nothing to test)** — quests.table settings reward_1..reward_5 (coins, rank-scaled; amounts tuned against achievement rewards), quest_complete_message, quest_complete_sound=questcomp.ogg (relative to sounds/misc); optional override fields at the end of a quest line (reward_target:reward_amount:reward_message, like achievements; no cookiespeed); parser reads them; new quests buffer (create_buffer, F6 toggle on the main screen and in minigame_input, non-interrupting); a saved per-run set of rewarded quest ids, cleared at prestige and new game; a check run wherever check_achievements runs that announces + pays; the no-back-pay migration on load; Control L keeps the set. Tests: finish a quest (temporary low threshold) and hear the message and get the money; it doesn't repeat; an override pays its item; F6 mutes the buffer.
2. **Docs** — readme (quests section, quests.table reference for the new settings and override fields, F6 in the key lists, buffers list), changelog entry, memory.
