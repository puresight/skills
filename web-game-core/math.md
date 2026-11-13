# Web Game Math & Spatial Invariants

Enforce deterministic, allocation-light math. Never hand-write vector, matrix, or quaternion arithmetic.

## Preferred math source
- When the project already uses Three.js / React Three Fiber: prefer `THREE.Vector2/3/4`, `THREE.Quaternion`, `THREE.Matrix3/4`, `THREE.MathUtils`, `THREE.Ray`, `THREE.Box3`, `THREE.Sphere`, and related classes.
- Otherwise (or for pure 2D Canvas games): use `gl-matrix` with destination-passing style.
- Optional helpers: `maath` for extra distributions, buffers, or geometry utilities when working with Three.
- Never reimplement addition, subtraction, dot, cross, normalize, multiply, or slerp.

## gl-matrix usage (when chosen)
```bash
pnpm add gl-matrix
```
```typescript
import { vec2, vec3, mat2d, quat } from "gl-matrix";
```
- Always pass a destination: `vec2.sub(out, a, b)` — never allocate new objects in hot paths.

## Distance & range
- Prefer squared distance (`vec2.sqrDist` / `vec3.sqrLen` / equivalent) for threshold checks.
- Avoid `Math.sqrt` inside loops unless the actual distance value is required.

## Angles & rotation
- Normalize vectors before trig.
- Use `Math.atan2(dy, dx)` for headings.
- Interpolate rotations with quaternion slerp (`quat.slerp` or `THREE.Quaternion.slerp`) or proper angular wrapping. Never lerp raw angles across the 0/2π boundary.

## Spatial indexing
- For scenes with more than ~30 dynamic or queryable entities, avoid O(n²) nested loops.
- Use `rbush` (or `kdbush`) for broad-phase bounding-box queries, then run precise tests only on candidates.
```bash
pnpm add rbush
```

## General rules
- Keep heavy math in plain TypeScript modules outside the React render path.
- Reuse results and temporary vectors across frames when possible.
- Stay consistent within a project: pick Three math or gl-matrix and stick to it.

## Deterministic randomness
- Never use `Math.random()` for gameplay that should be reproducible (attract-mode demos, tests, level layouts). Use a seeded PRNG:
```typescript
export function mulberry32(seed: number) {
  return () => { seed |= 0; seed = (seed + 0x6d2b79f5) | 0; let t = Math.imul(seed ^ (seed >>> 15), 1 | seed); t = (t + Math.imul(t ^ (t >>> 7), 61 | t)) ^ t; return ((t ^ (t >>> 14)) >>> 0) / 4294967296; };
}
```

## Grids & wrapping
- Tile index: `i = row * cols + col`; tile from position: `Math.floor(x / TILE)`.
- Screen wrap (Asteroids, Pac-Man tunnel): `((x % W) + W) % W` — plain `%` returns negatives.
- 8-way joystick headings: snap `atan2` to 45° steps; normalize diagonal movement so it isn't faster (unless the original wasn't).
