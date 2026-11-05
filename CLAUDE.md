# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Asteroids clone: three static files, no build step, no dependencies, no tests, no linter.
`index.html` (canvas + CSS) → `game.js` (everything else) → `favicon.svg`.

## Running

Open `index.html` in a browser, or `npx serve .` then `http://localhost:3000`.
Deployed via GitHub Pages at https://klerith.github.io/claude-asteroids/.

## Architecture (game.js)

Single file, no modules, no exports — load order is just the one `<script>` tag.

- `W`/`H` (800×600) are hard-coded constants that must stay in sync with the `<canvas>` attributes in `index.html`.
- Classes `Bullet`, `Asteroid`, `Ship`, `Particle` each own `update(dt)` + `draw()` and a `dead` flag; collections are swept with `filter(x => !x.dead)` each frame. Entities draw themselves directly to the module-level `ctx`.
- Module-level mutable game state (`ship, bullets, asteroids, particles, score, lives, level, state, deadTimer`) is reset by `initGame()` / `nextLevel()`.
- `state` is `'playing' | 'dead' | 'gameover'`; `update(dt)` early-returns per state, so each state decides on its own which entities keep ticking.
- Fixed-timestep-free loop: `loop(ts)` computes `dt` from rAF timestamps, clamped to 0.05s so a backgrounded tab can't tunnel entities through each other.
- Input: `keys` for held keys, plus `pressed(code)` which consumes a one-shot `justPressed` edge (used for shoot and restart). Reading it twice in a frame swallows the press.
- Asteroid size is `1..3` (small..large) and indexes the parallel arrays `RADII`, `SPEEDS`, `POINTS`. Add a size → extend all three.
- Toroidal space via `wrap()`; note bullets and asteroids wrap but `Particle` does not, by design.

## Conventions

- UI text and code comments are in Spanish; the identifiers are in English.
- `'use strict'`, 2-space indent, aligned assignment blocks, section banners (`// ── Name ───…`).

## Note

`README.md` still advertises power-ups and a "estrella fugaz" asteroid; those were removed in commit `13e713f` and no longer exist in `game.js`.
