Welcome to Cookie Craze!
In this game, you bake and sell cookies to earn money, climb the ranks, and build the ultimate automated bakery.

Start out clicking manually, then invest your money into upgrades and unlock minigames like blackjack, the cookie flipper, the slot machine, and the cookie lottery.
Automate your production until cookies are practically baking themselves.
Every rank you reach brings a reward, and milestones along the way unlock powerful new features.

Can you reach the highest rank and become the ultimate baker? Let's find out!

A note on currency.
Throughout the game, money is always displayed in dollars and cents. For example, 50 cents or $1.50.
You will never see the word coins in any player-facing message. However, coins is the internal name used inside the configuration files to refer to the player's money.
If you are editing config files, you must use the word coins exactly as written when the documentation says so. This does not affect how money appears in the game itself.

Game features

Baking and selling cookies.
Bake cookies and sell them to earn money. The more you bake, the higher your rank, and the more you earn.

You can bake manually by pressing the bake button, or let auto-baking handle it passively. Every cookie baked counts toward your rank progress. Your earnings per bake scale with your rank, so the further you progress, the faster money accumulates.

To sell cookies and earn money, enter the cookie store and choose the sell option. You will be prompted to type how many cookies you want to sell, with the maximum amount already filled in. Press enter to sell everything you have, or type a smaller number to sell only part of your supply.

Each cookie sells for a configurable rate set in the game settings, defaulting to 50 cents. The more you bake before selling, the larger your payout.

Baking mode is a toggle that activates automatic cookie production. To enable it, you need at least one auto cookie. Once active, cookies are baked automatically on a timed cycle without any input needed.

You can still press the bake button manually while baking mode is running to get an extra boost on top of the automatic output. Baking mode turns off automatically if your auto cookie count drops to zero.

If you have no baking slots yet, auto baking uses your full auto cookie value directly.
Once you purchase and enable slots through the baking slots manager, production is routed through them instead, giving you finer control over how cookies are distributed.

Ranking up.
Reach new ranks by baking cookies. Every rank rewards you with money, and milestone ranks unlock new features.

The money reward scales with your current rank, so higher ranks pay out more.
Certain milestone ranks also award bonus stat boosts on top of the money reward, such as extra auto cookies or manual cookies.

All rewards and unlock ranks are fully configurable in ranks.table.

Regular rank rewards are announced non-interruptively in the background and stored in the ranks buffer. Milestone rank rewards go to the critical buffer.
When baking mode is off, they show as a dismissible dialog so you don't miss them. When baking mode is on, they are delivered non-interruptively so they don't interrupt automated play.

The bundle shop.
Buy packages of multiple stat upgrades at once, often at better value than buying the same items individually.

Bundles are organized into categories by rank, spanning the full progression of the game from beginner to godlike tiers.
Each bundle lists what stats it contains and how much it costs.
Each category also announces which currency it uses before you enter it, so you always know whether to spend coins or cookies.
Like the singles shop, locked categories and bundles show their required rank by default. You can hide them entirely by disabling the show locked items option in the game settings.

The beginner and advanced categories use coins. All higher categories from expert onward use cookies instead.

One important thing to understand about bundle pricing: bundles do not have their own fixed prices.
Instead, the game uses the singles shop as a backend price engine, looking up the current price of each item based on how many you have already bought and adding them up.
This means bundle prices scale up naturally as you progress, just like singles do.

The advantage of bundles is that they package multiple items into one convenient purchase, so you get more total stats per transaction than buying the same items one at a time.

There are four ways to purchase in the bundle shop.

The first is buying a bundle at a custom quantity. Select any bundle and you will be prompted for how many you want to buy. Type the exact number you want and confirm to buy precisely that many at the current price.

The second is buying a bundle at the maximum quantity. When the purchase prompt appears, the maximum number you can currently afford is already filled in. Simply press enter without typing anything and the game buys as many as your budget allows in one transaction.

The third is buying one of every affordable bundle at a custom quantity. At the bottom of each category is a buy one of every affordable bundle option, which appears whenever at least one bundle in the category is within your budget. Selecting it prompts you for how many of each you want to buy, and the game purchases that many of every affordable bundle in the category at once, skipping any you cannot afford.

The fourth is buying one of every affordable bundle at the maximum quantity. When the prompt appears, the maximum number of times you can afford to buy the full set is already filled in. Simply press enter and the game buys as many of each affordable bundle as your budget allows.

Both shops support bulk buying, but they work differently. The singles shop lets you buy as many of each individual stat as you can afford, one stat at a time. Bundles package a curated mix of stats into a single purchase, so you can upgrade multiple stats at once in one transaction. The cost works out the same either way, since both use the singles shop prices as the foundation.

The singles shop.
Buy individual stat upgrades using your money. Three stats are available: auto cookies, manual cookies, and baking speed.

Auto cookies increases how many cookies are baked automatically per cycle.
Manual cookies increases how many cookies you produce per bake press.
Baking speed reduces the time between each bake.

Upgrades are organized into categories, with higher categories requiring a minimum rank to access.
Costs scale up the more you buy, so plan your purchases carefully.
Each category announces which currency it uses before you enter it, so you always know whether to spend coins or cookies.
By default, locked items and categories are shown in the shop with their required rank displayed. You can hide them entirely by disabling the show locked items option in the game settings.

The automatic baking and manual baking categories use coins. The user created items category uses cookies instead.

There are four ways to purchase in the singles shop.

The first is buying a single item at a custom quantity. Select any item in a category and you will be prompted to enter how many you want. Type the exact number you want and confirm to buy precisely that amount at the current price.

The second is buying a single item at the maximum quantity. When the purchase prompt appears, the maximum number you can currently afford is already filled in. Simply press enter without typing anything and the game buys as many of that item as your budget allows in one transaction.

The third is buying all affordable items at a custom quantity. At the bottom of each category is a buy all affordable items option, which appears whenever at least one item in the category is within your budget. Selecting it prompts you for a quantity, and the game buys that many of every affordable item in the category at once, skipping any you cannot afford.

The fourth is buying all affordable items at the maximum quantity. The prompt pre-fills the maximum number you can afford across all eligible items in the category. Pressing enter buys every affordable item to the maximum your budget allows, making it the fastest way to spend across an entire category in one go.

The ticket shop.
Buy scratch tickets using your money. Tickets are sold in tiers, with higher tiers costing more but offering larger prizes and better odds at the top end. Each tier draws from its own prize pool defined in lottery.table, with prizes weighted so smaller wins are more common.
By default, locked ticket tiers are shown with their required rank displayed. You can hide them entirely by disabling the show locked items option in the game settings.

Random events.
While you play, the game fires random events that can affect your stats in unexpected ways.

Events can give or take auto cookies, manual cookies, baking speed, and more.
Some are percentage-based, and others are flat amounts.
Events fire automatically during gameplay and are fully configurable in baker.event.

Blackjack. Unlocked at rank 10.
Beginner. It's the first unlock and introduces a simple card game with straightforward rules. Low stakes, easy to grasp.
A card game where you bet an item of your choice and try to reach 21 without going over.

You can bet money, cookies, auto cookies, manual cookies, or baking speed. When betting money, you enter the amount as a dollar value, for example type 1 to bet $1.00 or 0.50 to bet 50 cents. All other items are entered as whole numbers.
A natural 21 on your opening two cards pays out at 1.5 times your bet. A standard win pays double, and a tie returns your original bet. The dealer stands at 17 by default.

The deal button appears first in the tab order, followed by hit and stand.
You can draw cards manually, or enable automatic drawing in the settings.
All payouts, sounds, messages, and bet limits are configurable in jacks.table.
A configurable confirmation prompt can be set to appear when your bet reaches a certain threshold, protecting you from accidentally placing a large bet.

Cookie flipper. Unlocked at rank 20.
Beginner. Simple yes/no mechanic with a coin flip, very little strategy involved.
Flip a cookie or a penny to trigger a random event that can boost or reduce your stats.

When you open the flipper, the first thing you see is a type selector. Choose cookie to flip a cookie, or penny to flip a penny. Flipping is free either way — you get your cookie or penny back once it lands. The flip button updates its label to match whichever type is currently selected. A cookie lands on chocolate or biscuit, while a penny lands on heads or tails.

Each flip draws from the flipper.event configuration file, which works the same way as the main event system but with its own separate event list.
Some events are positive, and others are negative, so there is an element of risk.

Alongside the type selector is a checkbox list of all available events.
You must have at least one positive and one negative event checked before you can flip.
This lets you curate your own risk pool, opting into only the events you are comfortable with, or leaving everything checked to get the full range of outcomes.

You can flip manually, or enable automatic flipping in the settings so the result appears after a short random delay. When disabled, the result is held until you press enter or space.

A Flipper History box appears below the flip button and keeps a running log of your last 50 flips.
Each entry shows the flip number, which side it landed on, the event that was selected, and whether the outcome was positive or negative.
The history persists across sessions and resets when starting a new game.

Cookie lottery. Unlocked at rank 30.
Intermediate. Involves scratch tickets and prize tiers, requires understanding odds and managing ticket spending.
Scratch your tickets and reveal prizes ranging from money and cookies to stat boosts. You can scratch tickets one at a time or all at once from the lottery screen. When scratching one at a time, you can enable automatic reveal in the settings so the result appears after a short random delay. When disabled, the result is held until you press enter or space. When scratching all at once, prizes are distributed by weight across the full batch and applied instantly, so the results are statistically equivalent to scratching each ticket individually but resolve without any delay regardless of how many tickets you have.

Dice roller. Unlocked at rank 40.
Intermediate. More variables than blackjack with dice types, modifiers, and payout tiers to learn.
Roll a set of dice against a target score you set yourself and bet an item of your choice on the outcome.

Before rolling, choose an item to bet, then select your dice type, how many dice to roll, and a modifier to add or subtract from the total. Next, enter your bet amount and a target score. When betting money, you can enter the amount as a dollar value, for example type 1 to bet $1.00 or 0.50 to bet 50 cents. All other items are entered as whole numbers. Hitting or beating the target wins a payout scaled to how far you exceeded it. Falling short loses the bet.

The higher you set the target relative to what your dice can realistically roll, the greater the potential payout. If the target you enter is mathematically impossible to reach with your current dice type, count, and modifier, the game will block the roll and tell you the maximum possible score so you can adjust your settings. Keep in mind that negative modifiers can push the maximum possible roll below zero, making most targets unreachable, so use large negative modifiers with care. A configurable confirmation prompt can be set to appear when your bet reaches a certain threshold. The dice types, modifier options, payout tiers, sounds, and bet limits are all configurable in dice.table.

You can roll manually, or enable automatic rolling in the settings so the result appears after a short random delay. When disabled, the result is held until you press enter or space.

Slot machine. Unlocked at rank 50.
Intermediate. Multiple reels, payout combinations, and bet management make it more complex than early minigames.
Spin the reels and match symbols to win multiples of your bet.

Like the other minigames, you choose which item to bet and how much. When betting money, you enter the amount as a dollar value, for example type 1 to bet $1.00 or 0.50 to bet 50 cents. All other items are entered as whole numbers.
Payouts depend on how many reels match and which symbols line up, with higher matches paying out larger multiples. The symbols, payout multipliers, reel count, sounds, and bet limits are all configurable in slots.table.

A configurable confirmation prompt can be set to appear when your bet reaches a certain threshold, protecting you from accidentally placing a large bet.

Rank ups and achievement unlocks are checked and can fire while you are playing any of the minigames mentioned above.

Baking slots manager. Unlocked at rank 60.
Advanced. Requires understanding the entire baking system deeply enough to configure and automate it effectively.
Manage and configure your baking slots to balance automated and manual cookie production.

There are two types of slots.
Auto slots bake cookies passively without any input, scaling with your auto cookie stat.
Manual slots multiply the output of each bake press, scaling with your manual cookie stat.

You can purchase additional slots of either type, and toggle individual auto slots on or off to fine-tune how much of your production is automated versus manual.
Both submenus are locked behind a rank requirement, defaulting to rank 60.
The slot manager menu itself is always accessible so you can see what is coming, but you cannot enter either submenu until you reach the required rank.

Combos. Unlocked at rank 70.
Advanced. Demands consistent timing, significant progression in manual upgrades, and understanding of multiplier stacking to use effectively. Build a combo by pressing the bake button multiple times in quick succession. The combo does not activate immediately — you must reach a minimum number of consecutive presses within the time window before it kicks in. Once activated, a sound plays and a message announces that the combo has started. From that point, each press within the window increments your combo count and applies a multiplier to your manual cookie output. Reaching a combo tier plays a sound and announces the new multiplier. Missing the window at any point breaks your combo, plays a break sound, and resets everything back to zero.

The combo multiplier applies to both your base manual cookies and your manual slots, so higher slot counts make each combo tier even more rewarding.

All combo settings are fully configurable in combos.table, including the activation threshold, time window range, tier thresholds, multipliers, sounds, and messages. The combo system can also be disabled entirely from that file.

Baker info.
View a live snapshot of your current baker state. Press the Baker Info button in the main game interface to open it directly.

The baker info screen is divided into three sections.

Production.

Auto cookies: how many cookies your bakery produces automatically per bake cycle.
Manual cookies: how many cookies you produce per bake button press.
Baking speed reduction: the total number of milliseconds shaved off your bake interval through upgrades.
Bake interval: the actual time in milliseconds between each automatic bake cycle after your speed reduction is applied.

Slots.

Auto slots: the number of auto baking slots you currently own.
Manual slots: the number of manual baking slots you currently own.

Progress.

Current rank: your current rank number.
Current cookies: how many cookies you have right now.
Cookies needed for next rank: how many cookies are required to reach the next rank.
Current balance: how much money you currently have.
Cookie sell price: the current price per cookie as set in the game settings.
Earnings per sell: how much money you would earn if you sold your entire current cookie stockpile right now. Updates live as your cookie count changes.
Prestige level: how many times you have prestiged. Starts at 0 on a fresh save.
Prestige reward condition: describes what you need to complete to receive a reward on your next prestige, based on the require_all setting in quests.table.

Mode 1 means no reward is given, regardless of how many quests are complete.
Mode 2 means at least one quest must be complete to receive a reward.
Mode 3 means all quests must be complete to receive a reward, and shows the total count.

Quests completed: how many of your active quests are currently complete.

Statistics menu.
Access the baker statistics screen and achievement statistics screen from the Statistics button in the main game interface.

Baker statistics.
View a summary of everything you have done in your current playthrough. Open it from the Statistics menu.

Most stats shown here are tracked stats, meaning they are running totals that only ever go up and are saved with your game. A few are live stats, meaning they are calculated fresh from the current game state each time you open the screen and can go up or down. Live stats are noted individually where they appear.

The statistics screen is divided into ten sections.

Baking.

Cookies baked: counts the total number of cookies you have ever produced across all sources. This is a lifetime running total and never decreases when you sell cookies. Your cookie stockpile shown in the main game reflects only what you currently hold, while this stat reflects everything you have ever baked. Achievements that track cookies baked use this lifetime total, so they can unlock well before your stockpile reaches the same number.

Auto bakes performed: counts every time the auto baker fires a cycle.
Manual bakes performed: counts every time you press the bake button manually.

Baking slots manager.

Auto slots purchased: counts every auto baking slot you have ever bought, regardless of whether it is enabled or idle.
Auto slots enabled: counts every time an auto slot has been automated, whether individually or through the automate all option.
Auto slots idle: shows how many auto slots you currently own but have not enabled.

Unlike the other stats, this one is not saved separately because it can go up and down at any time.
It is always calculated fresh from the current state of your bakery, similar to how a stock price reflects the current value rather than a running total.

Manual slots purchased: counts every manual baking slot you have ever bought.

Combos.

Combos started: counts every time a combo activated after reaching the required number of consecutive presses.
Combos broken: counts every time an active combo expired because the press window was missed.
Highest combo reached: the highest consecutive press count you have ever achieved in a single combo. This is a lifetime personal best and only ever goes up.

Economy.

Money earned: counts the total amount of money you have received across all sources, including selling cookies and rank rewards.
Bundle upgrades purchased: counts every bundle shop transaction.
Single upgrades purchased: counts every singles shop transaction, whether you buy one item or a full bulk purchase.

Baker events.

Baker events fired: counts every baker event that successfully triggered and applied its effect during baking.

Flipper events.

Flipper events fired: counts every flipper event that successfully triggered and applied its effect during a cookie flip.

Cookie flipper.

Flipper flips: counts the total number of flips across both types.
Cookie flips: counts how many times you have flipped a cookie specifically.
Penny flips: counts how many times you have flipped a penny specifically.

Dice roller.

Total rolls: counts every dice roll.
Wins: counts rolls where the total met or exceeded the target score.
Losses: counts rolls where the total fell short of the target score.

Blackjack.

Hands played: counts every round where a bet was placed and cards were dealt.
Wins: counts rounds you won, including naturals, hitting 21, and dealer busts.
Losses: counts rounds where you busted, or the dealer beat you.
Pushes: counts rounds that ended in a tie, where your original bet was returned.

Slot machine.

Total spins: counts every spin of the reels.
Wins: counts spins where the payout multiplier was greater than zero, meaning you got at least something back.
Losses: counts spins where the payout multiplier was zero, meaning you lost your bet entirely.

Lottery.

Tickets bought: counts every ticket purchased across all tiers.
Tickets scratched: counts every ticket you have scratched, regardless of outcome.
Wins: counts scratches where a prize was awarded.
Losses: counts scratches that returned nothing.

Quests.

Quests completed: counts the total number of quests you have completed across all prestige cycles.
Rerolls performed: counts every time you have rerolled a quest.

All stats are saved with your game data and persist between sessions.

Achievement statistics.
View your progress toward every achievement grouped by category. Open it from the Statistics menu.

Each category lists complete achievements first showing your current stat value, followed by incomplete ones showing your current value against the required threshold.
Keep in mind that achievement thresholds compare against lifetime tracked stats, not your current stockpile. For example, a cookies baked achievement unlocks based on how many cookies you have ever produced, not how many you currently hold. Selling cookies does not reduce your progress toward baking achievements.
This screen is read only and does not require any input to navigate.

Prestige history.
View a log of every prestige run you have completed in your current save. It appears as a read-only input box in the quests screen, after the prestige button.

Each entry shows the run number, the rank you reached before prestiging, and what reward you received.
The history is empty on a fresh save and grows by one entry each time you prestige.
It resets only when starting a new game in the same slot.

Achievements.
Track your progress and earn recognition for milestones across every part of the game.

There are many achievements spread across all tracked statistics, including baking, baking slots manager, economy, upgrades, bundles, events, blackjack, the cookie flipper, the cookie lottery, the dice roller, the slot machine, quests, and combos.
Each achievement has a name, a description, and a hint that tells you what you need to do to unlock it.

Achievements are shown in a dedicated menu accessible from the main game interface. The menu is organised into categories covering baking, economy, events, minigames, and more.
Each category label shows how many achievements it contains. The main menu shows how many you have unlocked out of the total across all categories.

Opening a category shows how many you have unlocked out of the total for that category, with unlocked ones listed first followed by locked ones.

Pressing enter on an unlocked achievement shows a dialog with its description that you can read at your own pace.
Pressing enter on a locked achievement gives you a cryptic hint about what you need to do to earn it.

When you earn an achievement during play, it is stored in the achievements buffer.
If baking mode is off, it shows as a dismissible dialog.
If baking mode is on, it is announced non-interruptively so automated play is not interrupted.

Quests.
Complete a set of objectives to unlock the prestige option and start a new run with a permanent bonus.

Quests are automatically assigned at the start of each prestige cycle using a difficulty-based system. Each quest has a difficulty from 1 to 10, and active slots are spread evenly across the range so you always get a balanced mix.
Required quests occupy their difficulty slot directly, and random quests fill the rest. Only one required quest per stat can be active at a time, so if a stat has multiple required tiers only the current one will appear.

The number of active quests is configurable in quests.table and is capped at 10.
The game ships with a variety of quests spanning both required rank milestones and random objectives across many trackable stats.
Rerolling a quest replaces only the currently focused quest with a new one of the same difficulty, leaving the rest of your active quests untouched.

To view your quests, press the Quests button in the main game interface.
A Quest Category list at the top of the quests screen lets you narrow down the list. The options are All, Required, and Random, and the list updates immediately as you switch between them.
The quests screen shows required quests at the top, followed by random quests sorted from easiest to hardest. Completed quests are labelled with complete so you can see your status at a glance.
Arrow to any quest and the detail box updates automatically, showing the description and current progress. A complete label appears before the progress values when the quest is done, for example complete, 5 of 5.

The prestige button is located in the quests screen and its label updates dynamically to reflect your current status.
A prestige history box also appears in the quests screen after the prestige button, showing a log of every prestige run.
A reroll history box appears directly after the reroll button, showing the last 50 rerolls and listing which quest was replaced, what replaced it, and how much it cost. It is hidden when a required quest is focused, always visible when the category is set to Random, and toggles as you move between quests when set to All.
Both boxes show a message when empty and reset when starting a new game.
Before reaching the minimum rank the prestige button shows unlocked at rank X. Once unlocked, it shows no reward available or reward available depending on your quest completion status.

If you want a different quest, arrow to it and press the reroll button. Rerolling replaces only that quest with a new random one of the same difficulty. The reroll button does not appear when a required quest is focused.
Rerolling deducts a cost from a configurable stat, and the cost increases each time using compounding scaling. The reroll count resets at the start of each prestige cycle so costs go back to base.
The reroll_warning setting in quests.table controls how the reroll button behaves when a quest is complete. See the configuration file reference for details.

Prestige.
Reset your progress and earn a permanent bonus that carries into every future run.

The prestige option is available from the quests screen.
Once you reach the minimum rank, pressing the prestige button opens a confirmation dialog explaining what resets and what carries over, and whether you will receive a reward based on your current quest completion status.

When you prestige, the following are reset: your money, cookies, rank, all upgrade purchases, baking slots, and baking and economy stats.
The following are kept: your prestige level, all achievements and achievement progress, minigame unlocks, minigame stats, and preferences.

Whether you receive a reward depends on the require_all setting in quests.table. If you have not met the quest requirement, nothing is gained.
Certain prestige levels award a milestone reward, and all other levels fall back to a default reward if one is configured. After the prestige message you will always receive a second notification telling you what you gained.
Once all prestige dialogs are dismissed, a summary screen appears showing the prestige level, the rank you achieved, quests completed, what reward you received, and how many prestige points you earned. All prestige settings and milestone rewards are fully configurable in prestige.table.

Prestige store.
Spend prestige points on permanent upgrades that carry into every future run.

The prestige store becomes available after your first prestige. A prestige store button appears in the quests screen directly below the prestige button once you have at least one completed prestige run.

Prestige points are earned each time you prestige. The number of points awarded is equal to the points_per_prestige setting in prestige.table multiplied by the number of quests you completed before prestiging. If you prestige without completing any quests, you earn no points for that run.

To check how many prestige points you have, open the prestige store. The store announces your current balance when it opens.

The prestige store is divided into two categories.

Passive Bonuses contains upgrades that permanently boost your cookie production or reduce the cookies needed to rank up, applied across every run from the moment you buy them.

Head Start contains upgrades that give you bonus resources at the beginning of each new run, such as starting coins, auto cookies, or manual cookies.

How prestige upgrades work.
Unlike the bundle, single, and ticket shops, the prestige store uses a one-time purchase system. Every upgrade in the store can only ever be bought once. Once purchased, the upgrade is immediately active and stays active permanently across every future run, including after further prestiges. You will never need to buy it again, and you cannot buy it a second time even if you wanted to.

Passive bonus upgrades work as multipliers layered on top of your existing stats. For example, buying a cookie multiplier upgrade does not add to your auto cookie or manual cookie counts directly. Instead, every time a bake fires, the output is multiplied by the bonus percentage. So if you normally produce 100 cookies per bake and you have a 5% cookie multiplier, you produce 105 instead. The higher your stats grow through the normal shop, the more noticeable the multiplier becomes. Similarly, a coin multiplier does not change your cookie sell price. It multiplies the total payout after the price is applied, so larger sell batches benefit more.

Head start upgrades work differently. They add a flat amount directly to your stats at the very start of each new run, before you have bought anything from the normal shop. For example, starting with 5 auto cookies means your run begins as if you had already purchased 5 auto cookies, giving you a head start on production without having to earn them from scratch. Head start upgrades do not affect your current run. They take effect the next time you start a new run after prestiging, so you will not notice them until your following playthrough.

Passive bonus upgrades take effect immediately after purchase and apply to your current run without needing to prestige again.

Since each upgrade can only be bought once, the store shrinks over time as you work through it. Once you have purchased everything available at your current prestige level, the store has nothing left to offer until you reach a higher prestige level that unlocks new items. If you have bought every item across all prestige levels, the store is permanently empty and points accumulate with nowhere to go. This makes the prestige store most valuable in the early and middle stages of your prestige journey, where the bonuses meaningfully reduce the grind of each new run.

Each upgrade in the prestige store can only be purchased once. Once bought, it is removed from the shop permanently. If all upgrades in a category are purchased, entering that category shows a message instead of an empty menu. If all upgrades available at your current prestige level are purchased, opening the store returns you to the quests screen with a message reminding you that more upgrades unlock at higher prestige levels. Locked items and categories follow the same show locked items setting as the other shops. Items locked behind a higher prestige level show the required level when show locked items is enabled, or are hidden entirely when it is disabled.

All prestige store upgrades, costs, point requirements, and effects are fully configurable in prestige.store.

Save slots.

The game supports multiple save slots for separate playthroughs, selected from the main menu. Each slot is completely independent, with its own rank, cookies, money, upgrades, and settings.
You can start a new game in any slot at any time without affecting your other saves.

Buffers.

Buffers are categorized message logs that keep track of everything that happens during your game.
Instead of important messages disappearing after being spoken, they are stored in a buffer so you can review them at any time.

There are eight buffer categories.

All is a special aggregate buffer that receives a copy of every message from every other buffer. It gives you a single place to review all game activity in the order it occurred. It cannot be muted.
Achievements holds messages for every achievement you earn during play.
Combos holds messages from the manual baking combo system, including combo starts, tier milestones, and breaks.
Critical holds important notifications like rank ups and game milestones.
Events holds messages from random events and flipper flips.
General holds status updates and informational messages.
Misc holds things like save confirmations and other game actions.
Ranks holds regular rank up announcements.

All of the named buffers, except for All, can be muted independently so they stop being spoken aloud while still logging messages for later review. Mute states are saved automatically the moment you toggle them and restored the next time you load your game, so you never need to re-mute buffers after restarting.
You can also export all of the buffer contents to log files at any time. Log files are saved to the logs folder inside the game's AppData directory.

Keyboard commands

In game.

Letter, F: Announces how many cookies you produce per bake when baking mode is active.
Letter, J: Announces how many cookies you produce per bake when baking mode is inactive.
Letter, C: Announces your current cookie count.
Letter, R: Announces your current rank and how many cookies are needed to reach the next rank.
Letter, M: Announces how much money you currently have.
Control S: Saves your game progress.
Control L: Reloads all configuration files and your save data without restarting the game. Useful when editing config files.
Escape: Opens a prompt asking whether you want to quit.
Alt plus F4: Exits the main game immediately without saving.

Buffers.

Comma: Moves to the previous item in the focused buffer.
Period: Moves to the next item in the focused buffer.
Shift plus Comma: Jumps to the top of the focused buffer.
Shift plus Period: Jumps to the bottom of the focused buffer.
Left Bracket: Navigates to the previous buffer category. Announces the category name, item count, and current item.
Right Bracket: Navigates to the next buffer category. Announces the category name, item count, and current item.
Shift plus Left Bracket: Jumps to the first buffer category. Announces the category name, item count, and current item.
Shift plus Right Bracket: Jumps to the last buffer category. Announces the category name, item count, and current item.
Shift plus C: Copies the current buffer message to the clipboard.
Shift plus M: Toggles mute or unmute on the focused buffer.
Shift plus Backslash: Exports all buffer items to log files in the logs folder.

Forms and menus.

Tab: Moves focus forward through form controls.
Shift plus Tab: Moves focus backward through form controls.
Spacebar or Enter: Activates the focused button or opens a menu.

Configuration files for modders

A note on the word coins in config files.
Inside the configuration files, the word coins is the internal identifier for the player's money. When a field says coins, that is the exact value you must type.
It does not mean the game will display the word coins to the player. All money values are shown in dollars and cents in-game, such as 50 cents or $1.50.
The word coins only appears in the config files themselves as a technical label, never in any message the player reads or hears.

All of the configuration files are located in the data/config folder, and are split into three subfolders.
Lines starting with a semicolon, hash, or double slash are treated as comments and ignored by the parser.

Six of the files, ranks.table, slots.table, prestige.table, quests.table, lottery.table, and combos.table, use section headers in square brackets such as [sounds], [default], [rewards], [settings], [quests], and [prize:id]. These are not cosmetic.
The parser uses them to know which format to expect. Do not remove or rename these headers, or the parser will not be able to read the file correctly.
Each functional header has a warning comment placed directly below it inside the file as a reminder. That comment is cosmetic and can be removed, but the header itself must stay exactly as written.

Six of the remaining files, baker.event, flipper.event, jacks.table, singles.store, tickets.store, and prestige.store, do not use functional section headers. Every line in those files follows the same format throughout.
They do have commented section headers starting with a semicolon for readability, but those are purely cosmetic and can be removed or changed freely.

achievements.table uses a category alias system at the top of the file, the same way singles.store defines shop menus. Category aliases are defined as alias=Full Category Name lines before any achievement entries.
Each achievement line then begins with its category alias as the first field. The categories drive the achievements menu and achievement statistics screen dynamically, so the order and names are entirely up to you.

bundles.store uses one functional section header, [bundles], which marks where the bundle definitions begin.
Aliases defined above it are always read regardless of position, but bundle lines are only read after the header appears.

baker.event

Location: data/config/events/baker.event

Defines random events that fire during normal gameplay.

Format: event_name:sound:baking_type:polarity:target:min_amount:max_amount:use_percent:chance:description

event_name
The internal name of this event. Used for identification only. Can be anything you like.

sound
The sound file or files to play when this event fires, relative to sounds/misc/. Do not include the folder path.

You can use a random range by writing the number part as (min,max). Example: burn(1,3).ogg picks randomly from burn1, burn2, or burn3.
You can also play multiple sounds by separating them with commas. Example: lose.ogg,burn(1,3).ogg

The alert sound, alert_big for positive events and alert_small for negative events, is always played automatically.
Set to none to play no additional sound.

baking_type
Controls which baking mode can trigger this event.

1 = auto baking only. The message is spoken non-interruptively in the background.
2 = manual baking only. The message appears as a dialog the player must dismiss.
3 = both modes. Delivery method depends on which mode triggered it.

polarity
Declares whether this event is good or bad for the player.

1 = positive. The event gives the player something or helps them.
2 = negative. The event takes something away or hurts them.

target
The stat this event affects when it fires.

cookies = the player's current cookie count.
coins = the player's current coin count.
autocookie = the player's auto cookie production rate.
manulcookie = the player's manual cookie production rate.
cookiespeed = the player's baking speed. Positive events reduce the interval, and negative events increase it.

min_amount and max_amount
The range of values the effect can roll between. A random value between min and max is chosen each time.

Rank operators can be appended to scale the amount with the player's current rank.

*rank multiplies the amount by rank. Effect grows stronger as the player progresses.
/rank divides the amount by rank. Effect grows weaker as the player progresses.
+rank adds rank to the amount. Gentle linear growth.
-rank subtracts rank from the amount. Effect diminishes as the player progresses.

Examples: 40*rank, 10/rank, 5+rank, 100-rank

use_percent
Controls whether the rolled amount is treated as a flat value or a percentage of the player's current stat.

false = the rolled amount is applied directly. A roll of 60 gives or takes exactly 60.
true = the rolled amount is treated as a percentage of what the player currently has.

A roll of 20 with use_percent true takes 20 percent of the player's current cookies.

chance
The probability of this event firing when it is selected, from 1 to 100.

100 means it always fires when chosen, and 50 means it fires roughly half the time.
Set to 0 to effectively disable this event without removing it from the file.

Note that this value works alongside the global event settings in the game settings, not instead of them. There are two master enable checkboxes, one for auto baking events and one for manual baking events. If either is disabled, no events fire for that baking mode at all regardless of this value. There are also two global chance sliders that apply on top of the per-event chance, so setting this to 100 does not guarantee the event fires if the global slider reduces the overall frequency.

description
The message spoken to the player when this event fires.

Use %amount% as a placeholder. It is replaced with the rolled value.

For flat events, %amount% shows the exact flat amount gained or lost.
For percentage events, %amount% shows the rolled percentage, for example 10.

Use %actual% as an optional placeholder to show the real computed stat change.

For percentage events, %actual% shows the true amount gained or lost after applying the percentage.

For example, if you have $2.00 and lose 10 percent, %amount% shows 10 and %actual% shows 20 cents.
For flat events, %actual% and %amount% show the same value, so only %amount% is needed.

flipper.event

Location: data/config/events/flipper.event

Defines events triggered by the cookie flipper minigame. Uses the same format as baker.event, except there is no baking_type field since flipper events are always shown as dialogs.
All fields work exactly the same as baker.event. Refer to the baker.event section above for full field descriptions.

Format: event_name:sound:polarity:target:min_amount:max_amount:use_percent:chance:description

bundles.store

Location: data/config/stores/bundles.store

Defines the bundle shop categories and bundles.

Category aliases.
Format: alias=Full Category Name|min_rank|hidden|currency|Description

Defined at the top of the file, before the [bundles] section header. Works the same as singles.store menu aliases, including the hidden flag. See the singles.store hidden field description for full details.

currency
Optional. Set to coins to spend the player's money when purchasing bundles in this category, or cookies to spend the player's cookie stockpile instead. Defaults to coins if omitted. The category label in the shop announces which currency it uses.

Bundle format: menu_alias:name:min_rank:hidden:item_type:quantity|item_type:quantity|...:description

menu_alias
The alias of the category this bundle belongs to, as defined in the aliases section.

name
The display name shown in the bundle shop menu. Must be unique across all bundles.

min_rank
The minimum rank required to purchase this bundle. Use 0 for no requirement.

hidden
Required. Set to true to completely hide this bundle from the shop until the player reaches the minimum rank. Set to false to show it as locked with the required rank displayed.
Works the same as the hidden field in singles.store.

items
A pipe separated list of item_type:quantity pairs. Each item_type must match an item_type from singles.store exactly.

The cost of each item in the bundle is calculated dynamically from its current shop price, so the bundle price scales naturally with your progression.

description
A short description of what this bundle contains, shown to the player in the bundle shop menu.

singles.store

Location: data/config/stores/singles.store

Defines the singles shop categories and upgrade items.

Menu aliases.

Format: alias=Full Menu Name
Format with all fields: alias=Full Menu Name|min_rank|hidden|currency|Description

Define short aliases for menu names at the top of the file. Use the alias as the first field on item lines to assign items to that category.
Append a pipe followed by a minimum rank, another pipe followed by the hidden flag, another pipe followed by the currency, and another pipe followed by a description.

currency
Optional. Set to coins to spend the player's money when purchasing items in this category, or cookies to spend the player's cookie stockpile instead. Defaults to coins if omitted. The category label in the shop announces which currency it uses.

hidden
Required on menu lines. Set to true to completely hide this category from the shop until the player reaches the minimum rank. The category will not appear at all, not even as a locked entry.
Set to false to show it as locked with the required rank displayed, which is the default behavior.
This is useful for secret or surprise categories you do not want players to know exist until they are ready to access them.

Item format: menu:item_type:target:base_cost:cost_multiplier:amount:min_rank:use_percent:hidden:description

menu
The alias of the menu this item belongs to, as defined in the aliases section.

item_type
The display name of this item as it appears in the shop menu.

target
The stat this item increases when purchased.

1 = auto_cookie. Increases the player's automatic cookie production rate.
2 = manual_cookie. Increases how many cookies the player bakes per manual click.
3 = cookie_speed. Increases the player's baking speed by reducing the auto bake interval.

base_cost
The starting price of this item before any purchases have been made. Displayed as currency in game.

cost_multiplier
How much the cost increases with each purchase. Uses compounding geometric scaling, meaning each purchase multiplies the previous price rather than adding a fixed amount to the original.

1.25 means each purchase costs 25 percent more than the last, and 1.0 means the price never changes.

Unlike a flat multiplier, a compounding multiplier grows on top of itself every time. The difference becomes dramatic very quickly.

A flat multiplier of 1.25 on a base cost of 100 always charges $1.25 no matter how many times you buy.
A compounding multiplier of 1.25 starts at $1.00 but reaches around $9.30 by purchase 10, and nearly $50,000,000 by purchase 100.

This means even values that look small are dangerous at a higher scale. A multiplier of 1.05 feels gentle at first, but after 300 purchases the price will have grown into the trillions of dollars.
Players who progress far into the game will buy certain items hundreds of times, so a multiplier that seems fine at purchase 20 will eventually make the item completely unaffordable.

This also affects bundle prices directly. Bundles calculate their cost by summing the current singles shop prices of all their items.
If any item in a bundle has an inflated price due to a high multiplier, that inflation carries over into every bundle containing it.

It is strongly recommended to keep this value at or below 1.02 for any item players are expected to buy many times.
Values above 1.05 should only be used for items with a very low purchase cap or items intentionally meant to become unaffordable after a small number of purchases.

amount
How much of the target stat is gained per purchase. For flat items this is a fixed number. For percentage items this is the percentage value.

For cookie_speed flat items, this is the number of milliseconds the bake interval is reduced by.
Use %item_count in the description as a placeholder and it will be replaced with this value.

min_rank
The minimum rank the player must reach before this item can be purchased. 0 means the item is available from the start.

Locked items still appear in the shop showing their required rank and description.

use_percent
Controls whether the amount is applied as a flat value or a percentage of the player's current stat.

false = the amount is added directly. An amount of 10 gives exactly 10 auto cookies.
true = the amount is treated as a percentage. An amount of 5 gives 5 percent of the player's current auto cookies.

hidden
Required. Set to true to completely hide this item from the shop until the player reaches the minimum rank. The item will not appear at all, not even as a locked entry.
Set to false to show it as locked with the required rank displayed, which is the default behavior.
This is useful for secret items you do not want players to know exist until they are eligible to purchase them.

description
The text shown when the player hovers over this item in the shop. Use %item_count as a placeholder for the amount value.

tickets.store

Location: data/config/stores/tickets.store

Defines the global lottery settings, ticket categories, and individual ticket tiers available in the lottery shop.

Settings.

max_tickets
The maximum number of tickets of each tier the player can hold at once. Set to 0 for no limit.

Category format: id=Display Name|rank|hidden|description

id
The internal identifier for this category, referenced by ticket entries below.

Display Name
Shown as the category heading in the buy tickets menu.

rank
The minimum rank required to see this category. Set to 0 for no requirement.

hidden
Set to true to hide the category entirely until the rank is reached. Set to false to show it as locked with the required rank displayed.

description
Shown when the player highlights the category in the shop.

Ticket format: category:id:cost:multiplier:rank:hidden:prize_id:description

category
The id of the category this ticket belongs to, as defined in the category section above.

id
The internal identifier for this ticket tier. Must be unique across all ticket entries.

cost
The base cost in currency for the first ticket purchased.

multiplier
How much the cost scales with each ticket purchased. Uses the same compounding scaling as the singles shop. Set to 1.0 for a flat price.

rank
The minimum rank required to buy this ticket. Set to 0 for no requirement.

hidden
Set to true to hide this ticket until the rank is reached. Set to false to show it as locked.

prize_id
The prize pool id from lottery.table that is used when a ticket of this tier is scratched. Must match a [prize:id] section header in lottery.table exactly.

description
Shown when the player highlights this ticket in the buy menu.

achievements.table

Location: data/config/tables/achievements.table

Defines all achievements in the game and the categories they are grouped under.

Category aliases.

Format: alias=Full Category Name

Define short aliases for category names at the top of the file, before any achievement lines. Use the alias as the first field on achievement lines to assign them to that category.
The categories appear in the achievements menu and achievement statistics screen in the order they are defined here.
Removing or renaming a category here removes it from both screens automatically.
You cannot hide a category directly. Instead, the game hides a category automatically when every achievement inside it has its hidden flag set to true and none of them have been unlocked yet. Once any achievement in the category is unlocked, the category becomes visible.

Achievement format: category:id:stat:threshold:silent:hidden:name:description:hint

category
The alias of the category this achievement belongs to, as defined in the aliases section above.

id
The internal identifier for this achievement. Used by the parser only. Must be unique across all entries.

Use lowercase letters and underscores, no spaces. Example: first_spin

stat
The statistic this achievement tracks. Must be one of the following values.

cookies_baked = total number of cookies baked, both manual and automatic.
auto_bakes_performed = total times the auto baker has completed a cycle.
manual_bakes_performed = total times the bake button has been pressed manually.
coins_earned = total money received from all sources.
coins_spent = total money spent on shop purchases and quest rerolls.

auto_slots_purchased = total auto baking slots ever bought.
auto_slots_enabled = total auto slots that have been automated, individually or via automate all.
auto_slots_idle = current number of auto slots owned but not enabled. This is a live value, not a running total, so it can go up and down.
manual_slots_purchased = total manual baking slots ever bought.

bundles_purchased = total bundle shop transactions.
singles_purchased = total singles shop transactions.

baker_events_fired = total baker events that successfully applied their effect during baking.
flipper_events_fired = total flipper events that successfully applied their effect during a cookie flip.
flipper_flips = total cookie flipper flips across both types.
cookie_flips = total cookie flips only.
penny_flips = total penny flips only.

dice_rolls = total dice roller rolls.
dice_wins = total dice rolls that met or exceeded the target.
dice_losses = total dice rolls that fell short of the target.

slot_spins = total slot machine spins.
slot_wins = total slot machine spins that returned a payout.
slot_losses = total slot machine spins that returned nothing.

blackjack_hands = total blackjack rounds played.
blackjack_wins = total blackjack rounds won.
blackjack_losses = total blackjack rounds lost.
blackjack_pushes = total blackjack rounds that ended in a tie.

quests_completed = total quests completed across all prestige cycles.
rerolls_performed = total times a quest has been rerolled.

threshold
The value the stat must reach to unlock this achievement.

name
The display name shown to the player in the achievements menu and spoken when unlocked. This can be anything you like and does not need to match the id.

Example: One Armed Baker

description
The message spoken to the player when they press enter on an unlocked achievement.
Use %threshold% as a placeholder and it will be replaced with the threshold value at display time.
This means if you change the threshold, the description updates automatically.

hint
The message spoken to the player when they press enter on a locked achievement. Supports the same %threshold% placeholder as the description field.

Use this to tell the player what they need to do to unlock it.

silent
Required. Set to true to suppress the buffer message and sound when this achievement is triggered. Set to false to allow the normal notification behavior.
This overrides the global achievement sound setting for that specific achievement. Recommended for achievements that track stats which closely mirror another, to avoid redundant notifications.
Silent achievements are still tracked and appear in the achievements menu and achievement statistics screen as normal. Only the notification is suppressed.

hidden
Required. Set to true to completely hide this achievement from the achievements menu and achievement statistics screen until it is unlocked. Set to false to show it as locked with a hint available, which is the default behavior.
Recommended for achievements tied to content locked behind a rank, such as minigames, so players are not shown entries for systems they have not encountered yet. Once unlocked, a hidden achievement appears in both screens like any other achievement.
Hidden achievements are also excluded from the total count until unlocked. For example, if there are 207 achievements but 128 are hidden and none unlocked, the menu will show 0 of 79, giving no indication that hidden achievements exist at all.

combos.table

Location: data/config/tables/combos.table

Defines all settings for the manual baking combo system.

Settings section.

enabled
Format: enabled=true or enabled=true:rank

Set to true to enable the combo system. Set to false to disable it entirely. You can optionally append a colon followed by a rank number, for example enabled=true:30, to make the system dormant until the player reaches that rank. Below the required rank, manual baking works as normal with no combo tracking. Once the rank is reached, the system activates fully. When no rank is specified, the system is active from the start.

combo_window
Format: combo_window=value or combo_window=min,max

The range in milliseconds the player has between consecutive bake presses, both during the silent build-up phase and while the combo is active. A random value between min and max is chosen after each press. As long as each individual gap between presses stays under that value, the combo continues building. If any single gap exceeds it, the count resets to zero and you must start over. Use a single value for a fixed window, for example combo_window=3000.

start_count
Format: start_count=value or start_count=min,max

The number of consecutive presses required before the combo activates. If a min and max are provided, a random value between them is chosen each time a new pre-combo phase begins. Until this threshold is reached, presses are tracked silently with no multiplier and no announcement. If the player pauses too long during this phase, the count resets and a new target is chosen. Once the threshold is reached, the combo activates immediately and the start sound and message fire.

start_sound
The sound to play when the combo activates after reaching the start_at threshold. Relative to sounds/. You can include a subfolder prefix, for example combos/start.ogg.

stop_sound
The sound to play when the combo timer expires and the combo breaks. Relative to sounds/. You can include a subfolder prefix, for example combos/stop.ogg.

start_message
The message spoken when the combo activates after reaching the start_at threshold.

stop_message
The message spoken when the combo breaks. Use %combo% as a placeholder for the count reached before breaking, and %hits% for the correctly pluralized word hit or hits.

Tiers section.
Format: count:multiplier:sound:message

Defines combo milestone tiers. Each tier fires once when the combo count first reaches its threshold.

count
The number of consecutive presses required to reach this tier.

multiplier
The output multiplier applied to manual cookie production when this tier is active. A value of 2.0 doubles your manual output. The multiplier applies to both base manual cookies and manual slots.

sound
The sound to play when this tier is first reached. Relative to sounds/.

message
The message spoken when this tier is first reached. Use %combo% as a placeholder for the current combo count and %multiplier% for the active multiplier.

dice.table

Location: data/config/tables/dice.table

Defines all dice roller settings including dice types, modifier options, payout tiers, bet limits, sounds, and outcome messages.

dice_types
A comma separated list of die sizes available in the dice type list, for example 2,4,6,8,12,20. Each value is the number of sides on that die. You can add or remove values freely.

max_dice_count
The maximum number of dice the player can roll at once. Controls how high the number of dice list goes. Set to 20 to allow up to 20 dice.

modifiers
A comma separated list of modifier values available in the modifier list, for example -10,-5,-3,-1,0,1,3,5,10. Negative values subtract from the total. Zero means no modifier.

min_bet
The minimum amount of any item a player must bet per roll. Set to 1 to allow any positive bet.

max_bet
The maximum amount of any item a player can bet per roll. Set to 0 to allow unlimited bets.

confirm_threshold
Format: confirm_threshold=amount:use_percent

Triggers a yes or no confirmation prompt when the bet meets or exceeds the threshold. Works the same way as in jacks.table and slots.table.

min_target
The lowest target score the player is allowed to enter. Prevents trivially easy targets.

max_target
The highest target score the player is allowed to enter. Set to 0 for no upper limit.

shake_sound
Sound to play when the dice are rolled. Relative to sounds/minigames/. Supports random range syntax.

land_sound
Sound to play when the dice land. Relative to sounds/minigames/. Supports random range syntax.

Payouts section.
Format: margin:multiplier:sound:message

Defines payout tiers based on how much the roll total exceeds the target.

margin
How much the roll total must exceed the target for this tier to apply. The highest matching tier is always used. Use -1 for the loss entry, which fires when the total falls short of the target.

multiplier
The payout multiplier applied to the bet. The bet amount is deducted before rolling, and the winnings replace it. Set to 0 for a loss with no return.

sound
The sound to play for this payout tier. Relative to sounds/minigames/.

message
The text spoken after the roll. Supports the following placeholders.

%roll% is replaced with the total rolled including the modifier.
%target% is replaced with the target score the player set.
%margin% is replaced with how much the roll exceeded or fell short of the target.
%amount% is replaced with the amount won.
%item% is replaced with the name of the item bet, for example cookies or auto cookies.

jacks.table

Location: data/config/tables/jacks.table

Defines all blackjack settings including payouts, bet limits, sounds, and outcome messages.

dealer_stand
The point value at which the dealer stops drawing cards. Standard casino rules use 17.

natural_multiplier
The payout multiplier for a natural blackjack, which is 21 on the first two cards. Standard casino rules use 1.5, meaning a $1.00 bet returns $2.50 total.

This is a flat multiplier applied once to the bet amount.
Setting this very high will make blackjack an extremely powerful way to multiply stats and can unbalance the game quickly if bets are large.

win_multiplier
The payout multiplier for a standard win. Standard rules use 2, meaning a $1.00 bet returns $2.00 total.

This is a flat multiplier.
Setting it below 1 means the player always loses value even on a win, and setting it very high will make winning hands disproportionately rewarding.

push_multiplier
The payout multiplier for a push or tie. Standard rules use 1, meaning the player gets their original bet back.

This is a flat multiplier.
Setting it above 1 rewards ties, and setting it to 0 means the player loses their bet on a tie.

min_bet
The minimum amount of any item a player must bet per round. If the player enters less, the bet is rejected. Set to 1 to allow any positive bet.

max_bet
The maximum amount of any item a player can bet per round. If the player enters more, the bet is clamped to this value. Set to 0 to allow unlimited bets.

confirm_threshold
Format: confirm_threshold=amount:use_percent

Triggers a yes or no confirmation prompt when the bet meets or exceeds the threshold.

Set use_percent to false to treat the amount as a flat value.
Set use_percent to true to treat the amount as a percentage of what the player currently holds.

For example, 25:true prompts when the bet is 25 percent or more of their current stat.

Set the amount to 0 to disable the prompt entirely.

win_sound
Sound to play when the player wins. Relative to sounds/misc/. You can include a subfolder prefix to use a different folder, for example blackjack/win.ogg.

Supports random range syntax, for example jackwin(1,4).ogg picks randomly from jackwin1 to jackwin4.

lose_sound
Sound to play when the player busts or loses to the dealer. Relative to sounds/misc/. Subfolder prefix syntax is supported.

push_sound
Sound to play when the round ends in a tie. Relative to sounds/misc/. Subfolder prefix syntax is supported.

player_draw_sound
Sound to play when the player draws a card. Relative to sounds/misc/. Subfolder prefix syntax is supported.

dealer_draw_sound
Sound to play when the dealer draws a card. Relative to sounds/misc/. Subfolder prefix syntax is supported.

Message placeholders.

%bet% is replaced with the amount the player bet.
%win% is replaced with the amount won.
%score% is replaced with the player's final score.
%dealer% is replaced with the dealer's final score.
%item% is replaced with the name of the item being bet, for example currency or auto cookies.

natural_message
Message spoken when the player hits a natural blackjack.

win_message
Message spoken when the player wins normally.

bust_message
Message spoken when the player busts by going over 21.

dealer_bust_message
Message spoken when the dealer busts.

lose_message
Message spoken when the dealer beats the player.

push_message
Message spoken on a tie.

ranks.table

Location: data/config/tables/ranks.table

Defines rank rewards, milestone unlocks, and the sounds that play when rewards are given.

Sounds section.
Format: min_amount:sound

Defines which sound plays based on the reward amount when a rank up occurs.

Order entries from lowest to highest. The entry with the highest min_amount that is still less than or equal to the reward amount is used. The last entry covers everything above its minimum.

sound is relative to sounds/misc/. You can include a subfolder prefix to use a different folder, for example store/coin1.ogg.

Default section.
Format: target:min_amount:max_amount:unlock:message

Defines the reward every rank receives automatically. Only one default line is allowed.

Rewards section.
Format: rank:target:min_amount:max_amount:unlock:message

Defines special milestone rewards for specific ranks.
Multiple lines with the same rank are all valid, and one is chosen at random when the player reaches that rank.
Milestone rewards fire in addition to the default reward.

target
The stat this reward affects.

cookies = the player's current cookie count.
coins = the player's current coin count.
autocookie = the player's auto cookie production rate.
manulcookie = the player's manual cookie production rate.
cookiespeed = the player's baking speed.

min_amount and max_amount
The range of values the reward can roll between. Supports the same rank operators as baker.event.

Setting both values to negative numbers causes the reward to deduct from the target stat instead of adding to it, making it a penalty. This is useful for difficulty modding where certain ranks should hurt rather than help.

unlock
An optional feature to unlock at this rank. Use none for no unlock.

blackjack = unlocks the blackjack minigame.
flipper = unlocks the cookie flipper minigame.
lottery = unlocks the cookie lottery and the ticket shop.
dice = unlocks the dice roller minigame.
slots = unlocks the slot machine minigame.
slotmanager = unlocks the baking slots manager. Also sets the rank gate for the baking slots manager menu automatically.

message
The text spoken to the player when this reward fires.

Use %rank% as a placeholder for the current rank number.
Use %amount% as a placeholder for the actual reward amount.
Use %name% as a placeholder for the player's name.

slots.table

Location: data/config/tables/slots.table

Defines the slot machine symbols, reel count, bet limits, and payout tiers.

symbols
Format: symbols=name, name, name, ...

A comma separated list of symbol names for the slot machine reels. You must have at least 10 symbols defined.

reels
Format: reels=number

The number of reels the slot machine spins. Must be at least 2.
Make sure no payout match value exceeds this number, otherwise that payout can never be triggered.

min_bet
The minimum amount of any item a player must bet per spin. If the player enters less, the bet is rejected. Set to 1 to allow any positive bet.

max_bet
The maximum amount of any item a player can bet per spin. If the player enters more, the bet is silently clamped to this value. Set to 0 to allow unlimited bets.

confirm_threshold
Format: confirm_threshold=amount:use_percent

Triggers a yes or no confirmation prompt when the bet meets or exceeds the threshold.

Set use_percent to false to treat the amount as a flat value.
Set use_percent to true to treat the amount as a percentage of what the player currently holds.

For example, 25:true prompts when the bet is 25 percent or more of their current stat.

Set the amount to 0 to disable the prompt entirely.

Payouts section.
Format: matches:multiplier:sound:message

matches
The number of matching symbols across the reels required to trigger this payout. Use 0 to define the loss outcome. This value should not exceed the number of reels.

multiplier
How much of the bet is returned to the player. 4 means the player wins 4 times their bet. 0.5 means the player wins half their bet, and 0 means the player loses their bet entirely.

This is a flat multiplier applied once to the bet amount.
Setting a payout multiplier very high for common match counts will make the slot machine trivially easy to exploit and can rapidly inflate the player's stats.

sound
The sound file to play when this outcome fires, relative to sounds/misc/. You can include a subfolder prefix to use a different folder, for example store/coin1.ogg.

message
The text spoken to the player when this outcome fires.

prestige.table

Location: data/config/tables/prestige.table

Defines the prestige system settings and milestone rewards.

Settings section.

min_rank
The minimum rank the player must reach before prestige becomes available.

points_per_prestige
The base number of prestige points awarded per quest completed when the player prestiges. The total points earned for a run is this value multiplied by the number of quests completed before prestiging. If the player completes no quests, they earn no points for that run.

sound
The sound file to play when the player prestiges. Relative to sounds/misc/.

message
The message spoken to the player when they prestige. Use %prestige% as a placeholder for the new prestige level.

Default reward section.
Format: target:min_amount:max_amount:message

Defines a fallback reward that fires for any prestige level that does not have a specific milestone entry in the rewards section. Only one default reward line is allowed.
If no default reward is defined, levels without a milestone reward show a nothing was gained message instead.

target
The stat to give. Uses the same stat names as the rewards section.

min_amount and max_amount
The minimum and maximum amounts to give. A random value between them is chosen each time.

Both support the same expression syntax as ranks.table, so values like 50*rank and 100*rank are valid and scale with the current prestige level.

message
The message spoken when the default reward fires. Use %amount% as a placeholder for the amount given and %level% for the current prestige level.

Rewards section.
Format: prestige_level:target:amount|target:amount:use_percent:message

Defines one time bonus rewards given to the player at the start of their new run when they reach a specific prestige level.
Multiple reward lines can exist for different prestige levels.
Each reward line can give multiple stats at once by separating them with a pipe character.

prestige_level
The prestige level that triggers this reward. Each reward only fires once, when the player first reaches that level.

target:amount|target:amount
One or more stat and amount pairs separated by a pipe. Each pair is a stat name followed by a colon and the amount to give.

You can chain as many pairs as you like on one line.

cookies = the player's current cookie count.
coins = the player's current coin count.
autocookie = the player's auto cookie production rate.
manulcookie = the player's manual cookie production rate.
cookiespeed = the player's baking speed.

use_percent
Controls whether the amounts are applied as flat values or percentages of what the player had when they prestiged. This applies to all items in the reward line.

false = amounts are given directly. An amount of 10 gives exactly 10 of the target stat.
true = amounts are treated as a percentage of the player's final stat value at the moment they prestiged.

An amount of 5 gives 5 percent of whatever they had.
Keep percentage values low, as even a small percentage of a late-game stat can be a significant head start.

message
The message spoken when this reward is given. Use %amount% as a placeholder and it will be replaced with a summary of all items given.

Use %level% as a placeholder and it will be replaced with the current prestige level number.

prestige.store

Location: data/config/stores/prestige.store

Defines the prestige store categories and upgrades purchasable with prestige points.

Menu aliases.

Format: alias=Full Menu Name|min_level|hidden|Description

Works the same as singles.store menu aliases, except min_level refers to the player's prestige level rather than their rank. Set min_level to 0 for a category available from the first prestige. Set hidden to true to hide the category entirely until the prestige level is reached, or false to show it as locked with the required level displayed.

Item format: menu:item_id:cost:min_level:hidden:amount:description

menu
The alias of the category this item belongs to.

item_id
The internal identifier for this upgrade. Must be unique across all entries. Also used as the display name in the shop, with underscores replaced by spaces.

The item_id also determines what effect the upgrade has. The game recognises the following prefixes.

cookie_multiplier = permanently increases all cookie production by a percentage each bake.
coin_multiplier = permanently increases all money earned from selling cookies by a percentage.
rank_discount = permanently reduces the cookies required to rank up by a percentage.
starting_coins = gives bonus coins at the start of each new run after prestige.
starting_autocookie = gives bonus auto cookies at the start of each new run after prestige.
starting_manualcookie = gives bonus manual cookies at the start of each new run after prestige.

cost
The number of prestige points required to purchase this upgrade.

min_level
The minimum prestige level required to see and purchase this item. Set to 0 for no requirement.

hidden
Set to true to hide this item until the prestige level is reached. Set to false to show it as locked with the required level displayed.

amount
The value applied when this upgrade is purchased. For cookie_multiplier and rank_discount this is a percentage. For starting stat upgrades this is a flat amount added at the start of each run.

description
The text shown when the player highlights this item in the prestige store.

quests.table

Location: data/config/tables/quests.table

Defines all available quests and the reroll system settings.

Settings section.

max_active
The maximum number of quests the player can have active at once. Required quests always fill first, and random ones fill the remaining slots.

require_all
Controls when the player receives a prestige reward. Prestige is always available once the minimum rank is met regardless of this setting.

1 = no reward is given, regardless of quest completion. Prestiging always shows nothing was gained.
2 = at least one quest must be complete to receive a reward. Prestiging without completing any quest shows nothing was gained.
3 = all quests must be complete to receive a reward. Prestiging without completing all quests shows nothing was gained.

Reroll settings.

reroll_target
The stat deducted when the player rerolls their random quests.

cookies = deducts from the player's current cookie count.
coins = deducts from the player's current coin count.
autocookie = deducts from the player's auto cookie production rate.
manulcookie = deducts from the player's manual cookie production rate.
cookiespeed = deducts from the player's baking speed.

reroll_base_cost
The base cost of the first reroll per prestige cycle.

reroll_multiplier
How much the reroll cost increases with each reroll. Uses compounding scaling, the same as the singles shop.

Keep this value low. Setting it too high will make rerolling unaffordable very quickly.

reroll_warning
Controls how the reroll button behaves when the focused quest is already complete. Accepts a number from 1 to 5.

1 = No confirmation on any reroll. The reroll fires immediately.
2 = Shows a confirmation prompt only when rerolling an incomplete quest.
3 = Shows a confirmation prompt only when rerolling a completed quest. This is the default.
4 = Shows a confirmation prompt before any reroll, complete or not.
5 = Hides the reroll button entirely when a completed quest is focused. Incomplete quests reroll without prompting.

reroll_sound
The sound file to play when the player rerolls. Relative to sounds/misc/.

reroll_message
The message spoken after a successful reroll. Use %cost% as a placeholder for the amount deducted.

Quests section.
Format: id:name:stat:threshold:use_percent:required:advance:difficulty:description

id
The internal identifier for this quest. Must be unique across all entries. Use lowercase letters and underscores, no spaces. Example: bake_million

name
The display name shown in the quest list in the quests menu.

stat
The statistic this quest tracks. Uses the same stat names as achievements.table. Must be one of the following values.

cookies_baked = total cookies baked, both manual and automatic.
coins_earned = total money received from all sources.
coins_spent = total money spent on shop purchases and quest rerolls.

auto_slots_purchased = total auto baking slots ever bought.
manual_slots_purchased = total manual baking slots ever bought.
auto_slots_enabled = total auto slots that have been automated.

bundles_purchased = total bundle shop transactions.
singles_purchased = total singles shop transactions.

baker_events_fired = total baker events that applied their effect during baking.
flipper_events_fired = total flipper events that applied their effect during a cookie flip.
flipper_flips = total cookie flipper flips across both types.
cookie_flips = total cookie flips only.
penny_flips = total penny flips only.

dice_rolls = total dice roller rolls.
dice_wins = total dice rolls that met or exceeded the target.
dice_losses = total dice rolls that fell short of the target.

slot_spins = total slot machine spins.
slot_wins = total slot machine wins.

blackjack_hands = total blackjack rounds played.
blackjack_wins = total blackjack rounds won.
blackjack_losses = total blackjack rounds lost.
blackjack_pushes = total blackjack rounds that ended in a tie.

threshold
The value the stat must reach to complete this quest.

use_percent
Controls how progress is reported in the detail input box when the player focuses this quest.

false = progress is shown as a raw value. For example, 342,500 of 1,000,000.
true = progress is shown as a percentage. For example, 3.62%.

Useful for quests with very large thresholds where a raw number may be hard to interpret.

required

true means this quest always appears every prestige cycle and occupies its difficulty slot, preventing a random quest of the same difficulty from filling that position.
false means it goes into the random pool.

advance

true means that once this quest is completed, it is permanently retired and replaced by the next tier of the same stat on the next prestige cycle. The player will never see the same quest again after beating it.
false means the quest repeats every prestige cycle regardless of whether it was completed before.

difficulty
A number from 1 to 10 controlling which slot this quest occupies in the active quest list.

It does not directly affect how hard the quest is to complete in practice, that is determined by the threshold.
Two quests can share the same difficulty number, and the one with the larger threshold will naturally take longer to finish.

The game spreads active slots evenly across the difficulty range found in the table, so easier quests always appear alongside harder ones.
Required quests occupy their difficulty slot directly.
The difficulty range is read dynamically from the table and capped at 10. The max_active setting is also capped at 10.

description
The message shown in the detail input box when the player focuses this quest. You do not need to include progress information in this field.

Below the description the game always appends a line automatically that reads current progress followed by either a raw value out of the threshold or a percentage.
If the quest is complete, a period and the word complete are appended on the same line.
If it is not complete, nothing extra is added.

The following placeholders are available and will be replaced at display time.

%threshold% = the target value the stat must reach.
%stat% = the name of the stat being tracked, in readable form.
%progress% = the player's current stat value, capped at the threshold.
%percent% = the player's current progress as a percentage of the threshold.

Keep the description focused on what the quest is asking, and let the game handle reporting the progress.

lottery.table

Location: data/config/tables/lottery.table

Defines all prize pools used when a ticket is scratched.

Prize pools.
Format: id:target:min_amount:max_amount:use_percent:weight:sound:message

Each prize pool is declared with a section header in the format [prize:id], where id matches the prize_id field on a ticket in tickets.store.
Every line under a pool header defines one possible prize the player can scratch.

id
The internal identifier for this prize. Must be unique across all entries. Used internally only.

target
The stat this prize affects when it is scratched.

cookies = the player's current cookie count.
coins = the player's current coin count.
autocookie = the player's auto cookie production rate.
manulcookie = the player's manual cookie production rate.
cookiespeed = the player's baking speed.
none = a losing ticket. No stat is changed.

At least one losing prize per pool is required.

min_amount and max_amount
The range of values the prize can award. A random value between them is chosen at scratch time.

use_percent
Controls whether the amount is treated as a flat value or a percentage of the player's current stat.

false = the rolled amount is applied directly.
true = the rolled amount is treated as a percentage of what the player currently has.

weight
The relative chance of this prize being selected within its pool. Higher values are more common.
Weights are relative to each other within the pool only. A weight of 200 is twice as likely as 100.

sound
The sound file to play when this prize is revealed, relative to sounds/misc/.
Supports random range syntax, for example ticklose(1,3).ogg picks randomly from ticklose1 to ticklose3.
Set to none to play no sound.

message
The message shown to the player when this prize is revealed.

Use %amount% as a placeholder for the awarded value. For coin prizes this is formatted as currency. For all others it is a plain number.
Use %item% as a placeholder for the correctly pluralized item name. For example, 1 auto cookie or 5 auto cookies. Not applicable to coin prizes.

Game conclusion

CookieCraze started as a simple cookie clicker and has grown into a full-featured idle game with automated production, minigames, achievements, a rank progression system, multiple save slots, and deep modding support.
Every system in the game, from rank rewards to slot payouts to event effects, can be tuned or extended through the provided configuration files.

Whether you are a player looking to understand the game better, or a modder building your own experience on top of it, we hope this document gives you everything you need to succeed in the baking industry.
Thanks for playing, and happy baking!
