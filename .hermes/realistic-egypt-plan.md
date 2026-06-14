# Realistic Egypt Map + Visual Overhaul Plan

## Current Problems
1. Canvas 960px on 633px viewport — game overflows screen, HUD covers half
2. Territories are rectangular grid tiles, not realistic land shapes
3. Troops are scattered dots — messy and ugly
4. Bottom 50% of Nile area unused
5. HUD takes too much visual space

## What to Change (Egypt era only, all others unchanged)

### 1. FIX CANVAS SIZING (CRITICAL — game is unplayable otherwise)
- File: index.html, functions `initBattlefield()` (~line 3541), `initGameCanvas()` (~line 3878), `handleWindowResize()` (~line 3937)
- Problem: canvas.width = rect.width * devicePixelRatio. On HiDPI this makes canvas 2x the CSS size, so all coordinates are wrong. The canvas CSS says width:100% height:100% but the internal resolution is 2x.
- FIX: Remove ALL devicePixelRatio multiplication. Canvas internal resolution should match CSS display size. So canvas.width = container.clientWidth, canvas.height = container.clientHeight. NO devicePixelRatio.
- Also: The `.game-canvas-container` CSS at line 959 sets `position:absolute;inset:0` which makes the container full-viewport. But the game-header and game-controls are position:fixed on top. The canvas renders BEHIND them. This is fine IF we account for the HUD area in the game rendering — or make the HUD semi-transparent so the game is visible behind it.
- Keep the CSS as-is (fullscreen canvas with floating HUD) but fix the canvas resolution.

### 2. REDESIGN EGYPT MAP — Realistic Nile Valley Geography
Replace the 40 rectangular territories with realistic land shapes that trace the actual Nile River geography. The map should look like a bird's-eye view of Egypt:

**Geographic Layout (top to bottom of screen):**
- **Mediterranean Coast** (top) — Alexandria, Rosetta, Damietta, Port Said along a curved coastline
- **Nile Delta** — fan-shaped network of rivers splitting from the main Nile
- **Lower Egypt** — Cairo/Giza area, Faiyum oasis to the west, Sinai to the east
- **Middle Egypt** — Beni Hasan, Amarna, Minya along the river
- **Upper Egypt** — Abydos, Dendera, Luxor/Thebes, Karnak, Valley of the Kings
- **Southern Egypt/Nubia** — Aswan, Philae, Abu Simbel near the bottom
- **Eastern Desert** — Red Sea Hills, Sinai mountains on the right side
- **Western Desert** — Kharga, Dakhla oases scattered in the sand

**Key Design Rules:**
- The Nile River should be a wide, winding blue line running roughly through the CENTER of the map (slightly left of center like real Egypt)
- Territory polygons should be IRREGULAR organic shapes — curved borders, not straight lines
- River territories (along the Nile banks) should be elongated north-south strips
- Desert territories on the east/west should be wider, more amorphous
- Mountain territories (Sinai, Red Sea Hills, Valley of Kings) should be angular/rocky shapes
- Oasis territories should be roughly circular
- Coast territories should have curved northern/eastern edges following the Mediterranean/Red Sea coast
- The ENTIRE canvas should be used — no empty space below the Nile
- Each territory needs ~8-12 polygon vertices (not just 4-6 rectangles)

**Territory Data Structure:**
Replace the current `ERA_MAPS.egypt.territories` array with new polygons. Keep the same:
- id values (0-39)
- name values (keep real Egyptian city/region names)
- terrain types (plains, river, desert, mountain, oasis, coast, water)
- adj arrays (adjacency — update to match new positions)

**Example realistic polygon format:**
```
{id:0, name:'Alexandria', poly:[[0.05,0.02],[0.15,0.01],[0.22,0.04],[0.24,0.10],[0.20,0.15],[0.10,0.16],[0.04,0.12]], terrain:'coast', adj:[1,8,9]}
```

### 3. REDRAW THE MAP — Realistic Visual Style
In `drawTerritoryMap()` (~line 6818) and `drawEraBackground()`:
- Draw a realistic Egypt background: sandy desert gradient, Mediterranean sea at top
- Draw the Nile River as a smooth wide blue curve running through the map
- Territory fills should use natural colors matching terrain:
  - Desert: sandy tan (#d4a574 → #c9956b)
  - River/nile bank: dark fertile green (#4a7a2c → #3a6a1c)
  - Mountain: gray-brown (#8B7355 → #6b5335)
  - Oasis: vivid green (#2d8a4e → #1d6a3e)
  - Coast: light blue-gray (#a0c0d8 → #8090a0)
  - Plains: yellow-green (#7a9a3c → #5a7a2c)
- Add subtle noise/texture variation per territory
- Territory borders should be thin, semi-transparent lines, not thick black
- Draw subtle terrain icons (pyramids near Giza, palm trees at oases, etc.)

### 4. FIX TROOP RENDERING — Organized Formations
In `drawArmy()` (~line 7466) and `drawTerritoryWarriors()` (~line 7536):
- Instead of scattered dots, draw troops as neat grid formations:
  - 1-5 troops: single row
  - 6-15 troops: 2 rows
  - 16-30 troops: 3 rows with spacing
  - 30+: 4 rows
- Each troop is a small shield icon (not a circle) — tiny colored rectangles with a dot
- Arrange formations centered on the territory center
- Add a small flag/banner above each army showing troop count
- Player troops: gold/yellow shields
- AI troops: their faction color shields
- Selected army has a glowing highlight ring

### 5. COMPACT HUD
The CSS at lines 959-1055 already makes the HUD floating and compact. Just verify:
- game-header: max 30px tall, semi-transparent dark background
- game-controls: max 35px tall, compact pill at bottom
- scoreboard: collapsible, compact on right side
- Make sure the game canvas renders behind the HUD (already does with z-index)

### 6. CANVAS CLICK HANDLING
The `handleCanvasClick()` function at line ~7790 converts click coordinates to canvas coordinates. If we remove devicePixelRatio from canvas sizing, we need to also remove it from the click coordinate conversion. Currently it probably multiplies by devicePixelRatio to convert CSS pixels to canvas pixels — this should be removed.

Check: `handleCanvasClick`, `handleMouseMove`, `getTerritoryAt` — any function that converts mouse/touch position to canvas coordinates and multiplies by devicePixelRatio.

## Implementation Order
1. Fix canvas sizing (remove devicePixelRatio) — TEST immediately
2. Replace Egypt map polygons with realistic shapes — TEST
3. Redraw map with terrain colors, Nile river — TEST
4. Fix troop formations — TEST
5. Verify HUD doesn't obstruct — TEST
6. Full playtest — play a full game to victory

## Critical Constraints
- Only modify Egypt era. Other eras must still work.
- JS must pass `node --check` after all changes
- File is single index.html (~12,000 lines)
- Keep existing game logic (combat, AI, scoring) unchanged
- 7 spaces indentation inside script block
