---
name: project_quest_rewards_plan
description: "Feature IN DISCUSSION (2026-10-29): quests pay an item reward (money, cookies, auto cookies, manual cookies; rank-scaled like achievement rewards) the moment they finish, on top of prestige points. Dev chose this over permanent quest bonuses (which would duplicate the prestige store). Draft + open questions; nothing approved or built."
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

(none yet)
