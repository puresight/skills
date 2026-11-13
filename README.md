# Retro & Tabletop Web Game Skills

Consolidated agent skills (Markdown + YAML frontmatter) for building original browser games inspired by 1970s–1980s arcade/console classics and tabletop board games in Bolt.new.

Start with [AGENTS.md](AGENTS.md) for the global stack and the **project-type → skills routing table**.

## Skills (7)

Each skill has a short `SKILL.md` (loaded when activated) plus sibling `.md` reference/template files (read on demand). All files are pure Markdown.

| Skill | Sibling references / templates | Use for |
|---|---|---|
| [game-design-brief](game-design-brief/SKILL.md) | [originality.md](game-design-brief/originality.md) | Design brief, naming, IP-safe reimagining of commercial games |
| [web-game-core](web-game-core/SKILL.md) | [loop.md](web-game-core/loop.md), [state-machine.md](web-game-core/state-machine.md), [input.md](web-game-core/input.md), [audio.md](web-game-core/audio.md), [persistence.md](web-game-core/persistence.md), [math.md](web-game-core/math.md) | Every game: 60 Hz loop, phases, input, chip audio, Dexie+Zod, math/RNG |
| [web-game-2d](web-game-2d/SKILL.md) | [canvas.md](web-game-2d/canvas.md), [collision.md](web-game-2d/collision.md), [retro-arcade.md](web-game-2d/retro-arcade.md) | 2D raster/vector Canvas, arcade collision vs physics, arcade flow |
| [web-game-platformer](web-game-platformer/SKILL.md) | [reference.md](web-game-platformer/reference.md) | Side-view platformers: jump feel, ladders, vines, hazards, LFSR rooms |
| [web-game-3d](web-game-3d/SKILL.md) | [scene.md](web-game-3d/scene.md), [style.md](web-game-3d/style.md), [assets.md](web-game-3d/assets.md) | React Three Fiber scenes, low-poly/vector style, GLB asset pipeline |
| [web-game-board](web-game-board/SKILL.md) | [boards.md](web-game-board/boards.md), [hotseat.md](web-game-board/hotseat.md) | Turn-based 3D grids, hexes, territory maps, hidden units, dice, hotseat |
| [web-game-chess](web-game-chess/SKILL.md) | [chess-game.md](web-game-chess/chess-game.md) | Chess & checkers rules via `chess.js`, square↔world mapping, PGN |

## Docs (human knowledge base)

[docs/README.md](docs/README.md) indexes the design theory and project studies (not auto-loaded by agents):
- [Fun & Design Principles](docs/fun-and-design-principles.md) · [Game Taxonomy](docs/game-taxonomy.md) · [Originality & IP](docs/originality-and-ip.md)
- [From 2D Boards to 3D Scenes](docs/board-to-3d.md) · [Visualization for Consulting](docs/visualization-for-consulting.md) · [Gamification](docs/gamification.md)
- [Game Studies](docs/game-studies/README.md): [Chess](docs/game-studies/chess.md), [Hidden-Identity Skirmish](docs/game-studies/hidden-identity-skirmish.md), [Territory Conquest](docs/game-studies/territory-conquest.md), [Jungle Runner](docs/game-studies/jungle-runner.md)

## Starter recipe: 2-player rotating-hotseat 3D chess

Load: `web-game-core`, `web-game-3d`, `web-game-board`, `web-game-chess`.
Assets: [Omies 3D Chess/Checkers Set](https://omies-assets.itch.io/3d-chesscheckers-set) → convert to GLB if needed, place in `public/models/chess.glb`, inspect node names (or run `npx gltfjsx`), and keep paid files out of public repos.
