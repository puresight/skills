# 2D Physics in WebContainers

Offload collision detection, resolution, and dynamics to vetted libraries compatible with WebContainer sandboxes.

## 1. Library Selection & Compatibility

WebContainers run in-browser Node.js without native C++ compilation (`node-gyp`).

- Default for lightweight/arcade games: `matter-js` (pure TypeScript/JavaScript, no build steps).

```bash
pnpm add matter-js @types/matter-js

```

- Default for precision/high-body-count simulations: `@dimforge/rapier2d-compat` (Wasm-based).

```bash
pnpm add @dimforge/rapier2d-compat

```

## 2. Rapier Wasm Initialization Rule

When using `@dimforge/rapier2d-compat`, you MUST await the Wasm binary initialization before constructing any world, rigid body, or collider:

```typescript
import RAPIER from "@dimforge/rapier2d-compat";

export async function initPhysics() {
  await RAPIER.init();
  const gravity = { x: 0.0, y: -9.81 };
  const world = new RAPIER.World(gravity);
  return { RAPIER, world };
}
```

## 3. Prohibited Patterns

- DO NOT invent custom collision resolution routines (e.g., manually reflecting velocities off circle boundaries with `vx = -vx * elasticity`).
- DO NOT step physics using variable frame time (`delta` from `requestAnimationFrame`).
- DO NOT alter physics body positions directly during collision callbacks; apply forces or impulses instead:
- Good: `body.applyImpulse({ x: 0, y: 15 }, true);`
- Bad: `body.setTranslation({ x: body.translation().x, y: body.translation().y + 10 });`
