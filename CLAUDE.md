# CLAUDE.md

This file provides guidance to Claude Code when working with this repository.

## Project Overview

**Tap Target** is a browser-based mobile game where players tap colored circles before they disappear. It is a single-page application built with vanilla HTML, CSS, and JavaScript — no frameworks or external dependencies.

The entire application lives in `index.html` (a single self-contained file).

## Repository Structure

```
hello-world/
├── CLAUDE.md      # AI assistant guidance (this file)
└── index.html     # Complete application (HTML + CSS + JS)
```

There is no build system, package manager, bundler, or CI/CD pipeline. The app runs by opening `index.html` directly in a browser.

## Architecture

`index.html` contains three embedded sections:

- **`<style>`** (lines 7–78) — All CSS including layout, HUD, screens, buttons, and floating text animations.
- **`<body>` markup** (lines 82–102) — HUD overlay, start/end screens, game log, and the `<canvas>` element.
- **`<script>`** (lines 104–290) — All game logic in vanilla JS using the Canvas 2D API.

### Key Game Constants

| Constant | Value | Purpose |
|---|---|---|
| `TARGET_RADIUS` | 30px | Base radius for spawned circles |
| `SPAWN_INTERVAL` | 800ms | Time between new target spawns |
| `TARGET_LIFETIME` | 1800ms | Base time before a target disappears |
| Game duration | 30 seconds | Fixed per round |

### Game Flow

1. **Start screen** → player taps "Play"
2. **Gameplay** → targets spawn every 800ms on the canvas; player taps to score; 30-second timer counts down
3. **End screen** → shows final score, hits, misses, accuracy %; session is logged to the game log
4. **Replay** → "Play Again" restarts the loop

### Key Functions

| Function | Purpose |
|---|---|
| `resize()` | Handles canvas sizing and high-DPI displays |
| `spawnTarget()` | Creates a target at a random position with dynamic lifetime |
| `handleTap(clientX, clientY)` | Hit detection via distance calculation against all targets |
| `getTapPos(clientX, clientY)` | Converts client coordinates to canvas-relative coordinates |
| `draw()` | Main render loop — clears canvas, removes expired targets, draws active targets with fade/scale effects |
| `startGame()` | Resets state, starts timers and render loop |
| `endGame()` | Stops timers, logs the session, shows end screen |
| `showFloatingText(x, y, text, cls)` | Creates temporary DOM elements for "+1" / "miss" feedback |

### Dynamic Difficulty

Target lifetime decreases as score increases: `Math.max(TARGET_LIFETIME - score * 15, 1200)`. At score 0 targets last 1800ms; at score 40 they last the minimum 1200ms.

## Development Guidelines

- **Keep changes small and focused** — this is a minimal single-file project; avoid unnecessary complexity
- **Write clear commit messages** — use imperative mood (e.g., "Fix", "Add", "Remove"), keep subjects under ~70 characters
- **Test changes before committing** — open `index.html` in a browser and verify both desktop (click) and mobile (touch) input work correctly
- **No build step required** — just edit `index.html` and reload in the browser
- **Preserve the single-file architecture** — do not split into multiple files unless there is a compelling reason
- **Maintain mobile-first design** — the app uses `touch-action: none`, viewport meta tag, and `dvh` units for mobile compatibility
- **High-DPI support** — canvas rendering uses `devicePixelRatio`; any canvas changes must account for this

## Testing

There is no automated test suite. Testing is manual:

1. Open `index.html` in a browser
2. Verify the start screen renders and the "Play" button works
3. Play a round — confirm targets spawn, taps register hits, misses show feedback
4. Verify the end screen shows correct score/accuracy and the game log updates
5. Test on both desktop (mouse click) and mobile (touch) if possible

## Code Style

- No linter or formatter is configured
- CSS uses utility-style classes (`.hidden`, `.btn`, `.hit-text`, `.miss-text`)
- JavaScript uses `let`/`const`, no `var`
- DOM elements are cached in top-level variables
- Animation uses `requestAnimationFrame`
- Event listeners use `{ passive: false }` for touch events to prevent scroll interference
