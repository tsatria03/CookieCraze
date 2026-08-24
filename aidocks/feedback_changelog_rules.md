---
name: feedback_changelog_rules
description: "cycrz/docks/changelog.txt is a player-facing record of what changed: prose, reverse-chronological, bump version.txt with each block."
metadata:
  node_type: memory
  type: feedback
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

`cycrz/docks/changelog.txt` is the source of truth for what shipped, and it is a **record of what changed, not a manual**. Player-facing prose, one idea per entry, reverse-chronological (newest block on top). Each new version block pairs with a bump of `build/version.txt` to the same number (see [[feedback_update_build_version_txt]]).

**Reverse-chronological applies WITHIN a block too, not just across blocks.** The newest entry sits at the very top of its version block, directly under the `New in x.y.` header. When adding several entries in one batch, order them so the **last thing built lands highest** — e.g. building minigame → achievements → quests means the block reads quests, achievements, minigame from top down. Each additional entry goes *above* the previous ones, never appended below in build order.

**Entry cap per version block:** a **minor** version block (`x.y`, e.g. 6.4) holds **at most 10 entries**; a **major** version block (`x.0`, e.g. 7.0) holds **at most 20**. When a block is full, don't overflow it — either **consolidate** related changes into a single entry (a whole feature often reads well as one entry) or **roll to a new version block** (and bump `build/version.txt`). Count entries in the current block before adding, and plan a multi-entry feature so it fits.

**Why:** Players read the changelog; engine internals, file names, and how-to instructions don't belong there. The changelog + `todo_list.txt` are the durable human record of the project's evolution.

**How to apply:** Describe the observable change in a sentence or two. Trust the changelog over the readme/todo when they disagree. Keep lines within the dock line limit ([[feedback_dock_line_length_1024]]).
