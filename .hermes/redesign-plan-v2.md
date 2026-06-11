# Hadooney Empire Wars — Redesign Plan v2
# FUN FIRST. Specific code changes. Testable. No fluff.

---

## Phase 1: Tutorial & Onboarding (Mechanics First)

### 1.1 First-time tutorial overlay
- **File:** `~/Documents/programming/hadooney/index.html`
- **Create:** `function showTutorial()` (~line 3400, after `initGame()`)
- **Behavior:** Check `localStorage.getItem('hadooney_tutorial_done')`. If null, show a semi-transparent overlay div with 3 sequential steps: (1) "Tap your army to select it" with arrow pointing to player sprite, (2) "Tap an enemy to attack" with arrow to nearest AI, (3) "End Turn when done — AI will attack!" pointing to End Turn button. Each step has a "Next →" button. Step 3 has "Let's Go!" that sets the localStorage flag and removes overlay.
- **Implementation:** Create `<div id="tutorial-overlay">` in the HTML body section. Style it `position:fixed; inset:0; z-index:500; background:rgba(0,0,0,0.7)`. Inner content is 3 div steps, only one visible at a time via `.tut-step.active` class toggling. Arrows are absolute-positioned emoji arrows.
- **Test:** Open game fresh (clear localStorage). Tutorial appears. Complete 3 steps. Refresh — tutorial does NOT reappear. Click "Skip" to dismiss immediately.
- **Lines:** ~80

### 1.2 Combat result prediction tooltip
- **Modify:** `function renderGame()` (~line 6170) — after drawing selected army highlight, check if `gameState.selectedArmy` is set and mouse is near an enemy. If so, draw a small box near the enemy showing predicted outcome.
- **Create:** `function getCombatPrediction(attacker, defender)` placed before `attackArmy()` at line 6963
- **Behavior:** Runs the same math as `attackArmy()` but deterministically (use 0.75 multiplier for both sides instead of random). Returns `{win: bool, attackerLoss: int, defenderLoss: int}`. Renders as text near cursor: "⚔ Likely WIN — lose ~5 troops" in green, or "⚠ Risky — lose ~8 troops" in orange.
- **Modify:** `attackArmy()` line 6968 — extract the multiplier logic into a helper `calcCombatPower(troops, isDefender)` so both prediction and real combat use identical math (the random part is an additional param).
- **Test:** Select your army, hover over an enemy with more troops. Tooltip says "Risky". Hover over weaker enemy. Tooltip says "Likely WIN". Attack — actual result matches prediction ±2 troops.
- **Lines:** ~45

---

## Phase 2: Territory Strategy (Make Territories MATTER)

### 2.1 Territory defense bonus in combat
- **Modify:** `function attackArmy()` at line 6965
- **Change:** After line 6975 (`let defPower = defender.troops * (0.5 + Math.random() * 0.4)`), add terrain bonus lookup:
  ```
  const defTerritory = getTerritoryAt(defender.x, defender.y);
  if (defTerritory) {
      const terrainBonus = {mountain: 1.4, forest: 1.25, city: 1.2, plains: 1.0};
      defPower *= (terrainBonus[defTerritory.terrain] || 1.0);
  }
  ```
- **Create:** `function getTerritoryAt(x, y)` at line 6963 — iterates `_territoryPaths`, checks if point is inside polygon (use existing ray-cast from `getArmyAtPosition` pattern at ~line 6940).
- **Modify:** `function drawTerritoryMap()` at line 6228 — when drawing each territory, add a small terrain icon (⛰ mountain,🌲 forest,🏙 city,🌿 plains) at territory center.
- **Test:** Move army to mountain territory. Get attacked. Defender takes fewer casualties than on plains. Check scoreboard before/after.
- **Lines:** ~30

### 2.2 Territory income bonus
- **Modify:** `function startNewTurn()` at line 7213
- **Change:** Replace `let income = 5 + (gameState.player.defeated || 0) * 2;` with:
  ```
  let baseIncome = 5;
  const ownedTerrs = (gameState.territories || []).filter(t => t.owner === 'player');
  const terrIncome = ownedTerrs.reduce((sum, t) => {
      const bonus = {mountain: 2, forest: 1, city: 3, plains: 1};
      return sum + (bonus[t.terrain] || 1);
  }, 0);
  let income = baseIncome + terrIncome + (gameState.player.defeated || 0) * 2;
  ```
- **Modify:** `function showTurnSummary()` at line 7183 — show breakdown: "5 base + 4 territory + 4 victories = 13 gold"
- **Test:** Own 1 territory. Get +1-3 extra gold per turn. Own 3 territories. Get +3-9 extra. Confirm AI also gets this bonus (check `processSingleAITurn` line 7140 — add same territory income calculation for AI).
- **Lines:** ~25

### 2.3 Territory capture on attack win
- **Modify:** `function attackArmy()` at line 6981 — inside the `if (atkPower > defPower)` block (line 6981), after `defender.eliminated = true`, add:
  ```
  const defTerritory = getTerritoryAt(defender.x, defender.y);
  if (defTerritory) defTerritory.owner = attacker.id;
  ```
- **Modify:** `function drawTerritoryMap()` at line 6228 — use `t.owner` to tint the territory fill color (currently uses faction color from army presence, which breaks when army is eliminated). Check `_territoryById` for owner, tint territory border/fill with owner's color at 30% opacity.
- **Test:** Attack an enemy on their territory. Win. Territory color changes to your faction color. Your income increases next turn.
- **Lines:** ~15

### 2.4 Adjacency requirement for attacks
- **Modify:** `function attackArmy()` at line 6965 — add at the very start after the troops check (line 6966):
  ```
  const atkTerritory = getTerritoryAt(attacker.x, attacker.y);
  const defTerritory = getTerritoryAt(defender.x, defender.y);
  if (atkTerritory && defTerritory && atkTerritory.id !== defTerritory.id) {
      const dynamicAdj = getDynamicAdjacency(atkTerritory.id);
      if (!dynamicAdj.includes(defTerritory.id)) {
          showPowerIndicator('Not adjacent! Move closer first.');
          return;
      }
  }
  ```
- **Create:** `function getDynamicAdjacency(terrId)` — looks up the dynamically generated adjacency from `dynamicTerrs` at line 3431-3450 (need to store this on `gameState` — add `gameState.territoryAdj = {}` populated during init at line 3444).
- **Modify:** Init code at line 3451 — add `gameState.territoryAdj[t.id] = t.adj;` inside the loop.
- **Modify:** `function drawTerritoryMap()` — highlight adjacent territories when an army is selected (same glow as selected army).
- **Test:** Try to attack a non-adjacent enemy. Get "Not adjacent!" message. Only can attack adjacent. AI respects same rule (add check in `processSingleAITurn` line 7151 — filter `alive` to only adjacent enemies).
- **Lines:** ~40

---

## Phase 3: AI Personality Visibility

### 3.1 AI chat bubbles during their turns
- **Modify:** `function processSingleAITurn()` at line 7138
- **Create:** `function showAIBubble(ai, text)` — creates a floating div near AI's position on the game canvas that auto-fades after 2 seconds.
- **Change:** Add personality-based messages at key moments:
  - **Before attack** (after line 7151): Aggressive says random from `["You're next!","CHARGE!","Fear me!"]`, Defensive says `["Stay back...","Protect the borders!"]`, Balanced says `["Let's be smart about this."]`, Cunning says `["All going according to plan..."]`
  - **After recruiting** (line 7155): Aggressive: `["More warriors!"]`, Defensive: `["Strengthening defenses."]`, Cunning: `["Building my forces quietly..."]`
  - **When attacked by player and survives:** Taunt. Aggressive: `["Is that all you've got?!"]`, Cunning: `["Predictable."]`
- **Implementation:** `showAIBubble(ai, text)` creates `<div class="ai-bubble">` positioned absolute over canvas at `(ai.x, ai.y - 40)`. CSS: white bg, dark text, border-radius 12px, fade-out animation 2s.
- **Test:** Play a game. Watch AI turns. See chat bubbles appear with personality-specific messages. Aggressive AI says "CHARGE!" before attacking.
- **Lines:** ~50

### 3.2 Different AI targeting behavior per personality
- **Modify:** `function processSingleAITurn()` line 7152
- **Change:** Replace `alive.sort((a, b) => a.troops - b.troops)` with personality-specific targeting:
  ```
  switch (ai.personality) {
      case AI_PERSONALITIES.AGGRESSIVE:
          alive.sort((a, b) => a.troops - b.troops); break;  // weakest
      case AI_PERSONALITIES.DEFENSIVE:
          alive.sort((a, b) => (a._threatLevel || 0) - (b._threatLevel || 0)); break;  // biggest threat
      case AI_PERSONALITIES.BALANCED:
          alive.sort((a, b) => a.troops - b.troops); break;  // weakest
      case AI_PERSONALITIES.CUNNING:
          alive.sort((a, b) => b.troops - a.troops); break;  // strongest (risky but high reward)
  }
  ```
- **Add threat tracking:** In `attackArmy()`, after any attack, increment `attacker._threatLevel = (attacker._threatLevel || 0) + defender.troops`. This gives Defensive AI actual strategic behavior — it goes after the biggest threat, not the weakest.
- **Modify:** Cunning AI also steals via powers more often — at line 7144, change `Math.random() < 0.3` to `Math.random() < 0.5` for Cunning.
- **Test:** Play vs Aggressive — it always attacks weakest. Play vs Defensive — it attacks whoever has the highest threat level (you, if you're winning). Play vs Cunning — it targets strongest and uses more powers.
- **Lines:** ~25

### 3.3 AI personality indicator on scoreboard
- **Modify:** `function updateScoreboard()` — add emoji next to each AI name: Aggressive="🔥", Defensive="🛡️", Balanced="⚖️", Cunning="🦊"
- **Change:** At the score-entry div (line 7125), inject the emoji before the name.
- **Test:** Open scoreboard. Each AI has a visible personality icon.
- **Lines:** ~10

---

## Phase 4: Era Differentiation (Play Different, Not Just Look Different)

### 4.1 Era-specific combat modifiers
- **Modify:** `function attackArmy()` at line 6968
- **Change:** After calculating `atkPower`, apply era modifier:
  ```
  const ERA_COMBAT_MODS = {
      viking: {atk: 1.15, def: 1.0},    // +15% attack, berserker culture
      medieval: {atk: 1.0, def: 1.15},  // +15% defense, castle era
      aztec: {atk: 1.1, def: 0.9},      // +10% atk, -10% def, aggressive but fragile
      roman: {atk: 1.0, def: 1.1},      // +10% defense, legion shields
      dragon: {atk: 1.2, def: 0.85},    // +20% atk, -15% def, glass cannon
      frost: {atk: 0.9, def: 1.2},      // -10% atk, +20% def, ice fortress
      ember: {atk: 1.1, def: 1.0},      // +10% atk, fire power
      shadow: {atk: 1.1, def: 0.95},     // slight atk, slight def loss
      cyber: {atk: 1.05, def: 1.05}     // balanced +5% each
  };
  const attackerEra = attacker.eraId || gameState.selectedEra?.id;
  const defEra = defender.eraId || gameState.selectedEra?.id;
  const atkMod = ERA_COMBAT_MODS[attackerEra];
  if (atkMod) atkPower *= atkMod.atk;
  const defMod = ERA_COMBAT_MODS[defEra];
  if (defMod) defPower *= defMod.def;
  ```
- **Modify:** Era selection screen — show the combat modifier prominently next to the era power description.
- **Test:** Pick Viking era. Attack. Do 20 attacks, average losses should be ~15% lower than Roman era attacking same targets. Pick Dragon — high damage but take more damage when attacked.
- **Lines:** ~25

### 4.2 Era-specific economy modifier
- **Modify:** `function startNewTurn()` at line 7213
- **Change:** Add era income modifier:
  ```
  const ERA_INCOME_MODS = {
      aztec: 1.3,       // +30% gold, treasure culture
      british: 1.2,     // +20% gold, trade empire
      industrial: 1.2,  // +20% gold, factories
      viking: 0.8,      // -20% gold, raiders not farmers
      ember: 0.9        // -10% gold, everything burns
  };
  const eraMod = ERA_INCOME_MODS[gameState.selectedEra?.id] || 1.0;
  income = Math.floor(income * eraMod);
  ```
- **Test:** Pick Aztec. Income is 30% higher than base. Pick Viking. Income is 20% lower.
- **Lines:** ~12

### 4.3 Show era bonuses on HUD
- **Modify:** `function updateGameUI()` — add a small indicator near the gold display showing active era bonuses: "+15% ATK (Viking)" or "+30% Gold (Aztec)"
- **Test:** Start game as Viking. See "+15% ATK" indicator near gold.
- **Lines:** ~8

---

## Phase 5: Combat Depth

### 5.1 Counter-attack mechanic
- **Modify:** `function attackArmy()` at line 6990 (the "attack failed" else block)
- **Change:** When attacker loses, the defender counter-attacks for reduced damage:
  ```
  } else {
      const loss = Math.floor(attacker.troops * 0.5);
      attacker.troops = Math.max(1, attacker.troops - loss);
      defender.troops = Math.max(1, defender.troops - Math.floor(attacker.troops * 0.3));
      // Counter-attack: defender deals 20% of their power back
      const counterDmg = Math.floor(defender.troops * 0.15);
      attacker.troops = Math.max(1, attacker.troops - counterDmg);
      showPowerIndicator('Attack failed! Counter-attack! -' + counterDmg + ' troops');
  }
  ```
- **Test:** Attack someone stronger. Lose. Check that you lose MORE troops than before (counter-attack added). Check defender still loses some troops from the initial clash.
- **Lines:** ~10

### 5.2 Retreat option
- **Modify:** Canvas click handler at line 6953-6960
- **Change:** When `gameState.selectedArmy` is set and player clicks an empty territory (not on an army), if that territory is adjacent, MOVE the army there instead of deselecting:
  ```
  if (!faction) {
      if (gameState.selectedArmy && gameState.selectedArmy.id === 'player') {
          const clickedTerr = getTerritoryAt(x, y);
          if (clickedTerr) {
              const playerTerr = getTerritoryAt(gameState.player.x, gameState.player.y);
              if (playerTerr && getDynamicAdjacency(playerTerr.id).includes(clickedTerr.id) && clickedTerr.owner === 'player') {
                  gameState.player.x = x; gameState.player.y = y;
                  showPowerIndicator('Moved!');
                  gameState.selectedArmy = null;
                  renderGame(); return;
              }
          }
      }
      gameState.selectedArmy = null; renderGame(); return;
  }
  ```
- **Test:** Select your army. Click an adjacent friendly territory. Army moves there (sprite repositions). Click non-adjacent territory. Nothing happens. This costs the player's attack action for the turn — after moving, deselect.
- **Lines:** ~20

### 5.3 Army composition (simple: infantry/cavalry split)
- **Modify:** Recruit function at line 7006. Add a second recruit option: "Recruit Cavalry (15g, 3 troops, +20% attack)"
- **Add to gameState:** `gameState.player.cavalry = 0` and `ai.cavalry = 0`
- **Modify:** `attackArmy()` line 6968:
  ```
  let cavalryBonus = 1 + (attacker.cavalry || 0) * 0.05;  // each cavalry = +5% atk
  atkPower *= cavalryBonus;
  ```
- **Modify:** Recruit panel UI — add "Recruit Cavalry" button with different cost/power
- **Modify:** `processSingleAITurn()` — AI recruits cavalry 30% of the time
- **Test:** Recruit 4 cavalry. Attack. Do noticeably more damage. Check gold spent is higher.
- **Lines:** ~35

---

## Phase 6: Storyline & Campaign

### 6.1 Era intro narrative
- **Modify:** `function startGame()` (wherever era is selected, ~line 3340)
- **Create:** `function showEraIntro(era)` — shows a full-screen overlay with era name, a 2-3 sentence story blurb, and a "Begin Campaign" button.
- **Add data:** `ERA_INTROS` object:
  ```
  const ERA_INTROS = {
      egypt: "The pharaohs have fallen. rival generals carve the Nile into warring states. Unify Egypt under your banner.",
      rome: "Rome is burning. Legions have split into factions. Only one will rebuild the empire.",
      viking: "The longships gather. Jarls across the fjords clash for supremacy. Prove your worth in the shield wall.",
      medieval: "Castles dot the war-torn countryside. Feudal lords wage endless war. Claim the throne.",
      // ... one line per era
  };
  ```
- **Test:** Select Egypt era. See narrative overlay. Click "Begin". Game starts.
- **Lines:** ~50

### 6.2 Victory/defeat narrative
- **Modify:** `function endGame()` at line 7242
- **Change:** Add era-specific victory/defeat messages:
  ```
  const VICTORY_MSGS = {
      egypt: "The Nile is united once more. You are Pharaoh.",
      rome: "SPQR! Rome stands restored under your rule.",
      viking: "All fjords bow to the true Jarl. Valhalla awaits.",
      // ...
  };
  ```
- **Test:** Win a game. See era-specific victory message.
- **Lines:** ~20

---

## Phase 7: Visual Upgrades (Small Wins, Not Overhauls)

### 7.1 Army health bar on territory
- **Modify:** `function drawTerritoryMap()` at line 6228 — after drawing the flag (line 6333), draw a small HP bar below the army sprite:
  ```
  const maxHP = (faction.troops + (faction.cavalry || 0)) * 1.5;
  const barW = rt * 0.6;
  const barH = 3;
  const hpRatio = Math.min(1, faction.troops / Math.max(1, maxHP));
  ctx.fillStyle = '#333'; ctx.fillRect(cx - barW/2, cy + rt * 0.6, barW, barH);
  ctx.fillStyle = hpRatio > 0.5 ? '#44ff44' : hpRatio > 0.25 ? '#ffaa00' : '#ff4444';
  ctx.fillRect(cx - barW/2, cy + rt * 0.6, barW * hpRatio, barH);
  ```
- **Test:** Start game. See green bars under armies. Attack an army. Bar shrinks. Turns yellow then red.
- **Lines:** ~10

### 7.2 Territory pulse when income collected
- **Modify:** `function startNewTurn()` at line 7216 — after adding gold, trigger a brief pulse animation on owned territories:
  ```
  document.querySelectorAll('.territory-owned-pulse').forEach(el => el.remove());
  ownedTerrs.forEach(t => {
      const el = document.createElement('div');
      el.className = 'territory-owned-pulse';
      // position over territory center
  });
  ```
- **CSS:** `.territory-owned-pulse` — a brief green glow that fades in 0.8s
- **Test:** Start new turn. Owned territories briefly glow green.
- **Lines:** ~15

---

## Phase 8: Polish & QA

### 8.1 Balance pass constants
- **Create:** `BALANCE` object near `DIFFICULTY_PARAMS` (line 1810):
  ```
  const BALANCE = {
      terrainDefense: {mountain: 1.4, forest: 1.25, city: 1.2, plains: 1.0},
      terrainIncome: {mountain: 2, forest: 1, city: 3, plains: 1},
      counterAttackRatio: 0.15,
      cavalryAtkBonus: 0.05,
      cavalryCost: 15,
      cavalryTroops: 3,
      retreatAllowed: true
  };
  ```
- Replace all hardcoded values in Phase 2-5 code with `BALANCE.*` references. This makes tuning a single-point edit.
- **Test:** Play 3 full games on Medium. No era feels dramatically overpowered. Adjust BALANCE values if needed.
- **Lines:** ~15 (define) + refactoring existing new code

### 8.2 Save/load territory state
- **Modify:** `function saveGame()` at line 7306 — add `territories: gameState.territories` and `territoryAdj: gameState.territoryAdj` to save data.
- **Modify:** `function loadGame()` — restore these fields. Add `cavalry` to player and opponent save/load.
- **Test:** Save mid-game. Refresh. Territories, owners, cavalry all restored correctly.
- **Lines:** ~10

### 8.3 Edge case hardening
- **Add guards:** In `attackArmy()`, verify both armies are alive before proceeding (already has some, add `if (attacker.troops <= 0) return;` at top).
- **Add guard:** In `startNewTurn()`, verify player is not eliminated before adding income.
- **Test:** Simulate edge cases via browser console — set troops to 0 mid-game, save corrupt state, etc.
- **Lines:** ~5

---

## Summary

| Phase | Items | Est. Lines | Priority |
|-------|-------|-----------|----------|
| 1: Tutorial | 2 | 125 | P0 — new players quit without this |
| 2: Territory | 4 | 110 | P0 — core of strategy, zero value currently |
| 3: AI Personality | 3 | 85 | P1 — makes repeat plays feel different |
| 4: Era Differentiation | 3 | 45 | P1 — justifies 20 eras existing |
| 5: Combat Depth | 3 | 65 | P1 — currently just coin flip |
| 6: Storyline | 2 | 70 | P2 — nice to have, not critical |
| 7: Visual Upgrades | 2 | 25 | P2 — quick wins |
| 8: Polish | 3 | 30 | P2 — stability |
| **Total** | **22 items** | **~555** | |

**Recommended order:** 1.1 → 1.2 → 2.1 → 2.2 → 2.3 → 2.4 → 3.1 → 3.2 → 3.3 → 5.1 → 5.2 → 4.1 → 4.2 → 5.3 → 4.3 → 6.1 → 6.2 → 7.1 → 7.2 → 8.1 → 8.2 → 8.3

Phases 1-2 make the game actually playable as a strategy game. Phase 3 makes it replayable. Phases 4-5 add depth. The rest is polish.
