---
name: feedback_one_sentence_game_messages
description: "In-game spoken messages are at most THREE sentences (changed from one on 2026-09-30) — keep them as short as the message allows; some, like minigame errors, legitimately need more than one."
metadata:
  node_type: memory
  type: feedback
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

In-game feedback messages (things the game speaks to the player) are **at most three sentences**. Aim for one when one is enough; use the second and third only when the message genuinely needs them.

**Changed 2026-09-30 (dev):** the limit used to be exactly one sentence. The dev raised it to three because some messages need to be longer on purpose, for example the error messages in the minigames. Don't treat a two- or three-sentence message as a bug; only four or more is.

**The "Press enter or space to continue." hint does NOT count (dev, 2026-09-30).** `dlgmessage_return` appends it automatically (a deliberate 1.8 feature) to tell players how to proceed if they don't know; count only the message's own sentences. Never write the hint into a message yourself — `dlgmessage_return` adds it, and doing both makes it play twice.

**Why:** The game is played by ear; short messages are quick to hear and don't talk over the next action, but some errors need room to explain what went wrong.

**How to apply:** State the result first; add at most two more sentences only if they're needed. General guidance still belongs in the docks/readme rather than a runtime message.
