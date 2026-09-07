---
name: project_prestige_points_cumulative
description: "Prestige points are awarded from a CUMULATIVE lifetime quest tally, not this-run completions. points_earned = points_per_prestige * (completedQuestIds.get_size() + total_endless_completions()); you earn points even with zero new completions this run."
metadata:
  node_type: memory
  type: project
  originSessionId: 6e820f7e-c923-43e9-bd1c-7c123f031f23
---

Prestige-points payout is **cumulative across your whole progression, not per-run** — an important, non-obvious detail that the readme originally got wrong.

## The formula (cycrz.nvgt, resetgame(prestige) flow)
`pointsEarned = int(floor(perQuestPoints * (double(completedQuestIds.get_size()) + total_endless_completions())))`, where `perQuestPoints = eval_event_amount(points_per_prestige, prestigeLevel)` (default `10*rank`, rank = prestige level).

- `completedQuestIds` = the set of finite (one-time, `advance`) quests EVER completed. It **persists across prestiges** — only cleared on a brand-new game (cycrz.nvgt else branch), never by a prestige or `reset_game_state()`. Endless quests are NOT added here (`mark_completed_quests` skips `infinite`).
- `total_endless_completions()` = sum of every endless quest's completion count (`questEndlessCounts`), also cumulative, grows unboundedly ([[project_endless_quests_plan]]).

So each prestige pays `points_per_prestige × (distinct finite quests ever completed + all endless completions)`. **You earn points even on a run where you complete nothing new**, as long as your lifetime tally is non-zero.

## Why this matters / the ambiguity
- The **milestone resource reward** IS gated on completing quests this run (`quests_reward_earned()` / `require_all`), but the **points are NOT** — an internal inconsistency.
- The readme originally described points as strictly per-run ("quests you completed before prestiging", "no points if none"), which was FALSE. Corrected in 6.8 to describe the cumulative behavior (player-facing Prestige section + the `points_per_prestige` config reference).
- The cumulative behavior **predates** the endless-quest feature (it's inherent to using the persisting `completedQuestIds`); the endless work only added the `+ total_endless_completions()` term.
- Dev was asked whether cumulative was intentional; evidence suggests it may have been an oversight (docs said per-run, and `completedQuestIds` arguably should reset per prestige if per-run was intended), but **dev chose to leave it cumulative as of 6.8**. Revisit per-run vs cumulative only if the dev raises it.
