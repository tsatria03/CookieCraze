---
name: project_shop_pack_menus_plan
description: "PLANNED for 7.7 entries 8-10 (2026-10-30), all design questions answered, not built yet: overhaul the buying shops to use pack menus like the 7.7 sell cookies menu — per item, Buy 1 / 5 / 10 ... / Buy max with real prices, buying instantly — one store at a time. Expect several changelog entries and a big readme update. Version undetermined; wait for the dev. On the todo list as Unfinished (2026-10-30); the dev moved it to the TOP of the unfinished items, ahead of the prize wheel, offline progress and the daily bonus."
metadata:
  node_type: memory
  type: project
---

# Shop pack menus overhaul (idea, one store at a time)

**Origin (dev, 2026-10-30):** after the sell cookies menu became a pack menu in 7.7 (sell 1 / 5 / ... / 10,000, sell all before Back, real payout labels, instant, stays on the line; see [[project_release_status]]), the dev asked how hard the same machinery would be for the buying shops. "I'm considering if they should have something like this too, but one store at a time." Then: "Please record this shop overhaul plan. I have a feeling this may require several change log entries, plus a big readme update." **Not approved or scheduled; the dev decides when and which store first.**

## The idea

- Today every shop asks "how many?" in an input box after you choose an item, already filled with the most you can afford.
- Pack style: choosing an item opens a menu instead: Buy 1 for $X, Buy 5 for $X, ... (the seller uses 1, 5, 10, 25, 50, 100, 250, 500, 1,000, 2,500, 5,000, 10,000), then **Buy max** (with its count and price) before Back. Labels come from each shop's existing bulk cost code, so they include rising prices, discount boosts, the 1 cent floor, and caps. Choosing a line buys instantly, speaks the result, stays on that line (remembered like the seller's sell_last_id).
- Only the input-box question is replaced; each shop's purchase logic already handles any amount.
- Trade-off to raise when planning: one extra menu level per item. Option: a "Buy a custom amount" line among the packs that opens the old input box.

## Per-store difficulty (assessed 2026-10-30)

- **Boosts** (easy): simple rising price; max_boosts hold limit.
- **Scratch tickets** (easy): rising price; max_tickets limit.
- **Single upgrades** (medium): money or cookie prices by category, percent vs flat items, the baking speed cap (cap_speed_maxbuy), and the existing category-level buy all.
- **Bundles** (medium): price is the sum of several items' rising prices; speed inside bundles respects its cap; existing buy all.
- (Superseded by Q1/Q9: boosts, tickets, singles, bundles, then the prestige store's Endless upgrades.) Suggested order: boosts or tickets first to settle the pattern, then singles and bundles; after the first, a shared pack-menu helper so later shops plug in their price and buy functions.

## Existing buy-all options (keep them)

- Singles: "Buy all affordable items" at the end of a category's item list (menu.nvgt ~1421); asks "How many of each item would you like to buy?" prefilled with the max.
- Bundles: "Buy one of every affordable bundle" (~1869); despite the name asks "How many of each affordable bundle would you like to buy?".
- Boosts and tickets have none.
- Plan: keep them at the end before Back (the shops' version of sell all). Per shop, decide whether their input box also becomes a pack menu (Buy 1 of each ... Buy max of each, with total prices; consistent but long labels) or keeps the input box (simpler to type "how many of each").

## Docs expectations

Several changelog entries (likely one per store, maybe more), and a big readme update: every shop's buying description in the cookie store section, plus the getting started purchase instructions.

## Decisions (ask ONE at a time, record each)

- **Q1 store order (dev, 2026-10-30): boosts, then scratch tickets, then single upgrades, then bundles last.**
- **Q2 (dev, 2026-10-30): NO input boxes anywhere — menu only.** No "Buy a custom amount" line; packs and Buy max only, like the seller. (Implies the buy-all options' "how many of each" input boxes also become menus.)
- **Q3 (dev, 2026-10-30): pack sizes match the cookie seller** — 1, 5, 10, 25, 50, 100, 250, 500, 1,000, 2,500, 5,000, 10,000, then Buy max before Back.
- **Q4 (dev, 2026-10-30): option A, list every pack always** with its price, like the add a slot menus and the seller; choosing one you can't afford plays the error sound, shows an error such as "Error! You can't afford 1,000 Manual cookies!", buys nothing, and returns to the menu.
- **Q5 (dev, 2026-10-30): option A, spoken like the seller** (stay on the line). **Exceptions, like the seller's last-cookie sale:** Buy max, and any purchase after which you can no longer afford even 1 more of that item (e.g. you could only afford 1), show the message in a dialog (dlgmessage_return) and leave the pack menu. Errors use dialogs.
- **Q6 (dev, 2026-10-30): one level up** — after a Buy max or a can't-afford-another purchase, the dialog closes onto the item list (e.g. the singles category list), still on the item just bought, like the seller going up to the cookie store.
- **Q7 (dev, 2026-10-30): option A** — the buy-all options open their own pack menu worded "of each": Buy 1 of each for $X ... Buy 10,000 of each, then Buy max of each before Back; each label is the total for every affordable item in the category; same spoken / dialog / one-level-up rules. Bundles' "Buy one of every affordable bundle" is renamed **"Buy all affordable bundles"** (it buys more than one).
- **Q8 (dev, 2026-10-30): the pack menu intro gives the item's name and your balance in the currency it is priced in** — "Manual cookie menu. You have $83,960.00.", or cookies for cookie-priced categories ("Baking gloves menu. You have 2.4 thousand cookies."). Buy max line shows only its price ("Buy max for $X"), like sell all. No "you can buy N" in the intro (the item list one level up still says it); owned counts via the stat keys. Buy-all "of each" menus presumably the same pattern (category name + balance).
- **Q9 (dev, 2026-10-30): option A** — the prestige store's two Endless categories (Endless Passive, Endless Head Start; 12 upgrades, today an input box "How many would you like to buy?") get the same pack menu as a **fifth store after bundles**: intro with the upgrade name and your prestige points, packs priced in points, Buy max before Back, same spoken / dialog / one-level-up rules. **Standard Passive and Standard Head Start stay one-press purchases** (each tier is bought once, so there is nothing to choose an amount of).
- **Changelog plan (dev, 2026-10-30): 3 entries, finishing 7.7 at 10 of 10** — (1) boosts + scratch tickets together, (2) single upgrades + bundles together (incl. the "of each" buy-all menus and the bundles rename), (3) the prestige store's Endless upgrades. Readme updated per store as each section's docs. If a hidden bug does turn up, fold it into its store's entry or move it to 7.8.

## Build log

- **Section 1 (boosts) BUILT and PASSED 2026-10-30:** shop_pack_sizes() helper + boost_pack_menu(boostIdx) in menu.nvgt replacing the input box (intro "<Boost> menu. You have <balance>.", Buy 1..10,000 with get_boost_bulk_cost prices, "Buy max for X" or plain "Buy max", Back; spoken purchase stays on the line; buy max or can't-buy-another shows dlgmessage_return and returns to the boost list; unaffordable pack / over hold limit errors). **Entry rule added by the dev during testing, APPLY TO EVERY SHOP:** choosing an item refuses to open its pack menu when you cannot buy even one — balance under 1 gives "Error! You have no money/cookies to buy boosts with!", otherwise "Error! You can't afford another <name> boost! The next one costs <price>." (inventory full keeps its own error). Docs wait until tickets (shared entry).
- **Line wording (dev, 2026-10-30), ALL SHOPS:** pack lines read "Buy <n> <item name> for <price>" (singular for 1, e.g. "Buy 1 Silver Ticket for...", "Buy 5 Clearance rack boosts for..."); the max line reads "Buy all <item name, plural> for <price>" (no price when none affordable). Applied to boosts and tickets.
- **Section 2 (tickets) BUILT and PASSED 2026-10-30:** ticket_pack_menu(ticketIdx), entry rule (no money / can't afford another + next price), max_tickets errors. 4 parts passed (spoken buy, 10,000 error, buy all of 2.24 thousand Silver Tickets with dialog back to Standard Tickets, entry error with next price $9.48 billion). Boost shop comment moved back above boost_buy_menu. **Docs DONE: readme ticket shop + boost shop paragraphs describe the pack menus; 7.7 changelog entry 8 (boosts + tickets).** Next: section 3, single upgrades.
