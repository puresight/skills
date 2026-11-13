# 2D Movement, Collision & Physics

## 1. Decide first: arcade or rigid-body?
Most classic games did NOT use physics engines. Their "feel" comes from simple, deterministic rules. Using Matter.js for Pong or Breakout makes them feel wrong (spin, energy loss, sticky corners).

| Game style | Use |
|---|---|
| Pong, Breakout, Space Invaders, Galaga, Frogger, Centipede | Custom AABB/circle tests, scripted reflection |
| Pac-Man, Dig Dug, Bomberman, maze games | Tile-grid movement + grid lookups |
| Asteroids, Defender | Velocity + wrap-around, circle tests |
| Donkey Kong, Mario-style platformers | Custom tile collision with axis-separated resolution |
| Pinball, Angry-Birds-like, stacking, ragdolls, joints | Rigid-body engine |

## 2. Arcade collision rules
- Move, then resolve **per axis** (move X → resolve X, move Y → resolve Y). Avoids corner snagging.
- Fast small objects (balls, bullets): sub-step the move or sweep (ray/segment vs box) so they cannot tunnel through thin paddles/bricks.
- Pong/Breakout paddle bounce: reflection angle comes from hit offset from paddle center (original behavior), not physical reflection. Clamp angle to avoid near-horizontal balls.
- Keep collision tests in a pure module (`collide.ts`) with unit tests for edge cases.
- Use squared distances for circle checks; broad-phase with a grid or `rbush` above ~30 objects (see `web-game-core/math.md`).
- Integrate with the fixed timestep only (`web-game-core/loop.md`).

## 3. Rigid-body engines (only when needed)
WebContainers (Bolt.new) run Node in the browser with no native compilation (`node-gyp`); pick pure-JS or Wasm packages.
- Lightweight: `matter-js` + `@types/matter-js`.
- Precision / many bodies / determinism: `@dimforge/rapier2d-compat` (Wasm embedded, works offline).

Rapier must be initialized before creating anything:
```typescript
import RAPIER from "@dimforge/rapier2d-compat";
export async function initPhysics() {
  await RAPIER.init();
  const world = new RAPIER.World({ x: 0, y: -9.81 });
  return { RAPIER, world };
}
```

When using an engine:
- Let the engine resolve collisions; don't hand-write velocity reflection for engine bodies.
- Step with the fixed timestep (`world.timestep = FIXED_DT; world.step()` / `Engine.update(engine, 1000/60)`), never variable frame delta.
- Don't teleport bodies in collision callbacks; apply impulses or queue changes for after the step. Teleporting is fine for kinematic bodies and respawns, outside callbacks.
- Use pixels-to-meters scale (e.g. 1 m = 32 px) for Rapier; it is tuned for meters.
