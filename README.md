# Retro Web Game Skills

Agent skills (Markdown + YAML frontmatter) for building browser reproductions of 1970s–1980s arcade and console games with Bolt.new.

**Assumed stack:** React 19 + Vite + TypeScript (strict) + Tailwind, Dexie + Zod for storage, Canvas 2D for raster games, React Three Fiber for 3D. Fully offline after load. Runs in WebContainers (no native Node modules).

## Skills

| Skill | Use for |
|---|---|
| [retro-arcade-fidelity](retro-arcade-fidelity/SKILL.md) | Start here for any retro remake: spec, resolution, palette, arcade flow, IP |
| [web-game-loop-architecture](web-game-loop-architecture/SKILL.md) | Fixed-timestep loop, React/R3F integration, pause |
| [web-game-state-machine](web-game-state-machine/SKILL.md) | Screens and turn phases |
| [web-game-input](web-game-input/SKILL.md) | Keyboard, gamepad, touch, pointer picking, 2P keys |
| [web-game-canvas-2d](web-game-canvas-2d/SKILL.md) | Pixel-perfect 2D rendering, sprites, tilemaps, vector glow |
| [web-game-physics-2d](web-game-physics-2d/SKILL.md) | Arcade collision vs rigid-body engines |
| [web-game-math](web-game-math/SKILL.md) | Vectors, angles, spatial indexing |
| [web-game-3d](web-game-3d/SKILL.md) | R3F scenes, cameras, picking, animation, lighting |
| [web-game-lowpoly-3d](web-game-lowpoly-3d/SKILL.md) | Low-poly / voxel / vector 3D art style |
| [web-game-3d-assets](web-game-3d-assets/SKILL.md) | Importing itch.io/Blockbench models, GLB pipeline, licensing |
| [web-game-chess](web-game-chess/SKILL.md) | Chess rules via chess.js, board mapping, PGN |
| [web-game-hotseat-multiplayer](web-game-hotseat-multiplayer/SKILL.md) | Local 2P: hotseat, alternating, simultaneous; camera rotation |
| [web-game-audio](web-game-audio/SKILL.md) | Web Audio chip-style SFX and jingles |
| [web-game-persistence](web-game-persistence/SKILL.md) | High scores, settings, saved games |

## Example: 2-player rotating-hotseat 3D chess (low-poly)

Skills: `web-game-3d` + `web-game-lowpoly-3d` + `web-game-3d-assets` + `web-game-chess` + `web-game-hotseat-multiplayer` + `web-game-state-machine` + `web-game-persistence` + `web-game-audio`.

Assets: [Omies 3D Chess/Checkers Set](https://omies-assets.itch.io/3d-chesscheckers-set). Convert to a single GLB if needed, upload to `public/models/`, and share its node names with the agent (or run `npx gltfjsx`). Keep the paid files out of public repos.

Starter prompt:
> Build a 2-player hotseat 3D chess game with React Three Fiber using the low-poly set in `/public/models/chess.glb`. Use chess.js for all rules. After each move, animate the piece, then rotate the camera to the other player's side (toggle in settings). Include legal-move highlights, promotion picker, check/checkmate/draw detection, move list, undo, resign, and resume from Dexie. Follow the skills in this repo.
