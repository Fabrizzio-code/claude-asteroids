# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Asteroids arcade clone. Pure HTML5 Canvas + vanilla ES6. No framework, no bundler, no dependencies, no build step, no tests.

## Run

Open `index.html` directly in browser, or serve locally:

```bash
npx serve .
```

There is no build/lint/test tooling. Edit `game.js` → refresh browser.

## Architecture

Three files only: `index.html` (canvas + `<script>` tag), `game.js` (all logic), `favicon.svg`.

`game.js` is one file, structured top-to-bottom:

- **Input** — `keys` (held) + `justPressed`/`pressed()` (edge-triggered, one-shot). Use `pressed(code)` for fire/restart, `keys[code]` for continuous (rotate/thrust).
- **Entity classes** — `Bullet`, `Asteroid`, `Ship`, `Particle`. Each has `update(dt)` + `draw()`, a `dead` flag, and `x/y/vx/vy`. Dead entities filtered out each frame.
- **Module-level game state** — globals `ship, bullets, asteroids, particles, score, lives, level, state, deadTimer`. `state` ∈ `'playing' | 'dead' | 'gameover'`.
- **`update(dt)` / `draw()`** — single update + render pass.
- **`loop(ts)`** — rAF loop. `dt` is real elapsed seconds, clamped to 0.05 max.

Key conventions:
- **All motion is time-based** (`* dt`), speeds in px/s. Keep this when adding entities.
- **Toroidal space**: positions go through `wrap(v, max)`. Edges wrap around.
- **Collision** = circle distance via `dist()` vs radius. No spatial partitioning.
- Asteroid `size` 3→2→1 (big→small); `split()` spawns two of `size-1`, size 1 doesn't split. Per-size lookup arrays `RADII`/`SPEEDS`/`POINTS` are 1-indexed (index 0 unused).
- Rendering: white vector strokes on black, `ctx.save/translate/rotate/restore` per entity.

## Notes

- README is Spanish and describes power-ups + shooting-star asteroid that were **removed** (commits 13e713f, d78c249). Code is the source of truth, not README.
- Canvas is fixed 800×600 (`W`/`H` in `game.js`, `width`/`height` in `index.html` — keep in sync).
