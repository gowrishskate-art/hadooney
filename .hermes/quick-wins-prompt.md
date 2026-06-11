You are working on the Hadooney Empire Wars browser game at /Users/Gowrish/Documents/programming/hadooney/index.html

This is a single HTML file (~6,312 lines, ~400KB) containing ALL CSS, HTML, and JS for a turn-based empire conquest strategy game. It runs on GitHub Pages, works offline, on Android + Mac.

IMPORTANT RULES:
- Keep it as a SINGLE index.html file (CSS in <style>, JS in <script>)
- Do NOT add any external dependencies (no npm, no libraries)
- Keep the 7-space indentation style in JS blocks
- Preserve ALL existing gameplay functionality — these are VISUAL only changes
- Test that the game still works after each change

Apply ALL of the following Quick Wins to the file. Do them ALL in one pass:

## QW1: Google Fonts
Add Google Fonts link for Cinzel (medieval serif) in the <head>:
<link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@400;700;900&display=swap" rel="stylesheet">
Apply it to:
- .logo { font-family: 'Cinzel', serif; }
- .screen-title { font-family: 'Cinzel', serif; }
- .result-title { font-family: 'Cinzel', serif; }
- .modal-title { font-family: 'Cinzel', serif; }
- h2.screen-title { font-family: 'Cinzel', serif; }
- body font-family stays as system font for body text

## QW2: Glass Morphism
Add these CSS enhancements:

For .game-header:
```css
background: rgba(10, 10, 26, 0.7);
backdrop-filter: blur(12px);
-webkit-backdrop-filter: blur(12px);
border: 1px solid rgba(255, 255, 255, 0.1);
border-radius: 15px;
padding: 12px 20px;
```

For .modal-content:
```css
background: rgba(26, 26, 46, 0.95);
backdrop-filter: blur(20px);
-webkit-backdrop-filter: blur(20px);
border: 1px solid rgba(255, 255, 255, 0.15);
box-shadow: 0 8px 32px rgba(0, 0, 0, 0.5);
```

For .scoreboard:
```css
background: rgba(10, 10, 26, 0.6);
backdrop-filter: blur(8px);
-webkit-backdrop-filter: blur(8px);
border: 1px solid rgba(255, 255, 255, 0.08);
```

For .turn-summary:
```css
background: rgba(10, 10, 26, 0.95);
backdrop-filter: blur(15px);
-webkit-backdrop-filter: blur(15px);
border: 1px solid rgba(102, 126, 234, 0.3);
box-shadow: 0 0 40px rgba(102, 126, 234, 0.15);
```

## QW3: Screen Transitions
Replace the current instant screen swap in showScreen() function with a smooth transition.

In the CSS, update .screen to use opacity and transform transitions:
```css
.screen {
    display: none;
    min-height: 100vh;
    padding: 20px;
    opacity: 0;
    transform: translateY(15px);
    transition: opacity 0.4s ease, transform 0.4s ease;
}
.screen.active {
    display: flex;
    flex-direction: column;
    align-items: center;
    opacity: 1;
    transform: translateY(0);
}
```

Then update the showScreen() JavaScript function. The current code does:
```
document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
document.getElementById(screenId).classList.add('active');
```

Change it to handle the display:none → display:flex → then opacity transition properly. Use a small setTimeout trick:
```javascript
function showScreen(screenId) {
    const screens = document.querySelectorAll('.screen');
    const target = document.getElementById(screenId);
    
    // Fade out current
    screens.forEach(s => {
        if (s.classList.contains('active')) {
            s.style.opacity = '0';
            s.style.transform = 'translateY(-15px)';
            setTimeout(() => {
                s.classList.remove('active');
                s.style.opacity = '';
                s.style.transform = '';
            }, 300);
        }
    });
    
    // Fade in target after brief delay
    setTimeout(() => {
        target.classList.add('active');
        // Force reflow for transition
        void target.offsetWidth;
        target.style.opacity = '1';
        target.style.transform = 'translateY(0)';
    }, 350);
    
    gameState.screen = screenId;
    if (screenId === 'game') {
        setTimeout(() => { initGameCanvas(); startAnimLoop(); }, 400);
    } else {
        stopAnimLoop();
    }
}
```

## QW4: Era-Colored Theme
Add a function that sets CSS custom properties based on the selected era's color. Add this CSS to the :root:
```css
:root {
    --era-primary: #667eea;
    --era-glow: rgba(102, 126, 234, 0.3);
}
body { 
    --era-primary: #667eea;
    --era-glow: rgba(102, 126, 234, 0.3);
}
```

Add a JS function:
```javascript
function applyEraTheme(era) {
    const color = era ? era.color : '#667eea';
    document.documentElement.style.setProperty('--era-primary', color);
    // Parse color to create glow with opacity
    const r = parseInt(color.slice(1,3),16);
    const g = parseInt(color.slice(3,5),16);  
    const b = parseInt(color.slice(5,7),16);
    document.documentElement.style.setProperty('--era-glow', `rgba(${r},${g},${b},0.3)`);
}
```

Call applyEraTheme() in selectEra() function after setting gameState.selectedEra.

Also in continueSavedGame() after restoring the era, and in initializeGame().

Use the --era-primary variable in:
- .btn-primary background gradient: use var(--era-primary)
- .game-header border-bottom: 2px solid var(--era-primary) 
- .screen-title color: var(--era-primary)
- .modal-title color: var(--era-primary)
- .stat-value color: var(--era-primary)

## QW5: Button Polish Upgrade
Enhance all buttons with richer hover/active states:

For .btn:
```css
.btn {
    position: relative;
    overflow: hidden;
    letter-spacing: 2px;
    text-transform: uppercase;
}
.btn::after {
    content: '';
    position: absolute;
    top: 50%;
    left: 50%;
    width: 0;
    height: 0;
    border-radius: 50%;
    background: rgba(255, 255, 255, 0.2);
    transition: width 0.4s, height 0.4s, top 0.4s, left 0.4s;
    transform: translate(-50%, -50%);
}
.btn:active::after {
    width: 300px;
    height: 300px;
}
```

For .btn-primary:
```css
.btn-primary {
    background: linear-gradient(135deg, var(--era-primary, #667eea), color-mix(in srgb, var(--era-primary, #667eea) 70%, #000));
    box-shadow: 0 4px 15px var(--era-glow, rgba(102, 126, 234, 0.3));
}
.btn-primary:hover {
    transform: scale(1.05) translateY(-2px);
    box-shadow: 0 8px 25px var(--era-glow, rgba(102, 126, 234, 0.5));
}
.btn-primary:active {
    transform: scale(0.98);
    box-shadow: 0 2px 10px var(--era-glow, rgba(102, 126, 234, 0.2));
}
```

NOTE: color-mix might not work in all browsers. Use a fallback approach — just keep the existing gradient but enhance with box-shadow using var(--era-glow).

For .game-btn hover:
```css
.game-btn:hover:not(:disabled) {
    transform: scale(1.05) translateY(-1px);
    filter: brightness(1.15);
}
.game-btn:active:not(:disabled) {
    transform: scale(0.95);
    filter: brightness(0.95);
}
```

## QW6: Floating Gold Text on Income
In the showTurnSummary() function (around line 6161), after showing the turn summary notification, add a floating "+Xg" animation.

Add this CSS:
```css
.floating-gold {
    position: fixed;
    font-family: 'Cinzel', serif;
    font-weight: bold;
    font-size: 1.2rem;
    color: #feca57;
    text-shadow: 0 0 10px rgba(254, 202, 87, 0.5);
    pointer-events: none;
    z-index: 300;
    animation: floatUp 1.5s ease-out forwards;
}
@keyframes floatUp {
    0% { opacity: 1; transform: translateY(0) scale(1); }
    50% { opacity: 1; transform: translateY(-30px) scale(1.2); }
    100% { opacity: 0; transform: translateY(-60px) scale(0.8); }
}
```

In showTurnSummary(), before showing the modal, create a floating element:
```javascript
const goldEl = document.getElementById('stat-gold');
if (goldEl && income > 0) {
    const rect = goldEl.getBoundingClientRect();
    const floater = document.createElement('div');
    floater.className = 'floating-gold';
    floater.textContent = '+' + income + 'g';
    floater.style.left = rect.left + 'px';
    floater.style.top = rect.top + 'px';
    document.body.appendChild(floater);
    setTimeout(() => floater.remove(), 1500);
}
```

## QW7: Screen Shake on Combat
Add a screen shake CSS class and apply it during combat.

CSS:
```css
@keyframes screenShake {
    0%, 100% { transform: translate(0, 0); }
    10% { transform: translate(-3px, -2px); }
    20% { transform: translate(3px, 1px); }
    30% { transform: translate(-2px, 3px); }
    40% { transform: translate(2px, -1px); }
    50% { transform: translate(-1px, 2px); }
    60% { transform: translate(1px, -2px); }
    70% { transform: translate(-3px, 1px); }
    80% { transform: translate(2px, -3px); }
    90% { transform: translate(-1px, 2px); }
}
.screen-shake {
    animation: screenShake 0.3s ease-out;
}
```

In the attackArmy() function (around line 5951), after calculating the battle result, add:
```javascript
const gameScreen = document.getElementById('game-screen');
gameScreen.classList.add('screen-shake');
setTimeout(() => gameScreen.classList.remove('screen-shake'), 300);
```

## QW8: Enhanced Victory Confetti
The current spawnConfetti() function (around line 4376) creates basic particles. Enhance it to use the era's color palette.

In spawnConfetti(), after it's called, also get the era color and create confetti in that color + gold + white:
Modify the function to accept an era color parameter:
```javascript
function spawnConfetti(color) {
    const eraColor = color || (gameState.selectedEra ? gameState.selectedEra.color : '#667eea');
    // ... existing code, but add eraColor as one of the confetti colors
    // In the particle creation loop, mix eraColor in with the existing random colors
}
```

Make sure endGame() calls spawnConfetti with the era color when victory is true.

## QW9: Dark Vignette Overlay
Add a CSS vignette to the body and game screen for dramatic framing.

CSS:
```css
body::before {
    content: '';
    position: fixed;
    top: 0; left: 0; right: 0; bottom: 0;
    background: radial-gradient(ellipse at center, transparent 50%, rgba(0,0,0,0.5) 100%);
    pointer-events: none;
    z-index: 1;
}
#game-canvas {
    position: relative;
    z-index: 0;
}
.game-header, .game-controls, .scoreboard, .action-panel {
    position: relative;
    z-index: 2;
}
.modal {
    z-index: 200;
}
```

NOTE: The vignette should NOT block clicks. The pointer-events: none handles that. But make sure it doesn't cover buttons — the z-index on interactive elements should be higher.

## QW10: Better Game Control Styling
Improve the game control buttons styling:

Add CSS:
```css
.game-btn {
    display: flex;
    align-items: center;
    gap: 6px;
    position: relative;
}
.btn-end-turn {
    font-size: 1rem;
    padding: 15px 30px;
    letter-spacing: 1px;
    background: linear-gradient(135deg, #ee5a6f, #c44569);
    color: white;
    box-shadow: 0 4px 15px rgba(238, 90, 111, 0.4);
    animation: endTurnPulse 2s ease-in-out infinite;
}
@keyframes endTurnPulse {
    0%, 100% { box-shadow: 0 4px 15px rgba(238, 90, 111, 0.4); }
    50% { box-shadow: 0 4px 25px rgba(238, 90, 111, 0.7); }
}
```

## CRITICAL RULES:
1. Do NOT break any existing gameplay — these are purely visual/CSS/animation changes
2. Keep the single-file structure intact
3. Make sure all existing functions still work (attackArmy, endTurn, startGame, etc.)
4. The game must still work offline (Google Fonts is the only external resource, which is fine — it gracefully falls back)
5. Test by reading back the key modified sections to verify correctness
6. After all changes, commit with message: "UI Quick Wins: glass HUD, Cinzel font, era theming, screen transitions, floating gold text, screen shake, vignette"

Go ahead and apply all 10 quick wins now. Read the file, make all changes, save, and verify.
