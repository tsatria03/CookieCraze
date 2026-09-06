---
name: project_cookie_xp_rework_plan
description: "Approved plan to rework ranking onto a dedicated cumulative cookieXP stat (SimpleFighter-style), splitting the two redundant rank sliders into a rank modifier (threshold) and an experience modifier (XP per bake). Two mechanics still open."
metadata:
  node_type: memory
  type: project
  originSessionId: 6e820f7e-c923-43e9-bd1c-7c123f031f23
---

**NOTE:** the XP threshold variable this doc calls `cookiesrequiered` was renamed to `xprequired` in 6.8 (its save key too; value is recomputed on load, so no save impact). Read every `cookiesrequiered` below as `xprequired`.

**STATUS: SHIPPED in 6.7.** All four build sections completed, documented, and committed. Kept as the design record. (Notable in-build change: `cookieExpMod` default set to 5, not 1; `difmod` removal deferred to Section 3 for compile-safety.)

Rework ranking to mirror SimpleFighter's level/XP model, so the two rank sliders stop being redundant and selling no longer fights rank progress. Modeled on SimpleFighter (`SimpleFighter/src/includes/main/globals/game.nvgt` + `menus/menu.nvgt:739`). Planned per [[feedback_plan_features_in_memory]]; build after the endless-prestige feature ([[project_endless_prestige_plan]]) is finished. The dev commits each section; docks last ([[feedback_docks_last]]).

## The problem this fixes

Today `cookies` is one balance doing triple duty: it goes UP when baking (`game.nvgt:170,220`), DOWN when selling/shopping/betting/rerolling, and IS the number checked for ranking (`if (cookies >= cookiesrequiered)`). So **selling cookies lowers rank progress** — the money loop fights the rank loop. Also the two sliders `cookiemod` and `difmod` are mathematically redundant: `cookiesrequiered = cookiemod * (rank * (rank * difmod))` = cookiemod × difmod × rank², both linear scalars on the same term. SimpleFighter avoids this: `levmod` scales the XP threshold (cost side) while `xpmod` scales XP gained per kill (income side) — two independent axes.

## Locked design decisions

- **New dedicated `cookieXP` stat** (cumulative, only ever goes up), separate from the spendable `cookies` balance. Baking earns both.
- **Rank on `cookieXP`** instead of the spendable balance. Selling/shopping/betting no longer touch rank progress.
- **Cookie Rank Modifier** (`cookiemod`) unchanged: scales the XP threshold per rank.
- **Cookie Experience Modifier** — replaces `difmod` (retire difmod), a NEW global `cookieExpMod` default **1**, scaling XP gained per bake (the `xpmod` role). Settings slider "Difficulty rank Modifier" is renamed "Cookie Experience Modifier".
- **Threshold formula** keeps `difmod`'s old slot as a fixed constant so the numbers are unchanged: `cookiesrequiered = cookiemod * (rank * (rank * 10))` (10 = old difmod default; with cookiemod default 5 → 50 × rank², identical to today).
- **Pacing = Option A**: keep the current threshold numbers; ranking simply gets more accessible for anyone who sells (that decoupling is the point). The Cookie Rank Modifier default is the safety valve — bump it only if playtest shows ranks flying by. Rank-gated content (minigames 50/60/70, prestige, combos) will be reached sooner; watch it in playtest.
- **R key wording** (mirrors SimpleFighter `menu.nvgt:739`): "You're currently rank {rank} with {cookieXP} experience. Your next rank requires {cookiesrequiered - cookieXP} experience." (no "more" — matches SF and the current line.) The number now only ticks down as you bake.
- **C key** keeps reporting the spendable cookie balance, so R doesn't need to. Minor tidy: C currently says "You baked a total of {cookies} cookies" but `cookies` is the balance (drops on sell) — reword to "You have {cookies} cookies" (or point "total baked" at a real cumulative number).

## XP per bake (LOCKED)

`cookieXP += bakeGain * cookieExpMod` — 1 cookie baked = 1 XP, times the modifier. Added on BOTH bake paths, right beside the existing `stat_cookies_baked += ...` lines: manual at `game.nvgt:171` (`cookieXP += manualGain * cookieExpMod;`), auto at `:221` (`cookieXP += autoGain * cookieExpMod;`). Capped exactly like the other cumulative totals — `cookieXP = safe_cap(cookieXP);` in the per-frame normalizer block after `game.nvgt:47-49`, NO floor, no zero-guard, mirroring `stat_cookies_baked`. Verified: `safe_cap` clamps to `[0, 1e308]`; `stat_cookies_baked` / `stat_coins_earned` / `stat_coins_spent` are already normalized there each frame.

## Save migration (LOCKED — Option B)

On load, if the `cookieXP` save key is absent, seed `cookieXP = cookiemod * ((rank-1) * ((rank-1) * 10))` — the threshold that got them to their current rank. This keeps the player's exact rank and starts their XP bar at the beginning of that rank (rank 1 → 0). Do NOT seed from `stat_cookies_baked`: it counts sold cookies too, so it dwarfs the rank thresholds and would rank veterans up to absurd values on load. The old `difmod` save value is simply dropped (a customized difficulty is lost — acceptable for a rework). Note: the dev plans to start a fresh save, but the migration still matters for other existing players.

## Status: SHIPPED in 6.7 (all four sections built and committed).

## Build sections (commit-safe order — one per turn, pause for commit)

NOTE (compile-safety): `difmod` is referenced by the rank formula (game.nvgt, ranks_table.nvgt, savefuncts.nvgt) AND the settings slider, so it can't be removed until all those are repointed. Removal is therefore deferred to Section 3 (after the slider moves to cookieExpMod). Every section stays compilable.

1. **Globals + save** — DONE (Section 1, compile-safe/additive). `dec.nvgt`: added `double cookieXP = 0;` + `double cookieExpMod = 1;` (difmod KEPT). `savefuncts.nvgt`: cookieExpMod load/reset/save in the preffs (readpreffs/reset_game_settings/writepreffs, `st`); cookieXP load with Option-B migration seed (in readdata, after readpreffs so cookiemod is loaded) + save (writedata, `sd`). No formula change, no removal — purely additive.
2. **Rank + XP gain** — `game.nvgt` + `ranks_table.nvgt`: change the rank check from `cookies` to `cookieXP` (`game.nvgt:242`, `ranks_table.nvgt:137`); swap difmod → constant 10 in the threshold (`game.nvgt:245`, `ranks_table.nvgt:140`, and `savefuncts.nvgt` readdata formula ~line 295); add `cookieXP += bakeGain * cookieExpMod` on both bake paths + `cookieXP = safe_cap(cookieXP)` in the normalizer. **Also add `cookieXP = 0;` to `reset_game_state()` (cycrz.nvgt, alongside `cookies=0` line ~283) so prestige resets rank progress** — critical, else post-prestige rank rockets back up. Keep the rank-up announcement thinning intact.
3. **UI + wording** — `settings_menu.nvgt`: rename the slider to "Cookie Experience Modifier" and bind it to `cookieExpMod` (was difmod). Then **remove `difmod`**: the global (dec.nvgt) + its save/load/reset (savefuncts writepreffs/readpreffs/reset_game_settings). `game.nvgt`: update the R-key line (`:391`) to the experience wording; tidy the C-key line (`:435`).
4. **Docks** — readme (rank/XP model + the two modifiers), changelog entry, `build/version.txt` bump.

## Tuning knobs (all live-adjustable)
Cookie Rank Modifier (threshold scalar) · Cookie Experience Modifier (XP per bake) · threshold constant (in-formula). RETUNED in 6.8: both mods now default **1** (were 5), and the in-formula constant is **4** (was 10). So the default threshold is `1 * rank² * 4 = 4·rank²` (was `5 * rank² * 10 = 50·rank²`) — ranking is much faster by default now.
