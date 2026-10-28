---
name: feedback_flag_sections_without_tests
description: When proposing or finishing a build section that has no testing steps (nothing reachable or visible yet, or docs only), say so plainly to the dev instead of skipping silently.
metadata:
  node_type: memory
  type: feedback
---

If a build section has no testing steps, tell the dev explicitly — both when presenting the section plan and in that section's summary (instead of "ready for step 1?", say there are no steps and why, then "commit now" if appropriate).

**Why:** the dev asked (2026-10-28, double or nothing plan) — they rely on the build → review → steps routine ([[feedback_test_steps_one_at_a_time]]) and want to know when a section can't be tested rather than wondering if steps were forgotten.

**How to apply:** in a section plan, mark untestable sections (e.g. a config table nothing reads yet, code not reachable until a later menu section, docs) and offer to merge them into a neighbouring testable section.
