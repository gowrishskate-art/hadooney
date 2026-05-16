# Hadooney — Empire Wars: Build Plan

## Goal
Build a complete browser-based empire conquest game and deploy to GitHub Pages.

## Current State
- Repo exists at ~/Documents/programming/hadooney/
- Remote: https://github.com/gowrishskate-art/hadooney.git
- Old blob runner backed up to index-old-blob-runner.html
- index.html will be overwritten with new game

## Steps

### Step 1: Write index.html — The Complete Game
- Single file: HTML + CSS + JS
- ~1500-2000 lines estimated
- Everything in one file so it works offline

### Step 2: Test in Browser
- Open file:// in browser
- Check console for JS errors
- Verify: menu, era select, opponent select, game plays, stars save, timer works

### Step 3: Fix Any Bugs Found in Step 2

### Step 4: Git Commit and Push
- git add index.html
- git commit -m "Empire Wars: 20 eras, hex map, AI opponents, star ratings"
- git push origin main

### Step 5: Verify GitHub Pages Link
- Check https://gowrishskate-art.github.io/hadooney/

## Files Changed
- index.html (complete rewrite)

## Verification
- Game loads without errors
- Can select era, choose opponents, play a full game
- Stars persist in localStorage
- Play time accumulates
- Works on mobile (touch) and desktop (click)
