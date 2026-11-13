# Turn-Based 3D Boards & Maps

Background and design principles: `docs/board-to-3d.md`.

## 1. Layering (non-negotiable)
```
rules/      pure TS: state, legal actions, resolve(). No React, no three. Unit-tested.
view/       redact(state, viewer) -> what this player may see
scene/      R3F components render the redacted view only
```
The renderer must never receive information the current viewer shouldn't see.

## 2. Board topology as a graph
Use one adjacency API for every board type so highlighting, pathfinding, and AI are shared:
```typescript
export interface Board<Id extends string> {
  cells: Id[];
  neighbors(id: Id): Id[];
  center(id: Id): [number, number, number];   // world position for pieces/labels
  passable(id: Id): boolean;
}
```
- **Square grid**: id `"c-r"`; neighbors orthogonal (add diagonals only if rules need them). Impassable cells (lakes) → `passable=false` and visible terrain.
- **Hex grid**: axial coords `(q, r)`; world: `x = size * √3 * (q + r/2)`, `z = size * 1.5 * r`. See redblobgames.com/grids/hexagons.
- **Territory graph**: data file of territories, validated with Zod:
```typescript
const Territory = z.object({
  id: z.string(), name: z.string(), region: z.string(),
  polygon: z.array(z.tuple([z.number(), z.number()])).min(3),  // x,z in board units
  centroid: z.tuple([z.number(), z.number()]),
  neighbors: z.array(z.string()),
});
```
  Validate symmetry on load (if A lists B, B lists A).
- Movement through owned territories / reachable cells: BFS on the graph.

## 3. Rendering territories
- Build each region with `THREE.Shape` from the polygon → `ExtrudeGeometry({ depth, bevelEnabled: false })`, rotated −π/2 on X so it lies flat. Memoize geometry per territory.
- Height or token stack encodes troop count; number badge via drei `<Html>` or `<Text>` billboard at centroid.
- Borders: `<Line>` from drei along polygon outline; brighter for selected/attackable.
- Owner = color **and** pattern/emblem (colorblind-safe).
- Procedural maps: Voronoi from seeded points (`d3-delaunay`), then merge cells into territories; neighbors from shared edges.

## 4. Hidden information
- True state holds everything. `redact(state, viewer)` replaces opponents' hidden fields with `"unknown"` but keeps position and owner.
- Render unknown units showing their back/emblem; own units show face. Units physically face their owner's seat.
- Hotseat with hidden info **requires** a handoff screen: on turn end, render everything as unknown, show "Pass to Player 2 — tap when ready", then render the new viewer's redacted view.
- Reveals (combat) are events: both units turn to face the camera, clash animation, then apply the result.
- Track "known to opponent" flags so revealed units stay marked.

## 5. Phases
Typical flow (implement with `web-game-core/state-machine.md`):
`setup (each player, private) → [turn: reinforce → act/attack → move/fortify → end] → victory`.
- Setup: drag units onto allowed rows/territories; offer **Random** and **Preset** buttons; validate before confirm.
- Long games: autosave after every action (`web-game-core/persistence.md`).

## 6. Dice & randomness
- All randomness from a seeded PRNG stored in game state (save/load reproduces results; enables replays). See `web-game-core/math.md`.
- Decide results first, then animate dice to show them (scripted tumble ending on the known face). Never let physics decide outcomes.
- Show odds before committing (e.g. attacker win probability via precomputed table or quick Monte Carlo of 2,000 trials). Teaches players and builds trust.

## 7. Camera
- 2-player: seat per player, rotate on handoff (`web-game-board/hotseat.md`).
- 3+ players on a map: fixed overhead tilted camera with orbit; brief focus moves to battles.
- Orthographic option for strategy maps reduces perspective distortion.

## 8. UI overlays
Turn/phase banner, active player color, action buttons (End phase, Undo where legal), log of events, territory/unit info panel on hover, odds panel, minimap or top view toggle.

## 9. Originality
Use original names, maps, rosters, and art. Check `docs/originality-and-ip.md` before naming anything.
