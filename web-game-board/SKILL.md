---
name: web-game-board
description: "Use when building turn-based board, tabletop, or map games (grid, hex, or Risk-like territory graphs; Stratego-like hidden units; dice combat; hotseat or alternating players): graph topology, extruded 3D regions, redacted per-player views, camera seat rotation, and pass-the-device handoff."
---

# Turn-Based Boards, Maps & Local Multiplayer

Pair with `web-game-core`, `web-game-3d` (for 3D scenes), and `game-design-brief`. For chess, also load `web-game-chess`.

## Rules at a glance
1. **Strict layering**: `rules/` (pure TS: state, legal actions, resolve) → `view/redact(state, viewer)` → `scene/` (renders only the redacted view). Never keep a separate UI `currentPlayer` — read it from game state. → [boards.md](boards.md)
2. **One board graph API**: square grids, hex grids, and territory maps all expose `{ cells, neighbors(id), center(id), passable(id) }`. Use BFS on it for reachability/fortify paths. Validate territory JSON with Zod and check neighbor symmetry. → [boards.md](boards.md)
3. **3D territories**: polygon → `THREE.Shape` → `ExtrudeGeometry` rotated flat; height or stack = unit count; owner = color **plus** pattern/emblem (colorblind-safe); floating `<Html>` badges for counts. → [boards.md](boards.md)
4. **Hidden information**: `redact(state, viewer)` masks opponent ranks as `"unknown"`. Units face their owner's camera. In hotseat with hidden info, a **pass-the-device screen is mandatory** (hide all faces until next player taps Ready). → [boards.md](boards.md)
5. **Dice & odds**: roll with seeded PRNG in `rules/` first, then play a cosmetic dice animation to that face. Show attacker win odds before committing. → [boards.md](boards.md)
6. **Hotseat & camera seats**: on turn change: lock input → finish move animation → rotate camera around board Y to next player's seat (`CameraControls.rotateTo`, 0.6–1.0 s, toggleable, respects `prefers-reduced-motion`) → unlock. Also covers arcade alternating 2P and shared-keyboard 2P. → [hotseat.md](hotseat.md)

## Sibling files
- [boards.md](boards.md) — graph interface, hex/territory math, hidden-info redaction, setup phase, dice & odds
- [hotseat.md](hotseat.md) — camera seats (`SEATS`), turn handoff, alternating arcade 2P, simultaneous 2P
