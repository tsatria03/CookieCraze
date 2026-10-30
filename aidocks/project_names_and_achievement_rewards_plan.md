---
name: project_names_and_achievement_rewards_plan
description: "7.6 entries 8-9 DONE (2026-10-30): proper prestige store item names (prestige_item_name: autocookie -> auto cookies, manualcookie -> manual cookies, coin/coins -> money, capitalized; ids unchanged) and achievement rewards moved into a [rewards] section of achievements.table (old inline fields still read). Built one at a time, prestige names first."
metadata:
  node_type: memory
  type: project
---

# Prestige names + achievement rewards section (7.6)

Chosen by the dev (2026-10-30) from suggestions for 7.6's last entries; no minigames. Leaves 1 entry free.

## 1. Prestige store item names (entry 8, a "Fixed a bug where" entry)

The prestige store showed ids with underscores swapped for spaces ("starting autocookie 3", "coin multiplier 1", lower case). New prestige_item_name(id): split on "_", map words autocookie -> "auto cookies", manualcookie -> "manual cookies", coin / coins -> "money", join with spaces, capitalize the first letter (like single_store.nvgt store_item_name from 7.5). Used in all 6 menu.nvgt places that built names. Ids unchanged (purchases are saved by id). Readme prestige.store item_id reference updated. 1 testing step.

## 2. Achievement rewards [rewards] section (entry 9)

achievements.table gains section headers: everything before the first header is categories + achievements as now; a [rewards] section at the end lists `achievement_id:target:amount:message` (message may contain colons). The 45 reward lines lose their three trailing fields. Lines that still carry inline reward fields keep working (older and modded files). Readme achievements.table reference updated. Test: a reward still pays (temporary low threshold on an unlocked-not-yet achievement, don't save afterwards).

## Build log

- **Part 1 (prestige names) BUILT, PASSED, DOCS DONE 2026-10-30:** prestige_item_name in prestige_store.nvgt, used in the 6 menu.nvgt spots; Endless Head Start read Endless starting money / auto cookies / manual cookies; a purchase read "You bought 1 Endless money multiplier for 145 prestige points". Readme item_id reference updated; 7.6 changelog entry 8 of 10.
- **Part 2 (achievement rewards section) BUILT, PASSED, DOCS DONE 2026-10-30:** 45 reward lines stripped to 9 fields, [rewards] section appended (verified: old and new files parse to identical achievements + rewards); parser reads section headers (before the first header = categories + achievements), applies [rewards] after reading, section wins over inline fields. Test: Slot Singularity temporarily at 1 paid 528.75 thousand auto cookies (50*rank at rank 10,575); dev saved first and quit without saving; achievements.table restored. Readme achievements.table reference + section-header overview updated; 7.6 changelog entry 9 of 10.
