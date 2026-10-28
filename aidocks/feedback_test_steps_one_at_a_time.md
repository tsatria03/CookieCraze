---
name: feedback_test_steps_one_at_a_time
description: "Give the dev testing steps ONE per message: state step 1, wait for their result, then give the next. Never list all the test steps at once."
metadata:
  node_type: memory
  type: feedback
---

When a fix or section is ready to test, walk the dev through the testing steps **one at a time**: give a single step (what to do, and what they should hear or see), then stop and wait for their result before giving the next one. Don't put a numbered list of every step in one message. Say how many steps there are in total so they know how far along they are (e.g. "Step 1 of 6").

**Why:** the dev asked for this on 2026-10-26 ("go through the testing steps one at a time, not all in one message"). They test by screen reader in the running game, and a long list of steps is hard to hold onto while switching between the game and the conversation.

**Build → review → ask (dev, 2026-10-27, for every plan section from now on):** after building a section, review your own changes first (re-read the diff for compile-breakers, braceless-if traps, CRLF, missed wiring, wrong behaviour) and fix anything found; then send the summary including what the review checked/found, and **ASK whether the dev is ready for the first testing step** — do not send step 1 until they say so. Later steps still go one per message after each "passed".

**One action per message, even inside a step (dev, 2026-10-28):** if a step involves several things to do or check, give ONLY the first action (plus what they should hear), wait for the result, then give the next action. Never put a numbered list of 3–5 actions in one step message. Number them as parts so progress is clear, e.g. "Step 1 of 3, part 1 of 4". Count parts when stating the section's step count where useful.

**State the step count in every build summary (dev, 2026-10-28):** in each section's summary, before asking "ready for step 1?", say how many testing steps the section has (e.g. "This section has 3 testing steps."). Every section except docs; a docs section says it has none ([[feedback_flag_sections_without_tests]]).

**Step 1 goes in its own message, apart from the build summary (dev, 2026-10-27).** After a build turn's summary ("what changed", Files changed, relaunch note), make a tool call (e.g. a memory/plan update) so the step lands as a separate message block, then send only the step text ("Step 1 of N: …"). Never tuck the step at the bottom of the explanation.

**How to apply:** at the end of a build turn, summarize what changed, then give only step 1 (plus the total count) as a separate message. If a step fails, stop and fix before moving on. Once every step passes, check git log ([[feedback_check_git_log_for_commits]]) and continue to docs per [[feedback_docks_last]]. Related: [[feedback_pause_after_each_fix]].
