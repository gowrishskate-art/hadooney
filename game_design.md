# Hadooney — Empire Wars: Game Design Document

> **What is this file?** This is the blueprint for the entire game. Every feature, rule, number, and mechanic is written here. If you want to change how the game works, update this file first, then update the code.

---

## The Big Picture

**Hadooney** is a turn-based empire conquest game. You pick a historical or fictional era, each with a unique superpower, then conquer a hex-map by defeating AI opponents. Think Risk meets anime — colorful warriors battle on a map, and you have a ticking clock.

**Platforms:** Browser (Chrome, Safari, Firefox). Works on Android phone + MacBook. Fully offline — no internet needed.

**How to open:** Double-click `index.html` in your file manager, or drag it into a browser tab. That's it.

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

---

## The 20 Eras

Each era has a name, a color theme, and a one-time superpower that costs gold.

### Historical Eras

| # | Era | Color | Superpower | What It Does |
|---|---|---|---|---|
| 1 | Ancient Egypt | Gold | Sandstorm | Pick an enemy tile — kills 50% of their troops |
| 2 | Roman Empire | Red | Legion March | Pick two of your tiles — move troops from one to the other |
| 3 | Viking Age | Blue | Berserker Rage | Your next attack deals 2x damage |
| 4 | Medieval Europe | Gray | Castle Wall | Pick your tile — double its defense for 5 seconds |
| 5 | Ottoman Empire | Teal | Janissary | Pick your tile — get 15 free troops instantly |
| 6 | Aztec Empire | Green | Blood Sacrifice | Gain 30 gold instantly (no targeting needed) |
| 7 | Ming Dynasty | Orange | Great Wall | Pick your tile — nobody can enter it for 5 seconds |
| 8 | Edo Japan | Pink | Samurai Honor | Pick your tile — becomes invincible for 1.5 seconds |
| 9 | British Empire | Navy | Naval Bombardment | Pick any tile on the map — remove 5 troops |
| 10 | Industrial Revolution | Brown | Factory | Pick your tile — it produces +5 gold per turn forever |

### Fictional Eras

| # | Era | Color | Superpower | What It Does |
|---|---|---|---|---|
| 11 | Cyber Dynasty | Cyan | Hack | Pick an enemy tile — steal 3 troops from them |
| 12 | Quantum Realm | Purple | Teleport | Pick two tiles — swap their troop counts |
| 13 | Star Empire | White | Orbital Strike | Pick any tile — destroy ALL troops on it |
| 14 | Dragon Dominion | Dark Red | Dragon Breath | Pick a target tile — damage it AND all neighbors |
| 15 | Crystal Kingdom | Light Blue | Crystal Shield | All your tiles get +50% defense for 2 turns (no targeting) |
| 16 | Shadow Nexus | Dark Purple | Shadow Clone | Pick your tile — duplicate half its troops to an adjacent tile |
| 17 | Thunder Legion | Yellow | Chain Lightning | Automatically attacks up to 3 random enemy tiles (no targeting) |
| 18 | Void Collective | Black | Void Rift | Pick an enemy tile — remove it from the map entirely |
| 19 | Ember Ascendancy | Orange-Red | Inferno | Pick a tile — it and all neighbors catch fire (3 damage/turn, 3 turns) |
| 20 | Frost Dominion | Ice Blue | Blizzard | Freeze all adjacent enemy troops — they can't act for 1 turn |

---

## Difficulty Levels

Each difficulty changes multiple things at once: how much time you have, how smart/rich/strong the AI is, and how expensive things are for you.

### Timer (countdown)

If time runs out, you **automatically lose**. The timer is shown at the top of the screen during gameplay. It flashes yellow under 60 seconds and red under 30 seconds. Opening the pause menu pauses the timer.

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

### Design Philosophy

- **Easy** is for learning. You have lots of time, the AI is weak, and everything is cheap. You should win almost every time.
- **Medium** is the standard experience. Balanced challenge, comfortable pace.
- **Hard** makes you think fast (7 min) and spend wisely. The AI is a real competitor.
- **Crazy** is stressful — aggressive AI, tight clock. One bad decision can lose the game.
- **Demon** is for experienced players who know all the eras and powers. 5 minutes, strong AI.
- **Impossible** is the ultimate test. Same 5 minutes but the AI is overwhelming. Win rate should be ~5-15% for skilled players. It should feel like an achievement.

**What never changes between difficulties:** Terrain defense bonuses, your attack power formula, special power effects, starting territory count (3 for everyone).

---

## The Map

### Hex Grid

- The map is a hexagonal grid of hex-shaped tiles
- Each hex is one territory that can be owned by a player or AI
- The grid is generated randomly each game with some constraints to make it look like a continent

### Terrain Types

| Terrain | Defense Bonus | Gold Bonus | Notes |
|---|---|---|---|
| Plains | 1.0x (none) | +0 | Normal land. Most common (~65% of tiles) |
| Forest | 1.3x (+30%) | +1 extra gold | Good defensive position. ~15% of tiles |
| Mountain | 1.5x (+50%) | +0 | Hardest to attack. ~10% of tiles |
| Water | Impassable | — | Can't be owned or entered. ~10% of tiles |

### Starting Territories

- Player starts with 3 tiles, each with 3-5 troops
- Each AI starts with 3 tiles, each with (difficulty-based + 0-1 random) troops
- Remaining land tiles may have 1-3 neutral troops (30% chance)

---

## Economy (Gold System)

### Income (per turn)

- **Base:** 1 gold for each territory you own
- **Forest bonus:** +1 extra gold for each forest territory
- **Factory bonus:** +5 gold for each factory (from Industrial Revolution power)
- **Difficulty modifier:** At Hard and above, your income is multiplied by a value less than 1.0 (see difficulty table)

### Spending

| Action | Cost | What You Get |
|---|---|---|
| Recruit troops | Varies by difficulty (10-20g) | 4-5 troops on a tile you own |
| Use superpower | Varies by difficulty (20-30g) | One-time era ability (see era table) |

---

## Combat System

### How Battles Work

When you attack an adjacent enemy tile:

1. **Attack power** = your troops x (0.6 to 1.0 random) x berserker bonus (2x if active)
2. **Defense power** = their troops x (0.5 to 0.9 random) x terrain defense x defense bonuses
3. **AI attack bonus** adds on top of attack power (see difficulty table)

### Outcomes

| Result | What Happens |
|---|---|
| **Attacker wins** (attack > defense) | You take the tile. Surviving troops = your troops - (their troops x 0.7), minimum 1 |
| **Defender wins** (attack <= defense) | They keep the tile. Their surviving troops = their troops - (your troops x 0.5), minimum 1. Your tile drops to 1 troop |
| **Invincible tile** | Attack blocked entirely. You lose 1 troop |

### Special Conditions

- **Frozen troops** can't attack or be attacked
- **Tiles on fire** lose 3 troops per turn for 3 turns
- **Impassable tiles** can't be entered at all

---

## AI Opponents

### Personalities

Each AI gets one of four personalities, assigned in rotation:

| Personality | Attack Chance | Recruit Chance | Reinforce Chance | Play Style |
|---|---|---|---|---|
| Aggressive | 70% | 20% | 10% | Attacks a lot, recruits sometimes |
| Defensive | 30% | 50% | 20% | Builds up troops, attacks rarely |
| Balanced | 50% | 30% | 20% | Mix of everything |
| Cunning | 50% | 25% | 25% | Balanced but slightly trickier |

### How AI Chooses Targets

The AI always attacks the **weakest adjacent tile** it doesn't own. It doesn't strategize about which tiles are most valuable — it just goes for easy targets. This makes it predictable and beatable with good defense.

### AI Turn Flow

1. Collect income (based on difficulty income multiplier)
2. Take 1-8 actions (based on difficulty action range)
3. Each action: attack (personality chance), reinforce (personality chance), or recruit (if enough gold)
4. AI recruits at a flat 10 gold for 5 troops (not affected by player recruit cost)

---

## Star Rating System

Stars are awarded when you **win** a game. They're saved per-era, and only your best result is kept.

| Stars | Territory % Needed |
|---|---|
| 1 star | 20% or more |
| 2 stars | 50% or more |
| 3 stars | 75% or more |
| 4 stars | 85% or more |
| 5 stars | 94% or more |

Stars are shown on the era selection screen so you can see which eras you've mastered.

---

## Win and Lose Conditions

| Condition | Result |
|---|---|
| All AI opponents eliminated | **Victory!** Stars awarded based on territory % |
| You lose all your territories | **Defeat** |
| Timer runs out | **Time's Up!** (automatic loss) |
| You surrender (from pause menu) | **Defeat** |

---

## Save System

- **Auto-save:** After every turn
- **Manual save:** From the pause menu
- **Continue:** Title screen shows a Continue button if a save exists
- **Save data:** Era, difficulty, all player/AI states, all territories, turn number, timer state, play time
- **Storage:** Browser's localStorage (stays even after closing the browser)

---

## Play Time Tracker

Total play time across all sessions is tracked and shown on the title screen. Format: Hours:Minutes:Seconds. This persists in localStorage even between browser sessions.

---

## How to Play (Tutorial)

Accessible from the title screen. Covers:
- **Objective:** Conquer territories, defeat all opponents, earn stars
- **Controls:** Tap/click your tile, then tap an adjacent tile to attack or reinforce
- **Recruit:** Spend gold to add troops to your tiles
- **Special power:** One-time ability unique to your era
- **Terrain:** Plains (normal), Forest (+defense, +gold), Mountain (+defense), Water (blocked)
- **Economy:** 1 gold per territory per turn, forests give bonus
- **Timer:** Each difficulty has a time limit — if it runs out, you lose
- **End turn:** Click END TURN when you're done, then all AI opponents take their turns

---

## Visual Design

### Characters (Warriors)

- Drawn on hex tiles using Canvas 2D gradients for a 3D look
- Each era has unique outfit colors matching the era theme
- Characters have: 3D spherical head, cylindrical torso/arms/legs, shield, helmet/hair
- Scale based on hex size — characters should be large and clearly visible
- Idle animation: subtle breathing bob
- Multiple characters shown on tiles with higher troop counts

### Battle Animations

- Color-coded projectiles fly between attacker and defender
- Clash flash at the point of impact
- Particle explosions on territory capture
- Smooth troop movement animations along curved paths

### Hex Tiles

- Subtle background (very transparent terrain color) so characters stand out
- Selected tile: pulsing white highlight
- Adjacent friendly: green highlight
- Adjacent enemy: red highlight
- Terrain visual cues: grass for plains, trees for forest, peaks for mountain, waves for water

### HUD (Heads-Up Display)

Top of screen during gameplay:
- **Troops** (sword icon) — total troops across all your tiles
- **Gold** (coin icon) — current gold
- **Territories** (castle icon) — number of tiles you own
- **Timer** (clock icon) — MM:SS countdown, flashes when low
- **Turn** — current turn number
- **Music toggle** — on/off

### Scoreboard

Shows all players ranked by territory count. Eliminated players shown with strikethrough and skull.

### Result Screen

Shows: victory/defeat title, star rating, territories conquered, land percentage, turns played, difficulty, time used, opponents defeated.

---

## Audio

### Music

- Procedural (generated by code, not audio files)
- Each era has unique musical style: different scales, tempos, waveforms
- Toggle on/off with the speaker button in the HUD

### Sound Effects

Short beeps/tones for: attack, capture, recruit, special power, end turn, victory, defeat, elimination.

---

## Controls

| Action | Phone | Laptop |
|---|---|---|
| Select tile | Tap | Click |
| Attack/Reinforce | Tap adjacent tile | Click adjacent tile |
| Deselect | Tap same tile | Click same tile |
| Recruit | Tap Recruit button, then tap your tile | Click Recruit, click your tile |
| Special power | Tap Power button, then follow instructions | Click Power, follow instructions |
| End turn | Tap END TURN | Click END TURN |
| Pause | Tap menu button | Click menu button |

---

## Technical Notes (for reference)

- Single HTML file: `index.html` (~2700 lines)
- HTML5 Canvas for all game rendering
- No external libraries or dependencies
- JavaScript game logic, CSS styling, all inline
- `requestAnimationFrame` for 60fps rendering
- `setInterval(1s)` for game timer countdown
- localStorage for all persistence (saves, stars, play time)
- Web Audio API for music and sound effects
- Responsive design: works on phone screens (touch) and laptop screens (click)
