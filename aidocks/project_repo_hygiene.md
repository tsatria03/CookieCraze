---
name: project_repo_hygiene
description: ".gitattributes enforces CRLF on code/data files (with binary rules); what's gitignored; CLAUDE.md + aidocks/ are committed."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

**Line endings:** `.gitattributes` uses `* text=auto` plus explicit `text eol=crlf` on `*.sif *.nvgt *.py *.txt *.bat *.ps1 *.md *.ini` — code/data files are pinned to CRLF so NVGT's parsers and the AngelScript compiler always see Windows endings. Binary types (`*.dat` and audio/archives) are marked binary. The working tree is still a per-file LF/CRLF mix from before this policy (CookieCraze leaned mostly CRLF already); a one-time `git add --renormalize .` is the dev's call to flip existing files. Don't run a manual normalizer — [[feedback_no_crlf_normalization]].

**Gitignored (as of 2026-10-25):** `.claude`, `New File*.txt`, `lib/` (`cycrz/lib/` runtime DLLs exist locally but stay untracked by design — [[project_build_pipeline]]), `releases/` (compiled output + archives), and `user/` (the dev's private folder; that line was an uncommitted edit on 2026-10-25 — leave the folder alone). `cycrz/sounds/` is **tracked** since commit 5eeba37 (156 files). The `*.ini` ignore was removed so `build/tools.ini` commits. Not ignored but should be: `cycrz/errors.txt` and a leftover `src/cycrz/` bundle ([[project_bugs_build_docs]] #7).

**Committed on purpose:** `CLAUDE.md` and the whole `aidocks/` folder (this memory store) are committed — they travel with the repo.
