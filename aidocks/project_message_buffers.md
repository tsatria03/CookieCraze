---
name: project_message_buffers
description: "The categorized message-buffer system: several named buffers (achievements/combos/critical/events/general/…), each mutable and exportable, for non-interruptive announcements."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

CookieCraze routes game notifications through a **message-buffer system** rather than interrupting speech directly. `main()` creates several named buffers via `create_buffer(...)` — e.g. `achievements`, `combos`, `critical`, `events`, `general` (and more). The buffer backend is `main/deps/buffer.nvgt`; its notification sounds live in `cycrz/sounds/buffer/`.

**Why it exists:** during idle/auto-play a lot happens at once; buffering lets the player review categories at their own pace, mute noisy ones, and it keeps announcements from talking over each other. Buffers can be **muted per-category and exported** (exports land in the AppData `logs/` folder — [[project_save_data_layout]]).

**How to apply:** When adding a notification, push it to the appropriate existing buffer instead of a raw `speak()`. Adding a new category means a `create_buffer` call plus its sound(s) under `sounds/buffer/`. Keep the routing consistent with the existing categories.
