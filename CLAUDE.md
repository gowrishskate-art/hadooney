# Hadooney — Project Configuration for Claude

## Who Is Using This?

A 7th-grade student building a browser game for fun. No CS background. Treat every response like you're pairing with a smart kid who loves games but hasn't heard of "cyclomatic complexity" or "dependency injection."

**Communication rules:**
- No jargon without a one-line plain-English explanation right beside it
- If the kid says "make the characters fight gooder," translate that internally to "improve battle animation visuals and timing" — don't ask for clarification, just do it
- When the kid's request is vague, pick the most reasonable interpretation, do it, and briefly mention what you chose (so they can course-correct)
- Celebrate progress with the kid — "we just got warriors rendering in 3D!" is better than "implemented gradient shading in drawAnimeCharacter"
- Keep responses short. One paragraph max unless explaining something new
- If you need to do something that takes many steps, just do them — don't list out a 15-step plan and ask permission for each one. Only pause for the big choices

## Prompt Interpretation Guide

Kids describe what they *see* and *feel*, not what the code should *do*. Common translations:

| Kid says | Probably means |
|---|---|
| "make it cooler" | Add more visual effects, animations, or polish |
| "the characters are empty" | The warriors aren't rendering or are too small to see |
| "make it harder but not too hard" | Increase difficulty parameters but keep the game winnable with good strategy |
| "add more stuff" | Add a feature — pick something fun and fitting, don't ask which one |
| "it's broken" | Something crashed or looks wrong — investigate and fix |
| "make it like [other game]" | Adopt a specific mechanic or visual style from that game |
| "can you make it so..." | Feature request — implement it |
| "I don't like this" | Something looks or feels bad — investigate what and improve it |

## Project Overview

- **What:** Browser-based empire conquest strategy game
- **Where:** Single HTML file (`index.html`) — all code, styles, and scripts live in one file
- **Runs on:** Any browser, offline OK, works on Android phone + MacBook
- **Hosted on:** GitHub Pages at `https://gowrishskate-art.github.io/hadooney/`
- **Repo:** `https://github.com/gowrishskate-art/hadooney.git`

## Game Design Document

The full game design (what the game should do, all features, rules, eras, etc.) lives in **`game_design.md`** in this folder. When the kid says "I want to add X" or "change how Y works," update `game_design.md` first, then implement.

## Code Quality Rules

### Keep It Simple (the "Idiot Index")

Think of complexity as a score — the more branches, nested ifs, and parameters a function has, the higher the score. Lower is better.

- **Local score (one function):** Max 7 branches/decisions. If higher, break it into smaller pieces
- **Global score (whole system):** Minimize how many pieces depend on each other. If changing one function forces you to change five others, something's tangled up
- **File size:** This file is one big HTML file. If any section (CSS, JS block, HTML section) grows past ~300 lines, it's time to think about organization within the file
- **Function size:** Max ~20 lines. If longer, extract a helper

### Reuse Before Creating

Before writing new code:
1. Search the existing file for similar patterns (`grep` for keywords)
2. Can an existing function be extended instead of making a new one?
3. Only create something new when nothing existing can do the job

### No Dead Code

If something isn't used, delete it completely. No "removed this" comments, no renamed unused variables. Clean = gone.

### Comments

Write code that explains itself through good names. Only add a comment when the *why* isn't obvious — a hidden constraint, a workaround for a browser bug, or behavior that would surprise someone reading it.

## Performance Rules

- The game runs at 60fps using `requestAnimationFrame` — never block the main thread
- Canvas redraws should be fast: no creating new objects in the render loop, cache what you can
- `setInterval` is only for the game timer (1 tick/second) — everything else uses `requestAnimationFrame`
- Touch events: prevent default to stop scrolling, but don't add unnecessary listeners
- Particle systems: cap the total count (currently ~50) so phones don't lag

## Game-Specific Technical Notes

### The Single-File Structure

Everything is in `index.html`:
- `<style>` block — all CSS
- `<body>` — all HTML (screens, modals, canvas)
- `<script>` block — all JavaScript

This is fine for this project. Don't try to split into multiple files unless the kid asks.

### Indentation

This file uses **spaces** (7 spaces per indent level inside the script). Match the existing style — don't convert to tabs.

### Canvas Rendering

- Characters are drawn with Canvas 2D gradients (radialGradient for spheres, linearGradient for cylinders)
- `drawAnimeCharacter()` is the main rendering function — it's large (~200 lines) because it draws body parts individually. This is OK for now
- `darkenColor()` and `lightenColor()` are helper functions for 3D shading

### State Management

- `gameState` is one big object — screen, player, opponents, territories, turn, timer, etc.
- Save/load uses `localStorage` key `hadooney_save`
- Stars persist per-era in `localStorage` key `hadooney_stars_{eraId}`
- Play time persists in `localStorage` key `hadooney_play_time`

### Audio

- Web Audio API — procedural music generated per era (different scales/tempos/waveforms)
- Sound effects: short oscillator beeps for actions (attack, capture, recruit, etc.)
- No external audio files

## Tools & Plugins

### Compound Engineering Plugin (installed)

Skills available — use them autonomously when patterns match:
- **Code review** → `ce-code-review` (after significant changes)
- **Planning** → `ce-plan` (before big features — includes SWOT analysis)
- **Optimizing** → `ce-optimize` (when performance needs improvement)
- **Simplifying** → `ce-simplify-code` (after refactors to verify quality)

Don't ask the kid for permission to use these. Just use them when appropriate.

### QMD (installed globally)

Local markdown search with embeddings. Indexed collection: `hadooney`.
Use `qmd query` to search project markdown files when looking up design decisions or past context.

### GStack

Not currently installed locally. Skip if referenced.

## What NOT to Do

- Don't add external dependencies — the game must work offline with zero installs
- Don't split into multiple files unless asked
- Don't add npm packages to the game itself (playwright for testing is OK)
- Don't over-explain decisions to the kid — show the result, mention what changed, move on
- Don't ask "which approach do you prefer?" for small decisions — pick the best one and go
- Don't leave TODO comments in the code — either do it now or don't mention it
- Don't add features beyond what was asked — three similar lines beats a premature abstraction

## Git & Deployment

- Branch: `main`
- GitHub Pages auto-deploys from `main`
- The kid expects to see changes live at the GitHub Pages URL after pushing
- Never force-push to `main`

### Git Hygiene Rules

The working tree must be **clean** (no uncommitted changes, no untracked junk) before starting any implementation work. This is how we make sure we can always go back if something goes wrong.

**Before starting work on a feature or bug fix:**
1. Check `git status` — if it's not clean, commit or stash first
2. Commit with a clear message explaining what you're about to change
3. Now you have a safe rollback point

**After finishing work:**
1. Check `git status` — make sure only expected files changed
2. `git diff` — review what changed, make sure nothing unexpected got modified
3. Commit with a clear message
4. Push to origin

**What to track:**
- `index.html` — the game
- `game_design.md` — the design document
- `CLAUDE.md` — this file (project config for coding agents)
- `README.md` — project readme
- `.gitignore` — ignore rules
- `package.json` + `package-lock.json` — Playwright test dependency

**What to ignore** (covered by `.gitignore`):
- `node_modules/` — installed npm packages (reinstall with `npm install`)
- `.claude/` — Claude Code session config (personal to each developer)
- `test-results/` — Playwright test output (regenerated on each test run)
- `.DS_Store`, `Thumbs.db` — OS junk
- `.vscode/`, `.idea/`, `*.swp` — editor junk

**Commit message style:**
- Short and clear: what changed and why
- Examples: "Fix Aztec power not deducting gold", "Add countdown timer system"
- Always include `Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>`
