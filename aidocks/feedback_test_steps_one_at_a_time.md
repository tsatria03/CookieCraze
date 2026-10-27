---
name: feedback_test_steps_one_at_a_time
description: "Give the dev testing steps ONE per message: state step 1, wait for their result, then give the next. Never list all the test steps at once."
metadata:
  node_type: memory
  type: feedback
---

When a fix or section is ready to test, walk the dev through the testing steps **one at a time**: give a single step (what to do, and what they should hear or see), then stop and wait for their result before giving the next one. Don't put a numbered list of every step in one message. Say how many steps there are in total so they know how far along they are (e.g. "Step 1 of 6").

**Why:** the dev asked for this on 2026-10-26 ("go through the testing steps one at a time, not all in one message"). They test by screen reader in the running game, and a long list of steps is hard to hold onto while switching between the game and the conversation.

**Step 1 goes in its own message, apart from the build summary (dev, 2026-10-27).** After a build turn's summary ("what changed", Files changed, relaunch note), make a tool call (e.g. a memory/plan update) so the step lands as a separate message block, then send only the step text ("Step 1 of N: …"). Never tuck the step at the bottom of the explanation.

**How to apply:** at the end of a build turn, summarize what changed, then give only step 1 (plus the total count) as a separate message. If a step fails, stop and fix before moving on. Once every step passes, check git log ([[feedback_check_git_log_for_commits]]) and continue to docs per [[feedback_docks_last]]. Related: [[feedback_pause_after_each_fix]].
