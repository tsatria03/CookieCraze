---
name: project_number_overflow_cap
description: "Plan for 6.3: stop values printing Inf by clamping the growing balances/stats to 1e308, not just the cost formulas (which safe_cap already covers)."
metadata:
  node_type: memory
  type: project
  status: implemented-6.3
---

# Number overflow / `Inf` cap — implemented in 6.3

**Shipped 6.3** (version.txt bumped, changelog block added). Implementation as below; kept the `1e308` ceiling and `double` types. If `Inf` still appears somewhere, the miss is an add/display local that skips both a `floor(safe_cap(...))` normalizer and the loop clamp — search for a value shown by `format_number`/`convert_to_currency` whose source isn't wrapped.

**Player-facing goal (6.3):** players should stop seeing `Inf` (and `$inf`) show up in late-game numbers. This is the visible payoff of the fix — call it out in `changelog.txt` when it ships. Bump `build/version.txt` to `6.3` per [[feedback_update_build_version_txt]] / [[feedback_changelog_rules]].

## Root cause (audited 2026-08-23)

The dev decided values must never exceed `1e308` ([[project_data_driven_config]] uses `double` everywhere; ~1.8e308 is the finite-double ceiling). `safe_cap()` (`extrafuncts.nvgt:40`, clamps to `[0, 1e308]` and maps NaN→1e308) already guards **every cost/price formula** (`buy_item`, `calc_max_buyable`, `calc_total_upgrade_cost`, `calc_automation_cost`, `calc_slot_cost`, ticket cost at `lottery_table.nvgt:104`).

**But the values that actually grow are never capped.** The balance accumulators and every lifetime `stat_*` total are plain `double += …` with no clamp:
- The clicker main loop (`game.nvgt:21–46`) floors `coins/cookies/autocookie/manulcookie/cookiespeed` and clamps the **low** end to 0 — **no high-end cap**, and it never touches `stat_*` at all.
- So a balance climbs past `1e308`, an add yields `inf`, and `format_number` (`extrafuncts.nvgt:139` → `"Inf"`) / `convert_to_currency` print it.

Why earlier one-at-a-time fixes never converged: they capped *prices*, but the *balances/stats* were the uncapped thing. The `stat_*` totals only ever grow and are floored/capped nowhere, so they hit `inf` **first**.

Note: this is an `inf`/saturation problem, NOT the old int32 negative-count bug — that one is fixed (`ticket.owned` is now `double`; audit found no surviving int32 cast on a player value). `string_to_int` (`extrafuncts.nvgt:103`) is int32 by design and correct — it only parses small integer config fields (min_rank, weights, difficulty, window ms); player amounts use `string_to_number` (double). Not part of this work.

## The fix — option 1 + option 2 together

Keep `double` + the `1e308` ceiling. Constant to reuse: `1e308` (same value `safe_cap` uses).

**Option 1 — centralize a high-end clamp in the clicker loop** (`game.nvgt:21–46`, alongside the existing floor/low-clamp block). For each of: `coins`, `cookies`, `autocookie`, `manulcookie`, `cookiespeed`, add `x = min(x, 1e308)` (or `x = safe_cap(x)`). This is the cheap net for balance drift, but only runs while `clickergame()` is looping — a single huge minigame/prestige payout can still momentarily read `inf` on its own result screen, which is why option 2 is also needed.

**Option 2 — clamp at the source for the totals and the off-loop payouts.** Simplest is to wrap the assignment with the existing `safe_cap` (no new helper strictly required, but a `double add_capped(double base, double delta) { return safe_cap(base + delta); }` reads cleaner). Apply to:
- **All `stat_*` `+=`** (they are never in the loop): `game.nvgt:168,218`; `menu.nvgt:492`; `extrafuncts.nvgt:231`; `minigames.nvgt:1069`; prestige adds. Full `stat_*` list is `dec.nvgt:91–125`.
- **cookies/coins adds that happen off-loop** (minigame/prestige result screens): `minigames.nvgt:454-455,623-624,671-672,709-710,743-744,1068-1069`; `prestige_table.nvgt:159-160,196-197`; `cycrz.nvgt:278` (prestige starting bonuses); `extrafuncts.nvgt:231-234` (`apply_amount_effect`).
- **Shop/production adds** (autocookie/manulcookie/cookiespeed/coins): `menu.nvgt:491` (sell coins), `1060-1096`, `1162-1185`, `1443-1445`, `1529-1541`. Option 1 will re-clamp these next loop tick, but capping at the add prevents a transient `inf` on the purchase-summary text.

## Watch-outs when implementing

- Don't reformat / re-indent surrounding code ([[project_angelscript_braceless_if]], [[feedback_dont_flag_indentation]]) — several of these sites are one-line braceless branches; keep them one statement.
- `cookiespeed` also drives `clicktime` (`menu.nvgt:1445` decrements clicktime); the loop already clamps `clicktime` to `[50,1000]`, so capping `cookiespeed` is display-only there — fine.
- `stat_*` are saved/loaded as doubles (`savefuncts.nvgt`), so capping them doesn't change the save format.
- Flag a commit break point before the edit sweep ([[feedback_stage_commits_before_big_changes]]); it touches game.nvgt, menu.nvgt, minigames.nvgt, prestige_table.nvgt, extrafuncts.nvgt, cycrz.nvgt.
- Dev runs/builds, not Claude ([[feedback_dont_run_or_build_the_game]]); end the edit turn with a Files changed list ([[feedback_list_modified_files]]).
