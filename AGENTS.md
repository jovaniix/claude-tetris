# AGENTS.md

This file provides guidance to Codex (Codex.ai/code) when working with code in this repository.

## Running the game

No build step. Open directly or serve locally:

```bash
open index.html                  # macOS — direct open
python3 -m http.server 8000      # local server, then visit http://localhost:8000
```

## Architecture

Three files, no dependencies:

- `index.html` — DOM structure: `<canvas id="board">` (300×600px) for the playfield, `<canvas id="next-canvas">` (120×120px) for the next-piece preview, and `#overlay` for pause/game-over states.
- `style.css` — dark/retro aesthetic; layout via flexbox.
- `game.js` — all game logic (~305 lines, `'use strict'`, no modules).

### game.js internals

**State** is held in module-level `let` variables: `board` (2D array, `ROWS×COLS`, 0 = empty or color index 1–7), `current`/`next` (piece objects `{type, shape, x, y}`), `score/lines/level/paused/gameOver/dropAccum/dropInterval/animId`.

**Key functions and their roles:**

| Function | Role |
|---|---|
| `init()` | Full reset; starts `requestAnimationFrame` loop |
| `loop(ts)` | Game loop — accumulates `dt`, advances gravity, calls `draw()` |
| `collide(shape, ox, oy)` | Bounds + board overlap check |
| `tryRotate()` | CW rotation with ±1/±2 column wall kicks |
| `lockPiece()` | `merge()` → `clearLines()` → `spawn()` |
| `clearLines()` | Scans bottom-up, splices full rows, updates score/level/speed |
| `ghostY()` | Projects piece straight down for ghost rendering |
| `drawBlock(ctx, x, y, colorIndex, size, alpha)` | Renders a single cell with highlight strip |
| `draw()` | Full frame: clear → grid → board → ghost → current piece |

**Speed formula:** `dropInterval = Math.max(100, 1000 − (level − 1) × 90)` ms. Level increments every 10 lines.

**Scoring:** `LINE_SCORES = [0, 100, 300, 500, 800]` × level; soft drop +1/row, hard drop +2/cell.

### Canvas sizing dependency

`canvas width/height` in `index.html` must equal `COLS × BLOCK` and `ROWS × BLOCK` respectively. If you change `COLS`, `ROWS`, or `BLOCK` in `game.js`, update the `<canvas>` attributes in `index.html` to match.
