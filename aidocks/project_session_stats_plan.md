---
name: project_session_stats_plan
description: "AGREED (7.2, 2026-10-27, not built): session vs lifetime statistics modelled on ToyMania's player-facing design — Statistics menu gains Session/Lifetime screens, copy to clipboard, export to file; exit results dialog with a settings toggle. Replaces the old session summary todo item. No clear option."
metadata:
  node_type: memory
  type: project
---

# Session and lifetime statistics (7.2)

Grew out of the todo item "Add a session summary shown when you exit…". The dev pointed at ToyMania (`user/ToyMania-main`) and liked all of its stats design.

## ToyMania reference (surveyed 2026-10-27)

- Paired globals per stat (`collected_toys` / `total_collected_toys`), both incremented live; playtime added only at run end. Session resets on new game / pause-menu quit; session values are saved.
- Main menu "view game statistics" → "Session Statistics", "Lifetime Statistics", "Clear game statistics.", "Copy all game statistics to clipboard.", "Export all game statistics to file". One stat per menu item ("You have collected 12 toys." / "…a total of 340 toys.").
- End-of-run results dialog "Here are your current results for this game session: …" then copy/export offer. Setting "Enable stat copy and export options."
- Its bugs to avoid: quit loses playtime; quit reset misses two stats; clearing "both" misses session playtime; session time read as "a total of".

## Decided so far (dev)

- **Keep the player-facing design**, adapted to CookieCraze. **No clear option at all** (dev, 2026-10-27) — only copy and export. (Lifetime stats drive achievements, quest baselines and prestige, so clearing would have been risky anyway.)
- **Storage: snapshot-and-diff, not paired variables** (proposed, matches how quests already count the current run): snapshot every stat when a save is loaded/started; session = current − snapshot. No stat-increment sites change. Plus a new playtime stat (session and lifetime).
- **Where things go (dev, revised 2026-10-27):** main game screen Statistics button (Alt+T) → `infomenu()` (menu.nvgt): Achievement statistics, **Session statistics** (new, same read-only text box + sections as today, first line "Time played this session: …"), **Lifetime statistics** (today's "Baker statistics" renamed, + "Time played"), Back. **No copy/export menu items** — instead each of the three screens (`show_achievement_statistics`, session, lifetime; all are a read-only multiline input box + `&Close`) gets **Copy and Export buttons before Close**, acting on that screen's text. Hotkeys: Close already has Alt+C, so use `Co&py` (Alt+P) and `&Export` (Alt+E). Tab order: text, Copy, Export, Close. Today's screen is `show_baker_statistics()` in extrafuncts.nvgt (a read-only multiline input box).
- **Exit results (dev, revised 2026-10-27):** after a "Yes, quit…" choice in the quit confirmation (game.nvgt), show a **form screen like the other statistics screens** — a read-only multiline input box with this session's results, then **Copy, Export, Close** buttons (same hotkeys/behaviour; Export writes stats/session_statistics.txt). Close or Escape continues the exit. Replaces the earlier dlg-dialog idea and the ToyMania-style copy/export menu (open question 3 resolved). Game-settings checkbox (taste — [[feedback_settings_vs_tables]]) "Enable showing session results upon exiting the game." after the combo repeat checkbox, on by default.

## Open questions

1. ~~Export location~~ — **decided (dev, 2026-10-27): ToyMania's way, three files.** A `stats` folder in AppData (`APPDATA_DIR + "stats/"`, created if missing); each screen's Export **replaces** its own file (open "w"): achievement statistics, session statistics, lifetime statistics. Proposed names matching the screen titles: `achievement_statistics.txt`, `session_statistics.txt`, `lifetime_statistics.txt` (names approved by the dev). Confirmation dialog names the file, e.g. "Session statistics have been exported to stats/session_statistics.txt."
2. ~~Copy/export show/hide setting~~ — **decided (dev, 2026-10-27): no setting.** Copy and Export buttons are always present.
3. ~~Exit copy/export~~ — resolved: the exit screen has its own Copy/Export/Close buttons.
4. ~~Exit content~~ — **decided (dev, 2026-10-27), like ToyMania's run results:** the same lines as the Session statistics screen (every section, zeros included), framed by an opening line such as "Here are your results for this session, %name%." at the top and a short sign-off at the end. (ToyMania: game.nvgt:1226 / door.nvgt:165 — "Here are your current results for this game session:" + Label: number lines + "Better luck next time!"/"Grate work!" [sic].)
5. ~~Todo wording~~ — **reworded (dev, 2026-10-27):** "Add separate session and lifetime statistics screens, give them and the achievement statistics screen copy and export buttons, and show a session results screen when you exit the game, which a game settings option can turn off."

All questions answered; plan AGREED 2026-10-27, not built.

## Build sections (proposed)

1. **Session tracking.** Snapshot every lifetime stat when a save is loaded or a new game starts (reuse the quest-baseline idea, but separate from quest baselines); a new playtime stat (lifetime, saved) plus the session timer. Session values = current − snapshot. Nothing shown yet.
2. **Statistics screens.** `infomenu()`: rename Baker statistics → Lifetime statistics (+ Time played), add Session statistics (same sections, first line Time played this session). Copy (`Co&py`) and Export (`&Export`) buttons before Close on all three screens; exports replace `stats/achievement_statistics.txt`, `stats/session_statistics.txt`, `stats/lifetime_statistics.txt` in AppData (folder created if missing); confirmation dialogs name the file.
3. **Exit results + setting.** After a "Yes, quit…" choice: the session screen text with an opening line and sign-off, Copy/Export/Close, Close/Escape continues the exit. Checkbox "Enable showing session results upon exiting the game." after the combo repeat checkbox, on by default, wired like exitsfx/comborepeats.
4. **Docs.** Readme (statistics section, new screens/buttons/files, the setting), changelog: **one or two 7.2 entries (dev, 2026-10-27)** since it's several features in one — proposed split: (1) session/lifetime statistics screens + copy/export buttons on all three screens, (2) the exit results screen + its setting; that takes the block to 9 of 10 (8 if merged), todo → Finished, memory.

## Draft changelog entries (dev liked them, 2026-10-27; adjust wording to what gets built)

1. Added separate session and lifetime statistics. The statistics menu now has a session statistics screen, which counts only what you have done since you loaded your game, alongside the lifetime statistics screen, formerly baker statistics, which keeps your totals across every session. Both now also show how long you have been playing. The session, lifetime, and achievement statistics screens each gain copy and export buttons before the close button: copy puts the whole screen on your clipboard, and export saves it to its own file in the stats folder inside the game's AppData directory, replacing the previous export.

2. Added a session results screen that appears when you quit the game. After you choose to quit, with or without saving, it shows everything you did during that session, the same as the session statistics screen, along with copy, export, and close buttons, and closing it finishes quitting the game. A new game settings option, enable showing session results upon exiting the game, turns it off. It sits just after the combo repeat option and is on by default.
