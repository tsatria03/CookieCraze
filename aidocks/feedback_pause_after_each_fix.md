---
name: feedback_pause_after_each_fix
description: "After finishing a fix and its docs, stop and end the turn with a summary for the dev to review — never start investigating or asking about the next item in the same reply, even when the dev said to do several in a row."
metadata:
  node_type: memory
  type: feedback
---

After a fix and its dock updates are done, **end the turn there**: give a clear summary of what changed (code, config, readme, changelog, todo, memory) plus how to test, and stop. Do not, in the same reply, start reading code for the next item, run analysis for it, or ask its design question.

**Why:** 2026-09-30 the dev asked to fix four payout exploits in a row and mark each finished. After finishing the dice fix I immediately investigated higher or lower and asked its question in the same message. The dev didn't realize dice had been fixed at all — "You didn't pause to review what you did after you did the dock updates." A batch instruction ("do this for the next 3 as well") sets the workflow per item; it does not mean chain them without a review stop.

**How to apply:** one item per turn, ending with the summary and a single short line like "Ready for the next one when you are." Only start the next item when the dev replies. Pairs with [[feedback_ask_one_question_at_a_time]], [[feedback_confirm_before_implementing]], and [[feedback_list_modified_files]].
