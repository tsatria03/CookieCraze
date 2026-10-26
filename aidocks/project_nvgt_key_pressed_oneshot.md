---
name: project_nvgt_key_pressed_oneshot
description: "key_pressed() is consumed on first read each frame; read a multi-purpose key once and branch inside, never in two sibling ifs."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

NVGT's `key_pressed()` is a one-shot: it returns true only on the first read of that key each frame and is consumed. So if two sibling `if (key_pressed(KEY_X))` checks both want to act on the same key in one frame, only the first sees it — the second silently never fires.

**`key_repeating()` consumes it too** (verified 2026-10-25 in the legacy engine source, `src/input.cpp` `KeyRepeating` — [[reference_legacy_nvgt_source]]): it returns `pressed || repeating` and clears BOTH `g_KeysPressed[key]` and `g_KeysRepeating[key]`. So a `key_repeating(KEY_X)` read earlier in the frame makes a later `key_pressed(KEY_X)` return false. `key_down()` is not consumed. Call order therefore decides which handler wins; that's why Shift M / Shift C never double-fired (code #30 in [[project_bugs_code]]).

**Why it bites here:** it looks like two independent handlers for the same key, but the second is dead. This produces "the key sometimes does nothing" bugs that are hard to spot.

**How to apply:** Read a multi-purpose key **once** into a local (`bool x = key_pressed(KEY_X);`) and branch on the local, or nest the logic inside a single `if`. Never gate two separate actions on two separate `key_pressed()` calls for the same key.
