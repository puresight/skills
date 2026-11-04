# Web Game Math & Spatial Invariants

Enforce strict mathematical determinism across all game logic running in the browser and WebContainers.

## 1. Vector & Matrix Operations

- NEVER hand-write arithmetic for vector addition, dot products, cross products, normalization, or matrix multiplication.
- ALWAYS install and import `gl-matrix` for all spatial math:

```bash
pnpm add gl-matrix

```

```typescript
import { vec2, mat2d } from "gl-matrix";
```

- Use destination-passing style to minimize garbage collection pauses:
- Good: `vec2.sub(outVec, targetPos, currentPos);`
- Bad: `const outVec = { x: targetPos.x - currentPos.x, y: targetPos.y - currentPos.y };`

## 2. Distance and Range Checks

- Never calculate Euclidean distance via `Math.sqrt(dx*dx + dy*dy)` inside hot loops if checking thresholds.
- Use squared distance checks (`vec2.sqrDist(a, b) < threshold * threshold`) to eliminate unnecessary square-root computations.

## 3. Angle Calculations and Interpolation

- Always normalize vectors before calling trigonometric operations.
- Use `Math.atan2(dy, dx)` instead of `Math.atan()` for heading angles to avoid division-by-zero on vertical axes.
- Interpolate rotations using `quat.slerp` or angular difference wrapping logic; never linearly interpolate (`lerp`) raw angles across the 0 / 2π boundary.

## 4. Spatial Indexing

- For any scene with more than 30 dynamic or queryable entities, reject nested $O(n^2)$ loops.
- Install `rbush` or `kdbush` to index entity bounding boxes:

```bash
pnpm add rbush

```

- Query candidate collisions via the spatial index broadphase before running precise geometric tests.
