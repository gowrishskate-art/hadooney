# Hadooney — Empire Wars: Game Design Document

> **What is this file?** This is the blueprint for the entire game. Every feature, rule, number, and mechanic lives here. When you want to change how the game works, update this file first, then update the code.
>
> **Who reads this?** You (the developer) AND coding agents. Everything is written so both a 7th-grader and a computer can understand it.

---

## The Big Picture

**Hadooney** is a turn-based empire conquest game. You pick a historical or fictional era — each with a unique superpower — then defeat AI opponents on an open battlefield. Each faction is one army with cartoon anime warriors. Think Risk meets anime. Colorful warriors battle on a green battlefield, and you have a ticking clock.

**Platforms:** Browser (Chrome, Safari, Firefox). Works on Android phone + MacBook. Fully offline — no internet needed.

**How to open:** Double-click `index.html` in your file manager, or drag it into a browser tab. That's it.

### Why This Game Is Fun (Design Philosophy)

Every feature in this game serves one of these goals:

1. **Meaningful choices.** You never feel like you're just clicking randomly. Every decision — where to attack, when to recruit, when to use your superpower — matters.
2. **Escalating tension.** The game gets more exciting as it goes. Early turns are calm planning; late turns are desperate battles for survival.
3. **Fair losses.** When you lose, you know WHY. The game doesn't cheat — it plays by clear rules you can learn and exploit.
4. **One-more-try pull.** The star rating system makes you want to replay eras to get a perfect score. Different difficulties unlock different strategies.

---

## Game Flow (Start to Finish)

```
Title Screen
    |
    v
Era Selection (pick 1 of 20 eras)
    |
    v
Difficulty Selection (pick 1 of 6 levels)
    |
    v
Opponent Count (pick 1 to 20 AI enemies)
    |
    v
Game Play (conquer the map!)
    |
    v
Result Screen (stars + stats)
    |
    v
Play Again or Main Menu
```

There's also a **How to Play** button on the title screen that explains the rules, and a **Continue** button if you have a saved game.

### What Each Screen Shows

| Screen | What You See | What You Can Do |
|---|---|---|
| **Title** | Game logo, animated background, total play time | Play (new game), Continue (saved game), How to Play |
| **Era Select** | 20 cards in a grid (4 columns), each with era name, color, superpower name, stars earned | Tap/click an era card to select it, then Confirm |
| **Difficulty** | 6 cards, each with name, timer, and a short description of what changes | Tap/click to select, then Confirm |
| **Opponents** | A slider from 1 to 20 with a preview of how the map will look | Drag slider, tap Start Game |
| **Gameplay** | Open battlefield (full screen canvas), HUD bar at top, action buttons at bottom, scoreboard on side | Play the game (see Controls section) |
| **Result** | Victory/Defeat banner, star rating (animated), detailed stats | Play Again (same settings) or Main Menu |

**IMPLEMENTATION STATUS:** All screens exist and work. Timer display is in the HUD and counts down during player turns.

---

## The 20 Eras

Each era has a name, a color theme, and a one-time superpower that costs gold. You can only use your superpower ONCE per game, so timing is everything.

### Historical Eras

| # | Era | Color | Superpower | What It Does |
|---|---|---|---|---|
| 1 | Ancient Egypt | Gold | Sandstorm | Pick an enemy tile — kills 50% of their troops (round down) |
| 2 | Roman Empire | Red | Legion March | Pick two of your tiles — move troops from one to the other (leaves 1 behind) |
| 3 | Viking Age | Blue | Berserker Rage | Your next attack deals 2x damage |
| 4 | Medieval Europe | Gray | Castle Wall | Pick your tile — double its defense for 5 seconds (real-time) |
| 5 | Ottoman Empire | Teal | Janissary | Pick your tile — get 15 free troops instantly |
| 6 | Aztec Empire | Green | Blood Sacrifice | Gain 30 gold instantly (no targeting needed) |
| 7 | Ming Dynasty | Orange | Great Wall | Pick your tile — nobody can enter it for 5 seconds (real-time) |
| 8 | Edo Japan | Pink | Samurai Honor | Pick your tile — becomes invincible for 1.5 seconds (real-time) |
| 9 | British Empire | Navy | Naval Bombardment | Pick any tile on the map — remove 5 troops |
| 10 | Industrial Revolution | Brown | Factory | Pick your tile — it produces +5 gold per turn forever |

### Fictional Eras

| # | Era | Color | Superpower | What It Does |
|---|---|---|---|---|
| 11 | Cyber Dynasty | Cyan | Hack | Pick an enemy tile — steal 3 troops from them |
| 12 | Quantum Realm | Purple | Teleport | Pick two of your tiles — swap their troop counts |
| 13 | Star Empire | White | Orbital Strike | Pick any tile — destroy ALL troops on it |
| 14 | Dragon Dominion | Dark Red | Dragon Breath | Pick a target tile — deal 5 damage to it AND all adjacent tiles |
| 15 | Crystal Kingdom | Light Blue | Crystal Shield | All your tiles get +50% defense for 2 turns (no targeting needed) |
| 16 | Shadow Nexus | Dark Purple | Shadow Clone | Pick your tile — duplicate half its troops (round down) to an adjacent empty or owned tile |
| 17 | Thunder Legion | Yellow | Chain Lightning | Automatically attacks up to 3 random enemy tiles (no targeting) |
| 18 | Void Collective | Black | Void Rift | Pick an enemy tile — remove ALL troops and make it neutral |
| 19 | Ember Ascendancy | Orange-Red | Inferno | Pick a tile — it and all adjacent tiles catch fire (3 damage/turn, 3 turns) |
| 20 | Frost Dominion | Ice Blue | Blizzard | Pick a tile — freeze all enemy troops on adjacent tiles for 1 full turn (they can't attack or be attacked) |

### Superpower Rules

These rules apply to ALL superpowers:

1. **Cost:** Varies by difficulty (see Difficulty table). The cost is deducted when you activate the power.
2. **One use only.** Once used, the button is grayed out and says "Used."
3. **Can't use if you can't afford it.** The button shows the cost and is disabled if you don't have enough gold.
4. **Gold is deducted BEFORE the effect happens.** If you pick a target and cancel, you don't get the gold back — don't activate unless you're sure.
5. **Real-time effects (Castle Wall, Great Wall, Samurai Honor) use real seconds, not turns.** They keep ticking even during AI turns. This means you want to use them RIGHT BEFORE you attack, not early.
6. **Targeting mode:** Powers that say "pick a tile" enter targeting mode. The game shows instructions at the top telling you what to do. Tap a valid tile to fire, or tap Cancel to back out. Gold is NOT refunded if you cancel.
7. **Non-targeting powers (Aztec, Viking, Crystal, Thunder)** fire immediately when you press the button.

### Superpower Edge Cases

| Situation | What Happens |
|---|---|
| Sandstorm on a tile with 1 troop | 50% of 1 = 0.5, round down = 0. Troops become 0, tile becomes neutral |
| Legion March from a tile with 1 troop | Moves 0 troops (leaves 1 behind, has 0 to send). Power is wasted |
| Berserker Rage + attacker wins | Normal 2x damage. Surviving troops calculated normally from boosted attack |
| Berserker Rage + defender wins | You still lose 1 troop on your tile. The 2x bonus is consumed (wasted) |
| Castle Wall on a tile that's already boosted | Stacks multiplicatively: 2x becomes 4x defense. Probably overkill but allowed |
| Naval Bombardment on a tile with fewer than 5 troops | Removes ALL troops. Tile becomes neutral. Works on any tile (yours, enemy, neutral) |
| Naval Bombardment on a neutral tile with troops | Removes up to 5 troops. If 0 remain, tile stays neutral (empty) |
| Orbital Strike on your own tile | Allowed — destroys ALL your troops there. Tile becomes neutral. Don't do this by accident |
| Hack on a tile with fewer than 3 troops | Steals whatever they have (minimum 1). If they have 0, nothing happens and power is wasted |
| Shadow Clone with nowhere to place | If no adjacent tile is empty or owned by you, the power fails and gold is NOT refunded |
| Shadow Clone with 1 troop | Half of 1 = 0 (round down). 0 troops duplicated. Power is wasted |
| Chain Lightning with fewer than 3 enemies | Attacks however many enemy tiles exist (even just 1) |
| Void Rift on a tile with a factory | Troops removed, tile becomes neutral. Factory effect is LOST permanently |
| Inferno on a water tile | Water tiles can't catch fire. Skip them, only affect land tiles |
| Inferno + tile captured while on fire | New owner inherits the fire. Their troops take the damage |
| Frost Blizzard + frozen tile attacked by another player | Frozen tiles can't be attacked by anyone. The freeze protects the tile |
| Crystal Shield + Castle Wall on same tile | Crystal Shield is +50% defense. Castle Wall is 2x defense. They multiply: 1.5 x 2.0 = 3.0x defense |
| Using a power during AI turn | Not allowed. Powers can only be used during your turn |

**IMPLEMENTATION STATUS:**
- All 20 eras and superpowers exist in code.
- Gold deduction works for all powers including Aztec and Crystal Shield.
- Thunder Chain Lightning shuffles enemies randomly before selecting targets.
- Void Rift eliminates or reduces enemy troops as designed.
- Naval Bombardment (British) works on any enemy army.
- Real-time effects (Castle Wall, Samurai Honor, invincibility) use frame-based timed effects that pause when the pause menu is open.
- Gold is only deducted when the target is confirmed, not when entering targeting mode — so cancelling a power doesn't cost gold.

---

## Difficulty Levels

Each difficulty changes multiple things at once: how much time you have, how smart/rich/strong the AI is, and how expensive things are for you. The goal is that Easy feels relaxing and Impossible feels like a genuine achievement.

### Timer (Countdown)

If time runs out, you **automatically lose**. The timer counts down during YOUR turns only — it pauses during AI turns and when the pause menu is open.

The timer is shown in the HUD bar at the top of the screen. It uses the format **MM:SS**.

- **Green:** Normal (above 60 seconds)
- **Yellow + pulsing:** Warning (60 seconds or less)
- **Red + pulsing fast:** Danger (30 seconds or less)

### Difficulty Parameters

| Parameter | Easy | Medium | Hard | Crazy | Demon | Impossible |
|---|---|---|---|---|---|---|
| **Timer** | 10 min | 9 min | 7 min | 6 min | 5 min | 5 min |
| **AI income per tile** | 0.40g | 0.55g | 0.70g | 0.85g | 1.00g | 1.30g |
| **AI actions per turn** | 1-2 | 1-3 | 2-4 | 2-5 | 3-6 | 4-8 |
| **AI attack bonus** | +0% | +5% | +10% | +18% | +25% | +40% |
| **AI starting gold** | 10 | 15 | 20 | 25 | 30 | 40 |
| **AI starting troops/tile** | 2 | 2 | 3 | 3 | 4 | 5 |
| **Your income per tile** | 1.00x | 1.00x | 0.95x | 0.90x | 0.85x | 0.75x |
| **Recruit cost** | 10g | 10g | 12g | 14g | 16g | 20g |
| **Troops per recruit** | 5 | 5 | 5 | 5 | 4 | 4 |
| **Special power cost** | 20g | 20g | 22g | 24g | 28g | 30g |

### How Each Parameter Works

**AI income per tile:** Each turn, the AI earns `floor(number_of_tiles x income_per_tile)` gold. This is separate from your income — the AI has its own economy.

**AI actions per turn:** The AI takes a random number of actions in this range each turn. Each action is either attack, recruit, or reinforce (based on personality).

**AI attack bonus:** When the AI attacks, its attack power is multiplied by `(1 + bonus)`. So at Impossible, the AI's attacks are 1.4x stronger. This is applied AFTER the random roll.

**AI starting gold:** Gold each AI opponent begins the game with. Your starting gold is always 15 regardless of difficulty.

**AI starting troops/tile:** How many troops each AI starts with on their 3 tiles. Your starting troops are always 3-5 (random) per tile regardless of difficulty.

**Your income per tile:** Multiplier on YOUR gold income. At Hard, you earn 0.95 gold per tile instead of 1.0. The total is rounded down.

**Recruit cost:** How much gold YOU pay to recruit troops. The AI always pays 10g for 5 troops regardless of difficulty.

**Troops per recruit:** How many troops you get per recruit action. At Demon and Impossible, you get 4 instead of 5.

**Special power cost:** How much gold YOUR superpower costs. AI superpowers always cost 20g.

### Design Philosophy (Why These Numbers)

- **Easy** is for learning. You have lots of time, the AI is weak, and everything is cheap. You should win almost every time.
- **Medium** is the standard experience. Balanced challenge, comfortable pace. This is what most players will play first.
- **Hard** makes you think fast (7 min) and spend wisely. The AI is a real competitor. You can't just attack everything — you need to plan.
- **Crazy** is stressful — aggressive AI, tight clock. One bad decision can lose the game. For players who've beaten Hard a few times.
- **Demon** is for experienced players who know all the eras and powers. 5 minutes, strong AI, expensive recruiting.
- **Impossible** is the ultimate test. Same 5 minutes but the AI is overwhelming. Win rate should be ~5-15% for skilled players. It should feel like an achievement.

**What never changes between difficulties:**
- Terrain defense bonuses (Plains 1.0x, Forest 1.3x, Mountain 1.5x)
- Your attack power formula (troops x random 0.6-1.0)
- Special power effects (what each power does)
- Starting territory count (3 for everyone)
- Number of eras and superpowers

**IMPLEMENTATION STATUS:**
- Timer system fully implemented: HUD element with MM:SS countdown, pauses during AI turns and pause menu, color-coded warnings (yellow <=60s, red <=30s), auto-loss at 0.
- DIFFICULTY_PARAMS object with all 11 parameters per level is implemented and wired up.
- Recruit button and Power button show correct costs from DIFFICULTY_PARAMS.
- AI starting gold and troops use per-difficulty values from DIFFICULTY_PARAMS.
- Player income multiplied by playerIncomeMult.
- AI attack bonus applied in combat formula.

### Implementation Instructions for Difficulty System

Replace the current single `power` multiplier with a `DIFFICULTY_PARAMS` object. Each difficulty level should store all 11 parameters as a plain object:

```
DIFFICULTY_PARAMS = {
  easy:      { timer: 600, aiIncomePerTile: 0.40, aiActions: [1,2], aiAttackBonus: 0, aiStartGold: 10, aiStartTroops: 2, playerIncomeMult: 1.00, recruitCost: 10, troopsPerRecruit: 5, powerCost: 20 },
  medium:    { timer: 540, aiIncomePerTile: 0.55, aiActions: [1,3], aiAttackBonus: 0.05, aiStartGold: 15, aiStartTroops: 2, playerIncomeMult: 1.00, recruitCost: 10, troopsPerRecruit: 5, powerCost: 20 },
  hard:      { timer: 420, aiIncomePerTile: 0.70, aiActions: [2,4], aiAttackBonus: 0.10, aiStartGold: 20, aiStartTroops: 3, playerIncomeMult: 0.95, recruitCost: 12, troopsPerRecruit: 5, powerCost: 22 },
  crazy:     { timer: 360, aiIncomePerTile: 0.85, aiActions: [2,5], aiAttackBonus: 0.18, aiStartGold: 25, aiStartTroops: 3, playerIncomeMult: 0.90, recruitCost: 14, troopsPerRecruit: 5, powerCost: 24 },
  demon:     { timer: 300, aiIncomePerTile: 1.00, aiActions: [3,6], aiAttackBonus: 0.25, aiStartGold: 30, aiStartTroops: 4, playerIncomeMult: 0.85, recruitCost: 16, troopsPerRecruit: 4, powerCost: 28 },
  impossible:{ timer: 300, aiIncomePerTile: 1.30, aiActions: [4,8], aiAttackBonus: 0.40, aiStartGold: 40, aiStartTroops: 5, playerIncomeMult: 0.75, recruitCost: 20, troopsPerRecruit: 4, powerCost: 30 }
}
```

**Acceptance criteria:**
- [x] `DIFFICULTY_PARAMS` object exists with all 11 parameters per level
- [x] Timer HTML element exists in the HUD and shows MM:SS
- [x] Timer counts down by 1 every second during player turn only
- [x] Timer pauses when AI is taking its turn
- [x] Timer pauses when pause menu is open
- [x] Timer flashes yellow when <= 60 seconds
- [x] Timer flashes red when <= 30 seconds
- [x] Game ends in defeat ("Time's Up!") when timer reaches 0
- [x] AI income uses `floor(tiles x aiIncomePerTile)` instead of `floor(tiles x power)`
- [x] AI actions use random integer in `[min, max]` range from `aiActions`
- [x] AI attack bonus is applied as `(1 + aiAttackBonus)` multiplier on attack power
- [x] AI starting gold matches the table (not based on opponent count)
- [x] AI starting troops per tile matches the table
- [x] Player income is multiplied by `playerIncomeMult` and floored
- [x] Recruit button shows correct cost for current difficulty
- [x] Power button shows correct cost for current difficulty
- [x] Recruiting gives correct number of troops for current difficulty
- [x] Superpower deducts correct gold for current difficulty

---

## The Battlefield

### Open Battlefield (No Tiles)

The game uses an **open battlefield** — no hex grid, no tiles. Each faction is **one army** with a position (x, y) and a troop count. Armies are displayed as groups of cartoon anime characters on a green grass field.

- The battlefield fills the canvas (full screen)
- **Player army** starts on the left side
- **AI armies** spread in an arc on the right side
- The background is a dark green grass field with a subtle grid pattern
- Each army has a colored glow base and a label showing name + troop count

### Army Positions

- Player starts at 12% from the left, vertically centered
- AI opponents are placed in an arc formation on the right (60-90% from left)
- Positions scale to fit the screen size
- Army scale adapts so characters are always clearly visible

### How Armies Are Drawn

- Each army shows 1-5 anime characters depending on troop count (1 per 4 troops, max 5)
- Characters have idle bobbing animation
- Each era has unique outfit colors (armor, helmet, weapon)
- A colored oval shadow/base glows beneath each army group
- A label banner above shows: army name + troop count
- Selected army has a pulsing white highlight; enemies show red highlights

**IMPLEMENTATION STATUS:**
- Battlefield rendering with anime characters works correctly.
- No tiles, no hex grid — pure army-based system.
- `lightenColor()` and `darkenColor()` helpers for 3D shading.
- Characters scale based on canvas size and army count.

---

## Economy (Gold System)

### How You Earn Gold (Per Turn)

Your gold income at the start of each turn:

```
income = (number of territories you own x 1)     // base: 1 gold per tile
      + (number of forest territories x 1)       // forest bonus: +1 each
      + (number of factories x 5)                 // factory bonus: +5 each
income = floor(income x playerIncomeMultiplier)   // difficulty penalty at Hard+
```

**Example:** You own 12 tiles (3 of them are forests, 1 is a factory) on Hard difficulty:
```
base: 12 x 1 = 12
forests: 3 x 1 = 3
factory: 1 x 5 = 5
subtotal: 20
difficulty (Hard, 0.95x): floor(20 x 0.95) = floor(19) = 19 gold
```

### How You Spend Gold

| Action | Cost | What You Get |
|---|---|---|
| Recruit troops | 10-20g (varies by difficulty) | 4-5 troops added to a tile you own |
| Use superpower | 20-30g (varies by difficulty) | One-time era ability (see era table) |

### How AI Earns Gold (Per Turn)

```
aiIncome = floor(aiTiles x aiIncomePerTile)    // varies by difficulty
```

The AI does NOT get forest bonuses or factory bonuses. Its income is simpler.

### Gold Edge Cases

| Situation | What Happens |
|---|---|
| You have 0 gold | You can't recruit or use your superpower. Buttons are disabled. |
| You have 8 gold, recruit costs 10 | Can't recruit. Button shows cost in red. |
| Factory on a tile you lose | Factory is destroyed. The new owner does NOT get the factory. |
| Factory on a tile captured by Void Rift | Tile becomes neutral with 0 troops. Factory is destroyed. |
| Income calculation results in 0 | You earn 0 gold this turn. Unlikely but possible with 1 tile on Impossible. |

**IMPLEMENTATION STATUS:**
- Base income (1 gold/tile) and forest bonus (+1) work correctly.
- Factory bonus (+5/turn) works correctly.
- MISSING: Player income difficulty multiplier not applied.
- BUG: Recruit cost is hardcoded at 10g. Should vary by difficulty.
- BUG: Power cost is hardcoded at 20g. Should vary by difficulty.
- BUG: Troops per recruit is hardcoded at 5. Should be 4 at Demon/Impossible.

---

## Combat System

### How Battles Work

When you attack an adjacent enemy (or neutral) tile:

**Step 1 — Calculate attack power:**
```
attackPower = yourTroops x random(0.6 to 1.0) x berserkerBonus
```
- `berserkerBonus` = 2.0 if Viking Berserker Rage is active, otherwise 1.0
- After this step, berserker is consumed (set back to inactive)

**Step 2 — Calculate defense power:**
```
defensePower = theirTroops x random(0.5 to 0.9) x terrainDefense x defenseBonuses
```
- `terrainDefense` = 1.0 (Plains), 1.3 (Forest), or 1.5 (Mountain)
- `defenseBonuses` = Castle Wall (2.0x) x Crystal Shield (1.5x), if active on that tile

**Step 3 — AI attack bonus** (only when AI is attacking):
```
if attacker is AI: attackPower = attackPower x (1 + aiAttackBonus)
```

**Step 4 — Compare:**
```
if attackPower > defensePower:
    → Attacker wins (see outcomes below)
else:
    → Defender wins (see outcomes below)
```

### Outcomes

**Attacker wins** (attack > defense):
- You capture the tile. It becomes yours.
- Your surviving troops on that tile = `max(1, floor(yourTroops - theirTroops x 0.7))`
- The tile you attacked FROM drops to 1 troop (you left most of your forces behind)

**Defender wins** (attack <= defense):
- They keep the tile. You don't capture it.
- Their surviving troops = `max(1, floor(theirTroops - yourTroops x 0.5))`
- Your attacking tile drops to 1 troop

**Invincible tile** (Samurai Honor active):
- Attack is blocked entirely.
- You lose 1 troop from your attacking tile.
- Their troops are unchanged.

### Why These Numbers Feel Fair

- **Attackers have a slight edge** (0.6-1.0 vs 0.5-0.9) because attacking is risky — you leave your origin tile weak. The higher attack range rewards aggression.
- **The 0.7 and 0.5 multipliers** mean battles are bloody. Even when you win, you lose troops. This prevents snowballing (one player getting so strong nobody can stop them).
- **Terrain matters a LOT.** Mountains give +50% defense — that's like having 50% more troops for free. This makes map control strategic.

### Combat Edge Cases

| Situation | What Happens |
|---|---|
| Attacking a neutral tile with 0 troops | You capture it automatically. Your tile drops to 1 troop. No combat math needed. |
| Attacking a frozen tile | Not allowed. Show message "That tile is frozen!" |
| Your tile has 1 troop and you attack | Your attack power is tiny. You'll almost certainly lose, and your tile stays at 1 troop. Allowed but foolish. |
| Both sides have 1 troop | Pure luck. Attack = 0.6-1.0, Defense = 0.5-0.9. Either could win. |
| Berserker Rage + you lose the attack | Berserker is consumed (wasted). Your tile drops to 1 troop. |
| Attacking a tile on fire | Allowed. You attack normally. If you capture it, your troops inherit the fire. |
| Castle Wall expires mid-combat | Can't happen — combat is instant (no animation delay affects the math). |

### Reinforcing (Moving Troops Between Your Tiles)

Instead of attacking, you can move troops between two of your own tiles:

1. Select one of your tiles (must have 2+ troops)
2. Tap/click an adjacent tile that you also own
3. Troops move: `(yourTroops - 1)` move to the destination, origin keeps 1

This is useful for building up a strong tile before attacking, or saving troops from a weak tile.

**IMPLEMENTATION STATUS:**
- Core combat formula matches the design perfectly.
- Reinforce mechanic works correctly.
- AI attack bonus is applied in the attack formula.
- All edge cases handled correctly (frozen, invincible, berserker, fire).

---

## AI Opponents

### Personalities

Each AI gets one of four personalities, assigned in rotation (1st AI is Aggressive, 2nd is Defensive, 3rd is Balanced, 4th is Cunning, 5th is Aggressive again, etc.).

| Personality | Attack Chance | Recruit Chance | Reinforce Chance | Play Style |
|---|---|---|---|---|
| Aggressive | 70% | 20% | 10% | Attacks a lot, recruits sometimes. Dangerous early game. |
| Defensive | 30% | 50% | 20% | Builds up troops, attacks rarely. Becomes dangerous late game if left alone. |
| Balanced | 50% | 30% | 20% | Mix of everything. Unpredictable. |
| Cunning | 50% | 25% | 25% | Similar to Balanced but slightly trickier. |

### How the AI Picks Targets

The AI always attacks the **weakest adjacent tile** it doesn't own. If there's a tie, it picks randomly among the weakest.

**This is intentional.** The AI is predictable — it goes for easy targets. A smart player can bait the AI by leaving a weak tile exposed, then counter-attacking. This makes the game winnable through strategy, not just luck.

### AI Turn Flow

Each AI takes its turn in order (AI 1, then AI 2, etc.):

1. **Collect income:** `floor(tiles x aiIncomePerTile)` gold
2. **Pick action count:** Random number in `[minActions, maxActions]` from difficulty table
3. **For each action:**
   a. Roll a random number
   b. Compare to personality chances to decide: attack, recruit, or reinforce
   c. If attack: find weakest adjacent non-owned tile, attack it
   d. If recruit: pick a random owned tile, add 5 troops (costs 10g)
   e. If reinforce: move troops from a stronger tile to an adjacent weaker owned tile
4. **Check elimination:** If an AI has 0 tiles, it's eliminated

### AI Superpowers

AIs can use their superpower too! Here's how:

- **When:** The AI uses its superpower when it has enough gold AND the situation is right (varies by power type)
- **Cost:** Always 20g for the AI (not affected by difficulty power cost)
- **Targeting:** The AI picks the best target for its power type (weakest enemy tile, most valuable tile, etc.)
- **Frequency:** Once per game, same as the player

### AI Edge Cases

| Situation | What Happens |
|---|---|
| AI has 0 gold and tries to recruit | Skip that action, do nothing. |
| AI has no adjacent non-owned tiles to attack | Skip attack action. |
| AI has 1 tile left | Can still attack adjacent tiles and recruit. Desperate! |
| All AIs eliminated | Player wins! Victory screen shows. |
| Two AIs attack each other | Yes! AIs can attack each other. This creates natural alliances and rivalries. |
| AI can't find a valid power target | Doesn't use the power. Saves it for later. |

**IMPLEMENTATION STATUS:**
- All 4 personalities exist and match the design.
- AI target selection (weakest adjacent) matches.
- BUG: Personality chances are multiplied by difficulty `power`, distorting ratios. At Easy, a 70% aggressive AI only attacks 14% of the time. Should NOT scale personality chances with difficulty.
- MISSING: AI starting gold should use per-difficulty values, not a formula based on opponent count.
- MISSING: AI starting troops should use per-difficulty values, not always 2-3.
- MISSING: AI attack bonus not applied in combat.
- MISSING: AI superpowers not implemented — AIs never use their powers.

---

## Star Rating System

Stars are awarded when you **win** a game. They're saved per-era, and only your best result is kept. You can replay the same era on different difficulties to try for more stars.

| Stars | Territory % Needed | What It Feels Like |
|---|---|---|
| 1 star | 20% or more | "I survived!" — you won but barely. |
| 2 stars | 50% or more | "Half the map is mine!" — solid victory. |
| 3 stars | 75% or more | "I'm dominating!" — impressive performance. |
| 4 stars | 85% or more | "Almost perfect!" — very few tiles left. |
| 5 stars | 94% or more | "Total conquest!" — legendary. Show it off. |

### How Stars Are Calculated

```
yourPercent = (tiles you own / total land tiles) x 100
```

Water tiles are NOT counted. Only land tiles (Plains, Forest, Mountain) count toward the total.

### How Stars Are Saved

- Saved in `localStorage` with key `hadooney_stars_{eraId}` (eraId is 1-20)
- Only the BEST star count is kept (if you got 3 stars before and get 4 stars now, it updates to 4)
- Stars are shown on the era selection screen as filled/empty star icons
- Stars persist even if you clear your browser cache (localStorage survives browser restarts)

**IMPLEMENTATION STATUS:** Stars are fully implemented and working correctly. The percentage calculation and thresholds match the design exactly.

---

## Win and Lose Conditions

| Condition | Result | What the Screen Shows |
|---|---|---|
| All AI opponents eliminated | **Victory!** | Star rating animation, detailed stats |
| You lose all your territories | **Defeat** | "You've been conquered!" message, stats |
| Timer runs out | **Time's Up!** | "The clock beat you!" message, stats |
| You surrender (from pause menu) | **Defeat** | Same as losing all territories |

### What Happens When Someone Is Eliminated

When a player (you or an AI) loses their last tile:
- They're marked as "eliminated" in the scoreboard (strikethrough + skull icon)
- Their color disappears from the map
- A notification appears: "[Player] has been eliminated!"
- If it's the last AI, the game ends in victory

### What the Result Screen Shows

| Stat | Description |
|---|---|
| Victory/Defeat banner | Big text at the top |
| Star rating | 1-5 stars with animation |
| Territories | How many tiles you own / total land tiles |
| Land percentage | Your % of the map |
| Turns played | How many turns the game lasted |
| Difficulty | Which difficulty you played on |
| Time used | How long the game took (MM:SS) |
| Opponents defeated | How many AIs you eliminated |
| Best star record | Your previous best for this era (if any) |

**IMPLEMENTATION STATUS:**
- Win/lose conditions for territory loss work.
- MISSING: Timer-based loss condition (no timer exists).
- MISSING: "Time used" stat on result screen.
- BUG: Best star record not shown on result screen.

---

## Save System

### How Saving Works

| Type | When It Happens | What It Saves |
|---|---|---|
| **Auto-save** | After every turn (yours and AI turns) | Full game state |
| **Manual save** | When you tap "Save Game" in the pause menu | Full game state |
| **Continue** | When you tap "Continue" on the title screen | Loads the most recent save |

### What Gets Saved

- Your era, difficulty, and opponent count
- All tile states: owner, troops, terrain, effects (fire, frozen, factory, defense bonus)
- All AI states: gold, troops per tile, personality, alive/eliminated
- Your gold and superpower used/not used
- Turn number
- Timer remaining (seconds left)
- Map layout (which tiles exist and where — so the same map loads back)

### How Loading Works

1. Read save data from `localStorage`
2. Rebuild the map using the SAVED tile positions (NOT generate a new random map)
3. Restore all tile states, player states, and AI states
4. Restore timer to saved value
5. Resume from where you left off

### Save Data Storage

- **Key:** `hadooney_save` in `localStorage`
- **Format:** JSON string
- **Survives:** Browser restarts, phone reboots, closing the tab
- **Lost if:** User clears browser data/site data

### Save Edge Cases

| Situation | What Happens |
|---|---|
| Save data is corrupted (invalid JSON) | Show "Save data is corrupted" on title screen. Hide Continue button. Delete the bad save. |
| Loading a save from an older version of the game | Best effort — load what we can, reset what we can't. Show a warning. |
| Player starts a new game while a save exists | Old save is overwritten by the new game's auto-save. No warning needed. |
| Very large map with many opponents | Save data might be big (50KB+). localStorage can hold 5MB, so this is fine. |

**IMPLEMENTATION STATUS:**
- Auto-save and manual save work.
- Continue button works.
- CRITICAL BUG: Loading a save calls `generateHexMap()` which creates a NEW random map. The saved tile positions may not exist on the new map. This causes lost territory data.
- FIX: Save the map layout (which hex positions exist) in the save data. On load, rebuild the EXACT same map instead of generating a new one.

---

## Play Time Tracker

Total play time across all sessions is tracked and shown on the title screen.

- **Format:** HH:MM:SS (like 02:34:17)
- **What counts:** Time spent on the game screen during your turns
- **What doesn't count:** Time on menus, time during AI turns, time on result screen
- **Storage:** `localStorage` key `hadooney_play_time` (in seconds)
- **Updates:** Every 1 second while on the game screen

**IMPLEMENTATION STATUS:** Fully implemented and working correctly.

---

## How to Play (Tutorial)

Accessible from the title screen via the "How to Play" button. It opens as a modal overlay.

### What the Tutorial Covers

1. **Objective:** Conquer territories, defeat all opponents, earn stars
2. **Controls:** Tap/click your tile, then tap an adjacent tile to attack or reinforce
3. **Your Turn:** You can attack, reinforce, recruit, or use your superpower. When done, tap END TURN.
4. **Recruit:** Spend gold to add troops to one of your tiles
5. **Special Power:** One-time ability unique to your era. Costs gold. Use it wisely!
6. **Terrain:** Plains (normal), Forest (+defense, +gold), Mountain (+defense), Water (blocked)
7. **Economy:** 1 gold per territory per turn. Forests give bonus gold.
8. **Timer:** Each difficulty has a time limit — if it runs out, you lose!
9. **Winning:** Eliminate all opponents. The more territory you control, the more stars you earn.
10. **Tips:** Attack weak enemies, defend forests and mountains, save your superpower for the right moment

**IMPLEMENTATION STATUS:** Tutorial modal exists and covers the basics. May need updating to mention the timer and difficulty-specific costs.

---

## Visual Design

### Battlefield Background

- Dark green gradient (`#1a2f1a` to `#1e3520`) — lighter than before for better contrast
- Subtle grass texture (200 small green dots scattered pseudo-randomly)
- Faint grid lines for spatial reference (very low opacity)

### Characters (Anime Warriors)

- Drawn using Canvas 2D gradients for a 3D cartoon look
- Each era has unique outfit colors (ERA_OUTFITS object: hair, skin, armor, weapon, helmet)
- Characters have: 3D spherical head with anime eyes, cylindrical torso/arms/legs, shield, helmet/hair
- Scale based on `gameState.armyScale` — adapts to screen size
- **Idle animation:** Subtle breathing bob (sine wave on Y position)
- **Multiple characters:** Armies with more troops show more characters (1 per 4 troops, max 5)
- **Hurt animation:** Flashing transparency when taking damage
- **Attack animation:** Weapon swing and arm movement during battle

### Army Bases and Labels

- Each army has a colored oval shadow/glow beneath it for contrast against the green field
- Label banner above each army: dark background with colored border, white text
- Shows: army name + troop count (e.g. "You  15 troops")
- Selected army: pulsing white circle highlight
- Enemy armies (when player selected): red circle highlights

### Battle Animations

When you attack an enemy army:
1. **Charge phase** (0.4s): Attacking character lunges toward target with motion lines
2. **Clash phase** (0.2s): Bright flash at midpoint with "CLASH!" text and color sparks
3. **Result phase** (0.25s): If won, particle explosion from defender. If lost, defender glows.

### HUD (Heads-Up Display)

Top of screen during gameplay — a horizontal bar showing:

| Element | Icon | Position | What It Shows |
|---|---|---|---|
| Troops | Sword | Left | Total troops across all your tiles |
| Gold | Coin | Left-center | Current gold amount |
| Territories | Castle | Center | Number of tiles you own |
| Timer | Clock | Center-right | MM:SS countdown (flashes yellow < 60s, red < 30s) |
| Turn | Arrow | Right | Current turn number |
| Music | Speaker | Far right | On/off toggle |

### Action Buttons (Bottom of Screen)

| Button | When Visible | What It Does |
|---|---|---|
| Recruit (shows cost) | Always during your turn | Enters recruit mode — tap one of your tiles to add troops |
| Power (shows cost or "Used") | Always during your turn | Activates superpower (enters targeting mode if needed) |
| End Turn | Always during your turn | Ends your turn, all AIs take their turns |
| Menu | Always | Opens pause menu (Resume, Save, Surrender) |

### Scoreboard

Shows on the right side of the screen (or bottom on phones):

- All players ranked by territory count
- Each entry shows: rank number, color dot, name, tile count, land percentage
- Eliminated players shown with strikethrough text and skull icon
- You are always highlighted (bold or different background)

### Result Screen

- **Victory:** Big celebratory text, confetti-like particles, star rating animates in one by one
- **Defeat:** Softer text, no confetti, stars shown grayed out
- **Stats panel:** Grid of stats (territories, percentage, turns, time, difficulty, opponents defeated)
- **Buttons:** "Play Again" (same settings) and "Main Menu"

**IMPLEMENTATION STATUS:**
- Characters, animations, hex tiles, HUD, scoreboard, and result screen all work well.
- MISSING: Timer element in HUD.
- MISSING: "Time used" stat on result screen.
- MISSING: Confetti on victory.

---

## Audio

### Music

- **Procedural** (generated by code, not audio files) — this is why the game works offline
- Each era has a unique musical style using different scales, tempos, and waveforms
- Music plays softly in the background during gameplay
- Toggle on/off with the speaker button in the HUD

### Sound Effects

Short beeps/tones for game events:

| Sound | When It Plays |
|---|---|
| Attack | When you attack a tile |
| Capture | When you successfully capture a tile |
| Recruit | When you recruit troops |
| Special power | When you activate your superpower |
| End turn | When you end your turn |
| Victory | When you win the game |
| Defeat | When you lose the game |
| Elimination | When any player is eliminated |

**IMPLEMENTATION STATUS:** Fully implemented. All 20 era music styles and 8 sound effects work correctly.

---

## Controls

| Action | Phone | Laptop |
|---|---|---|
| Select tile | Tap | Click |
| Attack enemy | Tap selected tile, then tap adjacent enemy | Click selected, then click adjacent enemy |
| Reinforce friendly | Tap selected tile, then tap adjacent friendly | Click selected, then click adjacent friendly |
| Deselect | Tap the same tile again | Click the same tile again |
| Recruit | Tap Recruit button, then tap your tile | Click Recruit, click your tile |
| Use superpower | Tap Power button, then follow instructions | Click Power, follow instructions |
| End turn | Tap END TURN | Click END TURN |
| Pause | Tap menu button | Click menu button |
| Hover info | Long-press a tile | Hover mouse over a tile |

**Touch handling:**
- `touch-action: none` on the canvas prevents scrolling
- `touch-action: manipulation` on body prevents double-tap zoom
- Touch events are handled with `touchstart`, `touchmove`, `touchend`

**IMPLEMENTATION STATUS:** All controls work correctly on both touch and click.

---

## Technical Notes (for reference)

- Single HTML file: `index.html` (~2700 lines)
- HTML5 Canvas for all game rendering
- No external libraries or dependencies
- JavaScript game logic, CSS styling, all inline
- `requestAnimationFrame` for 60fps rendering
- `setInterval(1s)` for game timer countdown and play time tracker
- localStorage for all persistence (saves, stars, play time)
- Web Audio API for music and sound effects
- Responsive design: works on phone screens (touch) and laptop screens (click)
- 7 spaces per indent level inside the script block — match this style

---

## Implementation Checklist

This is the master checklist. Every item has a unique ID. When a coding agent finishes an item, it should check the box by changing `[ ]` to `[x]`. Add a brief note after each completed item saying what was changed.

**How to read this:**
- `[ ]` = not done yet
- `[x]` = done
- Priority: Critical = game-breaking, High = major issue, Medium = noticeable, Low = polish
- "Refs" points to the section in this doc with full details

---

### New Features (to build)

- [ ] **F-01** — Timer system (Critical) | Refs: Difficulty > Timer
  > Add a countdown timer to the HUD. Counts down only during player turn. Pauses during AI turns and pause menu. Flashes yellow at <=60s, red at <=30s. When it hits 0, game ends in defeat ("Time's Up!"). Duration varies by difficulty (10min Easy → 5min Impossible).

- [ ] **F-02** — Difficulty parameter system (Critical) | Refs: Difficulty > Parameters, Difficulty > Implementation Instructions
  > Replace the single `power` multiplier with an 11-parameter object per difficulty level (timer, aiIncomePerTile, aiActions, aiAttackBonus, aiStartGold, aiStartTroops, playerIncomeMult, recruitCost, troopsPerRecruit, powerCost). See the `DIFFICULTY_PARAMS` example in the Implementation Instructions section. Wire every parameter into the relevant game system.

- [ ] **F-03** — AI superpowers (High) | Refs: AI > Superpowers
  > Each AI opponent can use its era's superpower once per game when it has >=20 gold. The AI picks the best target for its power type (e.g., weakest enemy tile for offensive powers, its strongest tile for defensive powers). Powers that need no targeting fire immediately. Powers that need targeting auto-pick the best option.

- [ ] **F-04** — Time used on result screen (Medium) | Refs: Win and Lose Conditions > What the Result Screen Shows
  > Track how long the game took from first turn to game end. Display it on the result screen as MM:SS. Include it in the save data so it survives load/continue.

- [ ] **F-05** — Spread starting positions (Medium) | Refs: Map > Starting Territories
  > When assigning starting territories, ensure each player's 3 tiles are at least 2 hexes away from every other player's starting tiles. If the map is too small for this, reduce the minimum distance to 1. Use the hex grid's distance formula (cube coordinates).

- [ ] **F-06** — Confetti on victory (Low) | Refs: Visual Design > Result Screen
  > When the player wins, spawn a burst of colorful particles on the result screen. Use the existing particle system. Particles should be era-colored and fall with gravity. Lasts ~3 seconds.

---

### Bug Fixes

- [ ] **B-01** — Aztec Blood Sacrifice doesn't deduct gold (Critical) | Refs: Eras > IMPLEMENTATION STATUS
  > In the Aztec power code, add `player.gold -= powerCost` before `player.specialUsed = true`. Same fix for Crystal Shield power — it also skips gold deduction.

- [ ] **B-02** — Thunder Chain Lightning picks first 3 enemies instead of random 3 (Critical) | Refs: Eras > IMPLEMENTATION STATUS
  > In the Thunder power code, after collecting all enemy tiles, shuffle the array before taking the first 3. Use `array.sort(() => Math.random() - 0.5)` or Fisher-Yates shuffle.

- [ ] **B-03** — Save/load regenerates map (Critical) | Refs: Save System > How Loading Works, Save System > IMPLEMENTATION STATUS
  > Save the full hex grid layout (which positions exist, their terrain types, row/col coordinates) in the save JSON. On load, rebuild the map from saved layout instead of calling `generateHexMap()`. This ensures saved tiles always exist on the loaded map. Key change: `continueSavedGame()` must NOT call `generateHexMap()`.

- [ ] **B-04** — Void Rift should make tile neutral with 0 troops (High) | Refs: Eras > IMPLEMENTATION STATUS
  > In Void Rift power code, set `targetTile.owner = null` (or equivalent for neutral) and `targetTile.troops = 0`. If the tile had a factory, remove the factory from the owner's factory count. Show a visual effect on the cleared tile.

- [ ] **B-05** — Naval Bombardment should work on neutral tiles (High) | Refs: Eras > IMPLEMENTATION STATUS
  > Remove the check `targetTile.owner !== 'player'` that prevents targeting neutral tiles. Allow targeting any tile with troops. If the tile has troops and loses them all, it becomes neutral with 0 troops (stays neutral if already neutral).

- [ ] **B-06** — AI personality chances distorted by difficulty (High) | Refs: AI > IMPLEMENTATION STATUS
  > In `processSingleAITurn`, do NOT multiply personality chances by `diffPower`. Use raw personality percentages (e.g., Aggressive attacks 70% of the time regardless of difficulty). Difficulty should affect AI through income, actions per turn, and attack bonus — NOT by warping personality.

- [ ] **B-07** — Real-time effects tick during pause menu (High) | Refs: Eras > Superpower Rules
  > When the pause menu opens, record the remaining time on all active setTimeout effects (Castle Wall, Great Wall, Samurai Honor). Clear the timeouts. When the menu closes, restart them with the remaining time. Alternatively, switch from setTimeout to a frame-based timer that checks `isPaused` each tick.

- [ ] **B-08** — Recruit button shows wrong cost (Medium) | Refs: Difficulty > IMPLEMENTATION STATUS
  > Update the Recruit button text to show the current difficulty's recruit cost. Read from `DIFFICULTY_PARAMS[currentDifficulty].recruitCost`. Update whenever difficulty changes or game starts.

- [ ] **B-09** — Power button shows wrong cost (Medium) | Refs: Difficulty > IMPLEMENTATION STATUS
  > Update the Power button text to show the current difficulty's power cost. Read from `DIFFICULTY_PARAMS[currentDifficulty].powerCost`. Update whenever difficulty changes or game starts. If power is already used, show "Used" instead.

- [ ] **B-10** — AI starting gold uses formula instead of per-difficulty values (Medium) | Refs: AI > IMPLEMENTATION STATUS
  > In `assignStartingTerritories` (or wherever AI gold is initialized), use `DIFFICULTY_PARAMS[difficulty].aiStartGold` instead of `15 + floor(opponentCount / 5) * 5`.

- [ ] **B-11** — AI starting troops not difficulty-based (Medium) | Refs: AI > IMPLEMENTATION STATUS
  > When placing AI starting troops, use `DIFFICULTY_PARAMS[difficulty].aiStartTroops` as the base. Add 0-1 random on top for variety (so the range is `aiStartTroops` to `aiStartTroops + 1`).

- [ ] **B-12** — Two-tile powers don't refund gold on cancel (Medium) | Refs: Eras > Superpower Rules
  > For Rome (Legion March), Shadow (Shadow Clone), and Quantum (Teleport): do NOT deduct gold when the first tile is selected. Instead, deduct gold only when the second tile is confirmed. If the user taps Cancel after selecting the first tile, no gold is lost.

- [ ] **B-13** — Window resize changes map layout (Low) | Refs: Map > IMPLEMENTATION STATUS
  > Store the random noise seed used during `generateHexMap()`. On resize, use the same seed so the same tiles exist. Recalculate hex pixel positions for the new screen size but keep the same logical grid.

- [ ] **B-14** — No fallback if map generation fails (Low) | Refs: Map > Edge Cases
  > In `generateHexMap`, if after generation there aren't enough land tiles for all players (3 per player), regenerate. Try up to 100 times. If still failing, use a hardcoded fallback grid that is guaranteed to work.

---

### Progress Tracker

| ID | Item | Priority | Status |
|---|---|---|---|
| F-01 | Timer system | Critical | [ ] |
| F-02 | Difficulty parameter system | Critical | [ ] |
| F-03 | AI superpowers | High | [ ] |
| F-04 | Time used on result screen | Medium | [ ] |
| F-05 | Spread starting positions | Medium | [ ] |
| F-06 | Confetti on victory | Low | [ ] |
| B-01 | Aztec/Crystal gold deduction | Critical | [ ] |
| B-02 | Thunder random targets | Critical | [ ] |
| B-03 | Save/load map regeneration | Critical | [ ] |
| B-04 | Void Rift makes neutral | High | [ ] |
| B-05 | Naval on neutral tiles | High | [ ] |
| B-06 | AI personality chance fix | High | [ ] |
| B-07 | Pause stops setTimeout | High | [ ] |
| B-08 | Recruit button cost | Medium | [ ] |
| B-09 | Power button cost | Medium | [ ] |
| B-10 | AI starting gold | Medium | [ ] |
| B-11 | AI starting troops | Medium | [ ] |
| B-12 | Two-tile power refund | Medium | [ ] |
| B-13 | Resize map stability | Low | [ ] |
| B-14 | Map generation fallback | Low | [ ] |
