---
name: reference_legacy_nvgt_source
description: "Source of the legacy NVGT fork the game runs on is at C:\\Users\\tonys\\OneDrive\\Documents\\github\\tsatria03\\misc\\Legacy-NVGT (src/input.cpp etc.); C:\\nvgt holds only the compiled engine."
metadata:
  node_type: memory
  type: reference
---

The C++ source of the legacy NVGT fork ([[project_engine_pinned_nvgt2]]) is at `C:\Users\tonys\OneDrive\Documents\github\tsatria03\misc\Legacy-NVGT` (dev pointed to it 2026-10-25). Engine code is under `src/` — for example `src/input.cpp` (key_pressed / key_repeating / key_down) and `src/input_bind.cpp`.

`C:\nvgt` has only the built engine (`nvgt.exe`, `nvgtw.exe`, `lib/`, `stub/`, `config.properties`, `nvgt.chm`), no source and no include folder — so read engine behavior from the Legacy-NVGT repo, not from C:\nvgt. Read-only reference: don't edit the engine.
