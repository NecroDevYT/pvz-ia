# AGENTS.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview
This project is a clone of Plants vs. Zombies implemented as a single-file HTML5 Canvas game.
- **Main File**: `index.html` contains all game logic, rendering code, and embedded CSS.
- **Assets**: stored in `recursos/` directory.
- **Task List**: `lista-tareas.md` contains the development roadmap and specific implementation rules.

## Common Commands

### Run the Game
Since this is a static HTML file, you can run it by:
1.  **Directly**: Open `index.html` in a web browser.
2.  **Local Server** (Recommended to avoid CORS issues with local files):
    -   Python: `python -m http.server 8000`
    -   Node: `npx serve .`
    -   Then open `http://localhost:8000`

### Build & Test
-   **No Build Step**: The project is raw HTML/JS.
-   **No Automated Tests**: Testing is done manually using "Dummy Triggers" (see Development Rules).

## Architecture & Code Structure

### Game Loop
The game runs on a `requestAnimationFrame` loop (`gameLoop` function).
1.  **Update**: `updateGame()` handles logic (movement, collisions, spawners).
2.  **Render**: The `gameLoop` clears the canvas and draws layers in order: Background -> Grid -> Entities -> UI.

### Entity Management
Entities are simple JS objects or classes stored in global arrays:
-   `plants`: Placed plants.
-   `zombies`: Active enemies.
-   `peas`: Projectiles.
-   `suns`: Collectible currency.
-   `lawnmowers`: Row defense.

### Grid System
-   Fixed 9x5 grid.
-   Coordinates are mapped using `GRID_START_X`, `GRID_START_Y`, `CELL_WIDTH`, `CELL_HEIGHT`.

### Asset Loading
-   `imageSources` object defines all asset paths.
-   `loadImages()` preloads all images before `initGame()` starts.

## Development Rules (CRITICAL)

**Adhere strictly to these rules found in `lista-tareas.md`:**

1.  **Single File Policy**: Do NOT create separate `.js` or `.css` files. Keep everything in `index.html`.
2.  **Anchor Injection**: Logic MUST be injected strictly between specific comment anchors provided in the tasks.
    -   Example: `// --- [INICIO_ZONA_CLASE_SUN_1.1] ---` ... code ... `// --- [FIN_ZONA_CLASE_SUN_1.1] ---`
    -   Do not modify code outside the requested anchors unless necessary for the specific task.
3.  **Strict Resource Usage**:
    -   **Visuals**: Use `ctx.drawImage(images.key, ...)` exclusively. DO NOT use primitive shapes (rectangles, circles) for game entities.
    -   **Audio**: Instantiate directly: `new Audio('recursos/sonidos/...').play()`.
4.  **Dummy Triggers**:
    -   When implementing a feature, include a temporary "Dummy Trigger" in `initGame()` or `updateGame()` inside the designated anchor to test the functionality immediately (e.g., spawning a zombie at start to test behavior).
5.  **Vanilla JS**: Use ES6+ features. No external libraries.

## Branching Strategy
-   Create a new branch for each task using the format specified in `lista-tareas.md` (e.g., `feat/tarea-1.1-mecanica-soles`).
