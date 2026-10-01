---
name: project_release_status
description: "Release status: 6.9 is RELEASED and complete (2026-09-30) — its changelog block is frozen at 10/10; the next change opens a 7.0 MAJOR block (cap 20 entries) and bumps build/version.txt to 7.0 — dev-decided, NOT 6.10. Lists what 6.9 shipped."
metadata:
  node_type: memory
  type: project
---

**6.9 is released and complete (dev, 2026-09-30).** Treat it as frozen:
- Do **not** add, edit or reorder entries in the `New in 6.9.` block of `cycrz/docks/changelog.txt` (it is also full, 10 of 10 — [[feedback_changelog_rules]]).
- **The next version is 7.0, not 6.10 (dev-decided 2026-09-30).** The **first** player-facing change after this opens a new `New in 7.0.` block at the top of the changelog **and** bumps `build/version.txt` from 6.9 to 7.0 in the same turn ([[feedback_update_build_version_txt]]). Never hand-edit `src/includes/version.nvgt`.
- 7.0 is a **major** block, so it holds up to **20** entries, not 10 ([[feedback_changelog_rules]]).
- **Site updater issue to fix before the release AFTER 7.0 (e.g. 7.1):** `build/site_updater.ps1` does two replaces per line of the github.io HTML: tags (`tools.py` builds tag = "V" + version + "0", e.g. V6.90) via `V\d+\.\d+0`, then the title (e.g. V6.9) via `V\d+\.\d+(?<!0)` — the trailing-0 lookbehind is what tells title from tag. Releasing 7.0 itself works (old title V6.9 is replaced with V7.0). But from then on the title "V7.0" ends in 0, so the title pass skips it and it's too short for the tag pass — the site title stays "V7.0" forever. Same after any x.0 / x.10. Fix needs the site HTML to anchor the title by surrounding text. Explained to the dev 2026-09-30 (correcting the earlier "blocks 7.0" claim); not yet fixed. See build/docs bug #1 in [[project_bugs_build_docs]].
- Bug-fix batches still get their changelog + todo updates per fix, as soon as the dev confirms each works ([[feedback_docks_last]] exception).

**What 6.9 shipped (newest first, as in the changelog):** slot machine payouts scaled by checked-symbol count + check-payouts button ([[project_slots_scaled_payouts_plan]]); Escape blocked mid-round in blackjack / higher or lower; settings Cancel/Escape undo changes; menu search accepts letters after purchase prompts; Ctrl+L keeps saved quests; settings Save from the main menu no longer overwrites a slot; achievements saved; new game no longer inherits prestige progress ([[project_high_bugs_fix_plan]]); Ctrl+S works in minigames + slots takes the bet at spin; store crash at max baking speed fixed.

**What's open next:** the Medium and Low player-facing bugs listed under "Open after 6.9" in [[project_bugs_player_facing]], plus the remaining minigame payout exploits (dice always pays 8x, blackjack pays 3x, higher or lower, gold/diamond tickets) in [[project_bugs_exploits_balance]].
