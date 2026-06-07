# Hadooney — Comprehensive Polish Plan

> Red-teaming audit of every screen, mechanic, and visual element.
> Priority: P0 (broken/must-fix) → P1 (major polish) → P2 (nice-to-have)

---

## P0 — BROKEN / MUST-FIX

### 1. 20 Opponents = Cluttered Battlefield
**Problem:** 21 hex tiles crammed into a 4:3 canvas. Warriors overlap, territory names are unreadable, flags blend together. On phone it's even worse — the canvas is smaller but same number of tiles.
**Fix:** Scale the hex grid intelligently:
- 1-6 opponents: current layout works fine (1-3 rows)
- 7-12 opponents: switch to **compact mode** — smaller hexes with abbreviated names (first 4 chars), no flags, warriors shrink
- 13-20 opponents: switch to **army-view mode** — no hex tiles at all, show armies as colored circles with troop badges on a clean field (like the old blob-runner mode but polished). Click a circle to select, click enemy to attack. This is how real strategy games handle 15+ factions (Civilization, Total War).
- Always show a **mini-map** toggle button so players can see full layout vs focused view

### 2. Scoreboard Takes 120px With 20 Players
**Problem:** The scoreboard shows ALL 21 players in a scrollable list that eats 120px of vertical space. On a phone screen (667px height), that's 18% of the screen wasted on a text list nobody reads during combat.
**Fix:**
- Collapsible scoreboard (tap to expand/collapse, starts collapsed)
- When collapsed: show a small bar with "21 alive | Your turn" 
- When expanded: full list
- During AI turns, auto-collapse so player sees the battlefield
- Show player's own stats (troops/gold) in the HUD header always (don't rely on scoreboard)

### 3. Game Controls Obscure the Battlefield
**Problem:** The action buttons (Recruit, Power, End Turn, Menu) sit BELOW the canvas. Combined with the header + scoreboard above, the actual game canvas is squeezed into a small center strip. On mobile, players constantly hit the wrong buttons.
**Fix:**
- Move action buttons into a **floating bottom bar overlaid on the canvas** (semi-transparent, like mobile game UIs — Clash of Clans, COC style)
- Make the canvas fill the full game screen with HUD overlaid on top
- Header stats (troops, gold, timer, turn) become a translucent top bar floating over the canvas
- This gives the battlefield maximum visual space

---

## P1 — MAJOR POLISH

### 4. No Visual Feedback When Selecting Armies
**Problem:** When you tap an army, the only feedback is a white pulse ring. No tooltip, no highlight of valid targets, no indication of what you can do next.
**Fix:**
- Selected army: bright glow + floating label "SELECTED - Tap enemy to attack"
- Valid targets (adjacent enemies): show a subtle red pulse/border
- If army has 1 troop: show "Can't attack (need 2+ troops)" tooltip
- Tapping empty space: brief "deselect" animation

### 5. AI Turn Processing Is Boring
**Problem:** When AI takes turns, the player just stares at the screen. Each AI takes 300ms, so 20 AIs = 6 seconds of nothing. No indication of what's happening.
**Fix:**
- Show a "turn banner" at top: "Viking Age is attacking..." with the AI's color
- Flash the attacking army briefly
- Show battle result as floating text ("-3 troops!") at the battle location
- Speed up: 100ms per AI instead of 300ms (saves 4 seconds with 20 opponents)
- Add a "Skip AI Turn" button for experienced players

### 6. Turn Income Popup Is Too Small
**Problem:** The turn summary ("Turn 5, +8 gold") appears as a tiny centered overlay that fades after 2 seconds. Easy to miss.
**Fix:**
- Animate gold coins falling from the top into the gold counter
- Flash the gold number when income arrives
- Show "+8g" floating up from the gold stat

### 7. No Sound/Music Controls
**Problem:** Music starts automatically. No way to mute or adjust volume without refreshing the page. Some players play at night or in class.
**Fix:**
- Add a small 🔊/🔇 toggle in the game header
- Save preference to localStorage
- Default: music ON, SFX ON (most games do this)

### 8. Era Cards Are Monotonous
**Problem:** All 20 era cards look identical — same layout, same info, just different emoji and color. It's a wall of cards that's hard to scan.
**Fix:**
- Group by type: "Historical Eras" section then "Fictional Eras" section with a divider
- Show a small power icon/preview on each card
- Previously beaten eras show a checkmark
- Unplayed eras have a subtle "NEW" badge

### 9. Difficulty Cards Don't Show Impact Clearly
**Problem:** The difficulty grid shows timer, recruit cost, and power cost. But the most impactful things (AI aggression, AI attack bonus, income penalty) aren't shown.
**Fix:**
- Add a small bar graph showing relative difficulty: AI strength, time pressure, economy
- Color-code: green (easy), yellow (medium), orange (hard+), red (demon+)
- Show a one-liner description: "Relaxed game, lots of time" vs "Brutal — AI gets 40% attack bonus"

### 10. No Way to See What a Power Does Before Picking an Era
**Problem:** Players must memorize all 20 powers or pick blindly. The How to Play modal has a long text list that's hard to reference while choosing.
**Fix:**
- On the era card, show the power name with a tooltip on hover/long-press
- Or: show a "Power: Sandstorm (tap to learn)" that expands to show the description
- Or: after selecting an era, show a power detail panel on the difficulty screen

### 11. The "How to Play" Modal Is a Wall of Text
**Problem:** It's a long scrolling text block. Nobody reads it. New players learn by failing, not by reading.
**Fix:**
- Replace with an interactive 5-step tutorial:
  1. "Tap your army to select it" (highlight player army)
  2. "Tap an enemy to attack" (highlight an enemy, show arrow)
  3. "End Turn when done" (highlight button)
  4. "Recruit troops with gold" (highlight recruit button)
  5. "Use your special power once per game" (highlight power button)
- Each step has a "Next →" button
- Skip button available
- Only shows on first play ever (check localStorage)

---

## P2 — NICE-TO-HAVE

### 12. No Victory/Defeat Animation
**Problem:** When the game ends, it just cuts to the result screen. No dramatic moment.
**Fix:**
- Victory: slow-motion camera pan + confetti + "VICTORY" text slam animation
- Defeat: screen shake + fade to gray + "DEFEAT" fade-in
- Show a "Personal Best" indicator if this is your best score for this era/difficulty combo

### 13. No Combat Preview
**Problem:** You never know if an attack will succeed. It's pure luck. This frustrates players who feel they made the "right" move but got unlucky.
**Fix:**
- Show an estimated power comparison before attacking: "Your 15 troops vs their 12 troops"
- Don't reveal the random factor, but show approximate odds: "Likely Victory", "Even Match", "Risky"
- This makes decisions feel informed rather than random

### 14. Stalemate / Long Games Are Boring
**Problem:** With many opponents and slow combat, games can drag. After turn 20 with 20 opponents, it's tedious.
**Fix:**
- Add a "speed up" button that makes AI turns instant (no 100ms delay)
- Show a progress indicator: "5/20 enemies defeated"
- Late-game: remaining AIs get stronger over time (escalation) so games naturally end

### 15. No Pause Explanation
**Problem:** The pause menu (accessible during game) has "Resume", "Surrender", "Main Menu". No explanation of what surrender does or confirmation.
**Fix:**
- Add confirmation: "Surrender? You'll lose all progress this game."
- Add "Settings" option in pause menu (sound toggle)

### 16. File Size Is 375KB (5821 Lines)
**Problem:** The file is getting large. Every feature adds more code. The realistic warrior rewrite added 1100 lines.
**Fix:**
- The warrior drawing function (844 lines) should be extracted into sub-functions:
  - `drawWarriorBody()` — torso, legs, boots
  - `drawWarriorArms()` — pauldrons, arms, gauntlets, shield
  - `drawWarriorHead()` — helmet, face, hair
  - `drawWarriorWeapon()` — already separate
- This doesn't split the file, but makes the code navigable

### 17. No Theme Consistency
**Problem:** The title screen has a gradient background. The game screen has era-specific backgrounds. The result screen has a plain dark background. Each screen feels disconnected.
**Fix:**
- Use the selected era's color palette across ALL screens during a game session
- Result screen: show the era's background with the result overlay
- Scoreboard: tint with era colors
- Buttons: use era accent color

### 18. Slider for Opponents Is Boring
**Problem:** A plain range slider from 1-20. No visual preview of what the battlefield will look like.
**Fix:**
- Show a small thumbnail preview of the hex grid layout that updates as you drag
- Or: use preset buttons: "Skirmish (3)", "Battle (6)", "War (10)", "Epic (15)", "Ultimate (20)"
- Or: both — preset buttons + fine-tune slider

### 19. Stars System Is Invisible
**Problem:** Stars are earned per era but there's no indication of progress toward the next star. The era cards show filled stars but no context.
**Fix:**
- On era card: show "3/5 ⭐" with a progress bar
- On result screen: "NEW BEST! You earned 4 stars (was 3)"
- Add a total stars counter on title screen

### 20. No Keyboard Support
**Problem:** Desktop players must use the mouse for everything. No hotkeys.
**Fix:**
- Space = End Turn
- R = Recruit
- P = Use Power
- Esc = Pause/Menu
- 1-9 = Select army by position

---

## IMPLEMENTATION ORDER

1. **P0 #1** — Smart hex scaling + army-view mode (biggest visual impact)
2. **P0 #3** — Floating HUD overlay (frees up canvas space)
3. **P0 #2** — Collapsible scoreboard
4. **P1 #4** — Selection feedback
5. **P1 #7** — Sound toggle
6. **P1 #5** — AI turn visualization
7. **P1 #13** — Combat preview
8. **P1 #11** — Interactive tutorial
9. **P2 items** as time allows

---

## SUMMARY

| Priority | Count | Examples |
|----------|-------|---------|
| P0 (Must-fix) | 3 | Cluttered battlefield, scoreboard waste, controls layout |
| P1 (Major polish) | 8 | No feedback, boring AI turns, no sound control, bad onboarding |
| P2 (Nice-to-have) | 9 | Animations, combat preview, keyboard support, theme consistency |
| **Total** | **20** | |

The three P0 fixes alone would transform the game from "cluttered and confusing at high player counts" to "clean and playable at any count." The P1 fixes would bring it from "functional" to "polished."
