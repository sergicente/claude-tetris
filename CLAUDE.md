# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Vanilla JS Tetris (HTML5 Canvas). No dependencies, no build, no tests, no linter. UI text and README are in Spanish.

## Running

- `open index.html`, or serve statically: `python3 -m http.server 8000`

## Architecture

Three files, all logic in `game.js` (single classic script, no modules, `'use strict'`):

- `index.html` defines the DOM ids that `game.js` looks up at load (`board`, `next-canvas`, `score`, `lines`, `level`, `overlay`, `overlay-title`, `overlay-score`, `restart-btn`). Renaming an id requires updating the lookups at the top of `game.js`.
- Game state lives in module-level `let` globals (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, ...), reset by `init()`. Pieces are `{type, shape, x, y}`; `shape` is a matrix whose cell values are color indices into `COLORS`/`PIECES` (1–7, 0 = empty), so the board stores color index directly.
- Loop: `requestAnimationFrame(loop)` accumulates `dropAccum` against `dropInterval` (gravity); `draw()` runs every frame. Pause/game over cancel the rAF via `animId`; `togglePause` restarts it.
- Piece lifecycle: `lockPiece()` = `merge()` → `clearLines()` (updates score/level/`dropInterval`) → `spawn()` (promotes `next`, game over if spawn collides). Soft/hard drop add score; line-clear score is `LINE_SCORES[n] * level`.
- Rotation: `rotateCW` + `tryRotate` with horizontal-only wall kicks `[0,-1,1,-2,2]` (no SRS, no CW/CCW, no floor kicks). Piece selection is uniform random (no 7-bag).
- Keyboard handling is a single `keydown` listener (arrows, X/Up rotate, Space hard drop, P pause).
- `BLOCK` (30px) must match canvas size in `index.html` (300×600 = `COLS`×`ROWS`×`BLOCK`); the next-piece canvas is 120×120 (4×4 cells at 30px).
