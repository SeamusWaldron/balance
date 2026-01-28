# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Balance (Equilibrium Study No. 2) is a single-page HTML5 Canvas game inspired by Mondrian art. Players maintain visual equilibrium by manipulating colored rectangles through line dragging, splitting, and merging mechanics.

## Development

**No build tools required.** The entire game is self-contained in `game.html` with inline CSS and JavaScript.

- **Run:** Open `game.html` directly in any modern browser (works with file:// protocol)
- **Edit:** Modify `game.html` directly - all game logic is in the embedded `<script>` section

## Architecture

### Core Classes

- **Rectangle** - Colored rectangles with position (x, y), dimensions (w, h), and colorKey. Has smooth animation via renderX/renderY properties
- **Line** - Derived from rectangle edges (vertical/horizontal), supports dragging to resize adjacent rectangles

### Game State

Centralized `state` object tracks:
- `balanceScore` - Invisible 0-1 score (higher = more unstable)
- `isGameOver`, `draggedLine`, `hoveredRect`, `hoveredLine`
- `stressDuration` - Time spent above imbalance threshold
- `driftElapsed` - Time since drift began (starts at 20s)

### Balance System

Four weighted components calculate the invisible balance score:
1. **Center of mass** - Distance from canvas center
2. **Color area distribution** - Each color has weight (RED: 2.2, BLUE: 1.8, YELLOW: 1.4, WHITE: 0.4)
3. **Rectangle complexity** - Ideal count is ~8-9 rectangles
4. Visual feedback escalates through 4 stress bands: STABLE → TENSION → WARNING → COLLAPSE

### Game Loop

```
gameLoop(time) → update(dt) → draw() → requestAnimationFrame()
```

- **update()**: Applies passive drift, calculates balance, updates animation positions (0.15 lerp)
- **draw()**: Renders rectangles/lines with stress-based visual effects (desaturation, jitter, vignette)

### Player Actions

- **Line drag** - Resize adjacent rectangles (constrained by MIN_RECT_DIM = 20px)
- **Space** - Split largest eligible rectangle (requires ≥12% of canvas area)
- **Double-click** - Merge adjacent same-color rectangles (stress-biased tolerances)

### Key Constants

- Canvas: 600×600px
- Loss threshold: Balance > 0.75 for > 5 seconds
- Drift starts: 20 seconds into game
- Merge tolerances: BASE_EDGE_TOL=22, BASE_ALIGN_TOL=16, BASE_MAX_MERGE_AREA=0.42

## Design Reference

`specification.md` contains detailed game mechanics, balance calculations, onboarding flow, and acceptance criteria.
