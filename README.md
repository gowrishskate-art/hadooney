# Hadooney — Empire Wars

A hex-based empire conquest game with 20 eras, anime-style warriors, and AI opponents. Conquer the map before time runs out!

## Quick Start

1. Open `index.html` in any browser (Chrome, Safari, Firefox)
2. That's it — no install, no server, works offline

**Live version:** [gowrishskate-art.github.io/hadooney](https://gowrishskate-art.github.io/hadooney/)

## How to Play

1. Choose an era (each has a unique superpower)
2. Pick a difficulty and number of opponents
3. Tap your tile, then tap an adjacent tile to attack
4. Recruit troops with gold, use your superpower once per game
5. Conquer all opponents before the timer runs out!

## What's in This Repo

| File | What It Is |
|---|---|
| `index.html` | The entire game (code + art + music — all in one file) |
| `game_design.md` | The game blueprint — every rule, number, era, and mechanic |
| `CLAUDE.md` | Instructions for Claude (the AI coding assistant) |
| `character-designs.html` | Visual design reference for warrior characters |

## Features

- 20 eras (10 historical + 10 fictional) with unique superpowers
- 6 difficulty levels with countdown timers (10 min down to 5 min)
- Hex-based tactical map with 4 terrain types
- Anime-style 3D warriors on every tile
- Star ratings saved per era
- Fully offline — no internet needed
- Works on Android phone + MacBook

## Editing the Game Design

The full game design lives in **`game_design.md`**. To view it nicely:

1. Open this folder in **VS Code** (free code editor from [code.visualstudio.com](https://code.visualstudio.com/))
2. Click on `game_design.md`
3. Press `Ctrl+Shift+V` (or `Cmd+Shift+V` on Mac) to preview it as a formatted document

> **What is Markdown?** It's a simple way to write formatted text using plain characters. `#` makes headings, `|` makes tables, `**` makes bold text. VS Code renders it into a nice-looking document when you preview it. Almost all documentation on GitHub uses Markdown.

## Tech

- HTML5 Canvas (rendering) | JavaScript (logic) | CSS (UI) | Web Audio API (sound)
- GitHub Pages (hosting) | localStorage (saves)
