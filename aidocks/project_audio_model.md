---
name: project_audio_model
description: "sound_pool + HRTF spatial audio and the cycrz/sounds/ folder layout; sounds are cwd-relative to cycrz/. No sound packs."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

CookieCraze has no visual output — everything is screen-reader speech plus audio through NVGT's `sound_pool` (HRTF where positional). `sound_pool` is vendored in `main/deps/` (it depends on `rotation.nvgt` for `pi`/`calculate_theta` — see [[project_include_tree]]).

**Sound assets live in `cycrz/sounds/`**, referenced by bare cwd-relative paths like `"sounds/store/coin1.ogg"` (cwd = `cycrz/`, see [[project_path_conventions]]). Top-level categories:
- `ambience/`, `menu/`, `misc/`, `dlg/` — background, navigation, one-shots, dialog cues
- `store/` — shop/purchase sounds (including the coin sounds)
- `combos/` — combo-chain feedback
- `events/` — random-event cues (baker + flipper events)
- `minigames/` — blackjack/flipper/lottery/dice/slots sounds
- `buffer/` — the message-buffer notification sounds (see [[project_message_buffers]])

**Gotcha:** the preload cache replays stale audio if you reuse a filename for changed bytes — [[project_nvgt_sound_preload_cache]]. There is **no swappable sound-pack system** in CookieCraze (unlike ToyMania). When a new sound is needed, wire the playback to the intended filename now; the dev adds the `.ogg` later (don't create dummy files). Note the shipped folder `sounds/cooky_lottery` is intentionally spelled that way (a known naming quirk).
