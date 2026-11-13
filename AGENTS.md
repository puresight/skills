# Agent Instructions & Skill Router

Global rules for building retro-inspired 2D/3D browser games and interactive 2D/3D data visualizations in Bolt.new (WebContainer). State these once; individual skills assume them.

## 1. Global stack & constraints
- **Runtime & UI**: React 19 + Vite + TypeScript (`strict: true`) + **Ark UI** (`@ark-ui/react`) headless state-machine components + Tailwind CSS for DOM overlays, HUDs, dialogs, and interactive visualization controls.
- **WebContainer**: in-browser Node — **no native C++ addons** (`node-gyp`). Use pure JS/TS or embedded Wasm (`@dimforge/rapier*-compat`).
- **Offline-first**: after initial load, zero network calls. Bundle fonts, models, GeoJSON, and textures in `/public/`. Never use CDN presets (e.g. drei `<Environment preset="...">`).
- **Architecture**: `src/game/` (games) and `src/viz/` (data visualizations) hold rules, state machines, data transforms, scales, and simulation in plain TypeScript (no React, no Three/Canvas/DOM imports). Renderers only observe state and emit actions.
- **Display continuum**: data visualizations and UI overlays must adapt cleanly across **mobile phones** (touch, portrait), **desktop/tablet** (pointer + keyboard), **televisions / 10-foot wallboards** (`16:9` no-scroll, large type, D-pad/auto-tour), and **print / PDF** (`@media print`, vector SVG, pattern fills, `preserveDrawingBuffer` for 3D).
- **Storage**: Dexie + Zod (`src/db.ts`) for high scores, settings, saved matches, and saved visualization views/scenarios.
- **Originality**: never use trademarked game/character names or ripped assets. Write `DESIGN_BRIEF.md` first for any non-public-domain inspiration.

## 2. Which skills to load (routing table)

Load **only** the skills for the current project type, and read a skill's sibling `.md` files **only** when implementing that part. Do not auto-load `docs/` (human reference).

| Project type | Skills to load |
|---|---|
| **3D Chess / Checkers (hotseat)** | `web-game-core`, `web-game-3d`, `web-game-board`, `web-game-chess` |
| **3D Turn-based board / map game** (Stratego-like hidden units, Risk-like conquest) | `game-design-brief`, `web-game-core`, `web-game-3d`, `web-game-board` |
| **2D Side-view platformer** (Pitfall-like jungle runner, ladder/girder) | `game-design-brief`, `web-game-core`, `web-game-2d`, `web-game-platformer` |
| **2.5D Side-view platformer** (low-poly 3D with side camera) | `game-design-brief`, `web-game-core`, `web-game-3d`, `web-game-platformer` |
| **2D Classic arcade** (shooter, maze, paddle/ball, vector) | `game-design-brief`, `web-game-core`, `web-game-2d` |
| **3D Action / vector arcade** (Battlezone/Tempest style) | `game-design-brief`, `web-game-core`, `web-game-3d` |
| **2D Data visualization / dashboard / report** (mobile, desktop, TV wallboard, print) | `web-dataviz` (plus `web-game-core` if using seeded simulation or Dexie persistence) |
| **3D Spatial / volumetric data visualization** (extruded maps, 3D surfaces/scatter, digital twins, linked 3D+2D) | `web-dataviz`, `web-game-3d`, `web-game-board` (for territory polygons/graphs) |

## 3. Copy-ready templates inside skills
Instead of regenerating boilerplate from scratch, copy the fenced TypeScript modules from these sibling files:
- Fixed-timestep loop → `web-game-core/loop.md`
- Phase state machines → `web-game-core/state-machine.md`
- Action-mapped input → `web-game-core/input.md`
- Web Audio chip synth (`tone`, `noise`) → `web-game-core/audio.md`
- Dexie + Zod database (`GameDB`) → `web-game-core/persistence.md`
- Seeded PRNG (`mulberry32`) → `web-game-core/math.md`
- String-grid sprite baker → `web-game-2d/canvas.md`
- 255-room reversible LFSR → `web-game-platformer/reference.md`
- Board graph & territory schema → `web-game-board/boards.md`
- Hotseat camera seats → `web-game-board/hotseat.md`
- `ChessGame` wrapper + `squareToWorld` → `web-game-chess/chess-game.md`
- Pure-TS scales, LTTB downsampling, `ChartFrame` & `rbush` picking → `web-dataviz/scales-and-charts.md`
- Ark UI (`@ark-ui/react`) `SegmentGroup`, `Slider`, `Select` & `Splitter` → `web-dataviz/ark-ui-controls.md`
- Multi-device `useDisplayMode` (`mobile | desktop | tv | print`), TV auto-tour & `@media print` CSS → `web-dataviz/responsive-devices.md`
- 3D Ortho/Perspective `DataCameraRig` & `<InstancedDataCloud>` → `web-dataviz/spatial-3d-dataviz.md`
