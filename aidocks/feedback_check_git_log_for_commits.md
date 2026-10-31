---
name: feedback_check_git_log_for_commits
description: "The dev commits their own work between turns; check git log/status before assuming commit state."
metadata:
  node_type: memory
  type: feedback
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

The dev commits their own work between turns (often several commits you didn't make). Before asking about or assuming the commit state — or before a git operation — check `git log` / `git status` to see what's actually there.

**Why:** Assuming uncommitted work or a particular HEAD when the dev has already committed leads to wrong advice and redundant questions. They manage their own commits.

**During step-by-step testing, do NOT check git (dev, 2026-10-27, overrides the 2026-10-25 rule below for test steps).** When the dev says a test step passed, just give the next step — no `git log`, no `git status`, no "no new commit" remark. Only after the LAST step of a section passes, tell them to commit ([[feedback_stage_commits_before_big_changes]]); check `git log` only once they say they've committed that section.

**Outside step testing, when the dev says a fix "passed", "past", "works", "is good" or similar (dev, 2026-10-25):** run `git log` to see whether they've already committed it, and say so — don't ask whether to commit first.

**How to apply:** Don't commit or push unless explicitly asked (see the global rules). When commit state matters, look it up rather than guessing. Related: [[feedback_stage_commits_before_big_changes]].

**Always check the latest commit before starting a section (dev, 2026-10-30):** when the dev says they are ready for the next section, run git log / git status FIRST to confirm the previous section was committed and the tree is clean, then start building. Slipped once on 2026-10-30 (went straight to reading code for the singles section).
