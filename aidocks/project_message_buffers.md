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

**Sound effects are gated by buffer mute (6.3).** A category's sound effects fire only when its buffer is unmuted — check `buffer_muted(name)` (in `buffer.nvgt`, returns false if the name is unknown) before `pool.play_stationary(...)`. Current gates: `ranks` (rank-up "rankach" sound + rank-reward coin sound via `play_rank_sound`), `events` (baker event sounds), `achievements` (unlock jingle), `combos` (start/tier/stop sounds). This replaced the old per-sound settings toggles (`ranksfx`/`coinsfx`/`eventsfx`/`achsfx`), which were removed from the game settings menu and save — so muting a buffer now silences both its messages and its sounds, and there's no separate sound setting. The buffer roster is 7 named buffers (achievements, combos, critical, events, general, misc, ranks) plus the auto-created `all` aggregate. Unmutable buffers: `all`, the reserved `alerts`, and `critical` (6.3) — `toggle_mute` rejects these and their state is skipped in the mute save/restore loops (`savefuncts.nvgt`), so an old save that had `critical` muted can't strand it muted. `critical` carries milestone rank rewards and locked-minigame notices, which should always come through.

**Mute states persist in the user settings file (6.4).** `buffer_muted_<name>` keys live in `usersets.crz` — saved by `writepreffs3()`, restored by `readpreffs3()` (called at startup after `create_buffer`); `toggle_mute()` auto-saves via `writepreffs3()`. They were moved out of `gamesets.crz` because muting is a personal/live preference, not a game-mechanic setting. Consequence: the **user-settings reset** clears them (it deletes `usersets.crz` and restarts → all buffers load unmuted), while the **game-settings reset** no longer touches them. See [[project_save_data_layout]].
