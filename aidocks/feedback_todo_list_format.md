---
name: feedback_todo_list_format
description: "cycrz/docks/todo_list.txt entry format: finished items are prefixed '**Finished. ' and pending items '****Unfinished. ', each a plain-text sentence; two sections (unfinished on top, finished below)."
metadata:
  node_type: memory
  type: feedback
---

`cycrz/docks/todo_list.txt` uses a fixed per-line prefix convention:
- **Finished items:** `**Finished. <description>`
- **Unfinished / pending items:** `****Unfinished. <description>` (four asterisks, then `Unfinished. `).

Two headers split the file: `####These are the things that need to be finished for future versions of the game.` on top (pending items go under it), then `##These are the things that are already finished from earlier versions of the game.` (finished items below). Newest entries go at the top of their section.

Each entry is **one plain-text sentence** (or a few) — no markdown (`**bold**`/`*italics*`), no leading numbers, since a screen reader would read the symbols literally. Write it in the **imperative task voice** of the existing entries (`Add a …`, `Make it so …`, `Fix …`, `Replace …`), describing the feature itself — **not** the backlog's `Name — description. Difficulty.` shape. Drop difficulty ratings and any leading `Feature name.` when copying an item from the [[project_feature_ideas]] backlog. It doubles as the durable human record alongside the changelog ([[feedback_changelog_rules]]).

**Why:** the dev keeps this file by hand as a project log; matching the exact prefix keeps it consistent and screen-reader-clean.

**How to apply:** when marking something done, add `**Finished. …`; when logging a new backlog item, add `****Unfinished. …`. Don't invent a different marker (an earlier edit wrongly used a bare `****` with no `Unfinished.`).
