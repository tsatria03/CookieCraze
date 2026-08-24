---
name: feedback_docks_last
description: "When breaking a build/feature into sections, the LAST section is always reserved for dock updates (readme + changelog); code/config first, docs last."
metadata:
  node_type: memory
  type: feedback
---

When planning or building a feature in sections, **the final section is always reserved for dock updates** — the player-facing `cycrz/docks/` files (`readme.txt` gameplay + modder config reference, `changelog.txt`, and the `build/version.txt` bump). Everything else — config tables, parsers, globals/state, the feature logic, menu/rank wiring, stats/achievements/quests, settings toggles — comes first.

**Why:** docs describe what shipped, so they're written once the behavior is settled; doing them last avoids rewriting them as the implementation shifts, and keeps the changelog/readme accurate to the final result. Follows [[feedback_changelog_rules]] (changelog is a record of what shipped) and the readme's two-part convention (gameplay blurb + config reference).

**How to apply:** structure every multi-section build plan so the last section is "Dock updates." Don't touch readme/changelog/version until the code and config for the feature are done and verified.
