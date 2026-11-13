# Agent Instructions & Skill Router

Global rules for building retro-inspired 2D and 3D browser games in Bolt.new (WebContainer). State these once; individual skills assume them.

## 1. Global stack & constraints
- **Runtime**: React 19 + Vite + TypeScript (`strict: true`) + Tailwind CSS for DOM overlays.
- **WebContainer**: in-browser Node — **no native C++ addons** (`node-gyp`). Use pure JS/TS or embedded Wasm (`@dimforge/rapier*-compat`).
- **Offline-first**: after initial load, zero network calls. Bundle fonts, models, and textures in `/public/`. Never use CDN presets (e.g. drei `<Environment preset="...">`).
- **Architecture**: `src/game/` holds rules, state machine, and simulation in plain TypeScript (no React, no Three/Canvas imports). Renderers only observe state and emit actions.
- **Storage**: Dexie + Zod (`src/db.ts`) for high scores, settings, and saved matches.
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
