---
name: feedback_plan_features_in_memory
description: "Before coding any new feature, write the agreed plan (decisions + build sections) into an aidocks/ memory file first."
metadata:
  node_type: memory
  type: feedback
  originSessionId: 6e820f7e-c923-43e9-bd1c-7c123f031f23
---

When planning a new feature, record the finalized plan in an `aidocks/` memory file **before** writing any code. Capture the locked design decisions, the config/data shape, and the numbered build sections (commit-safe order), then add the one-line pointer to `aidocks/MEMORY.md`. Only start coding after the plan file exists and the dev has given the go-ahead.

**Why:** The dev works through features as a discussion, one decision at a time, then builds section by section over multiple turns/commits. A written plan survives context compaction and session breaks, keeps the agreed decisions from drifting, and gives a checklist to build against. Pairs with [[feedback_confirm_before_implementing]] (plan, don't jump to code) and [[feedback_docks_last]] (final section = docks).

**How to apply:** Once the design is settled, `Write` an `aidocks/project_<feature>_plan.md` (frontmatter `type: project`) holding: locked decisions, config format, and the build sections; index it in `MEMORY.md`. See [[project_endless_prestige_plan]] as the template and [[project_minigame_build_guide]] for the section-per-commit rhythm.
