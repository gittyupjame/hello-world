# CLAUDE.md

This file provides guidance to Claude Code when working with this repository.

## Project Overview

**Tap Target** is a proof-of-concept mobile browser game built as a single HTML file. Players tap randomly spawning colored circles before they disappear within a 30-second time limit. The game tracks score, accuracy, and maintains a session game log.

## Repository Structure

```
hello-world/
├── CLAUDE.md      # AI assistant guidance (this file)
└── index.html     # Complete game (HTML + CSS + JS, single file)
```

This is a minimal, zero-dependency project. Everything lives in `index.html`.

## Tech Stack

- **HTML5 / CSS3 / Vanilla JavaScript** — no frameworks, no build tools, no npm
- **Canvas API** — 2D rendering for game targets
- **Touch API** — mobile input handling with `touchstart` (passive: false)
- Device pixel ratio (DPR) scaling for high-density displays

## Architecture (index.html)

The single file contains three sections:

1. **CSS (lines 7-78)** — Dark theme styling, HUD layout, screen overlays, floating text animations
2. **HTML (lines 81-102)** — HUD, start screen, end screen with game log, and the `<canvas>` element
3. **JavaScript (lines 104-289)** — All game logic:
   - `resize()` — Canvas DPR scaling and dimension sync
   - `spawnTarget()` — Creates targets with position, radius, color, and adaptive lifetime
   - `handleTap()` / `getTapPos()` — Input processing with coordinate mapping via `getBoundingClientRect`
   - `draw()` — Render loop using `requestAnimationFrame`, handles fade-in/fade-out alpha
   - `startGame()` / `endGame()` — Game lifecycle, timer management, log entry creation

### Key Game Constants

| Constant | Value | Notes |
|---|---|---|
| `TARGET_RADIUS` | 30px | Base radius; actual spawns add 0-15px random |
| `SPAWN_INTERVAL` | 800ms | New target every 800ms |
| `TARGET_LIFETIME` | 1800ms | Base lifetime; scales down by `score * 15`, min 1200ms |
| Game duration | 30 seconds | Countdown timer |

## Development Guidelines

- **Keep changes small and focused** — this is a single-file project; changes should be surgical
- **Write clear commit messages** — use imperative mood, describe the "what" and "why" (e.g., "Fix tap detection on mobile by using getBoundingClientRect")
- **Test changes before committing** — open `index.html` in a browser; test on both desktop (click) and mobile (touch) if possible
- **No build step** — just edit `index.html` and reload in the browser
- **No external dependencies** — keep the project self-contained in one file
- **Maintain mobile-first design** — the game targets mobile browsers; always consider touch events, viewport units (`dvh`), and DPR handling

## Running the Project

Open `index.html` directly in any modern browser. No server required (though a local server works fine too):

```sh
# Option 1: Direct file open
open index.html            # macOS
xdg-open index.html        # Linux

# Option 2: Local server
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Testing

There is no automated test suite. Testing is manual:

1. Open the game in a browser
2. Click "Play" and verify targets spawn and are tappable
3. Verify score increments on hits, "miss" text shows on misses
4. Verify the timer counts down and the end screen shows correct stats
5. Verify the game log accumulates entries across rounds
6. Test on mobile or with browser dev tools in responsive/touch mode

## Common Pitfalls

- **Timing**: Game uses `Date.now()` consistently for target birth/lifetime tracking. Do not mix with `requestAnimationFrame` timestamps — this was a previous bug source.
- **Tap coordinates**: Always use `getBoundingClientRect()` for mapping client coordinates to canvas space. Direct `offsetX`/`offsetY` can be unreliable on mobile.
- **Canvas resize**: Call `resize()` at game start and on window resize. The canvas logical size must stay in sync with CSS size via DPR transform.
- **Target lifetime scaling**: Lifetime decreases with score (`score * 15` ms reduction, floored at 1200ms). Be careful adjusting these values — too aggressive makes the game unplayable at high scores.
