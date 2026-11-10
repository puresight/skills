---
name: web-game-lowpoly-3d
description: "Use when building low-poly, voxel, or blocky 3D games with React Three Fiber. Covers fixed-timestep simulation, GLB loading from Blockbench, simple materials, modular entity state, and offline-first architecture. Applies to Battlezone-style tank arenas, blocky combat, low-poly board games, and voxel mazes. Does not apply to photorealistic, high-poly, or pure 2D Canvas games."
---

# Low-Poly / Blocky 3D Games (React Three Fiber)

Use `@react-three/fiber` + `@react-three/drei` on the React 19 + Vite stack. Keep simulation decoupled from rendering.

## Architecture
- Fixed-timestep accumulator for simulation (update at fixed rate; interpolate for render).
- Drive rendering with `useFrame`. Never put simulation, AI, or collision logic inside the render path.
- Prefer declarative R3F components (`<mesh>`, `<group>`, `useGLTF`).
- Load models as GLB (Blockbench exports). Place in `/public/models/` or import them.
- Fully offline after load. Persist scores, settings, and saves with Dexie.

## Libraries
- `@react-three/fiber`, `@react-three/drei` (useGLTF, OrbitControls, Environment, Html, useAnimations).
- Prefer simple custom collision or lightweight physics for arcade feel. Add `@react-three/rapier` only if rigid-body physics is explicitly needed.
- Reuse `web-game-loop-architecture`, `web-game-math`, and related skills where concepts transfer.

## Style & assets
- Target low-poly, voxel, or blocky aesthetics that match Blockbench cube models.
- Simple materials (`MeshStandardMaterial` or `MeshBasicMaterial`). Avoid heavy post-processing.
- Support basic animations via `useAnimations` or direct bone/matrix updates.
- Favor a small set of reusable models (vehicles, pieces, obstacles, projectiles).

## Structure
- Keep rules, collision, AI, and entity state in plain TypeScript modules outside the React tree when practical.
- Expose a clean simulation object that R3F components observe.
- Make models and constants easy to swap so the project can be refined later in Cursor or Antigravity.

## Constraints
- Stay inside the global stack (TypeScript strict, Dexie, Zod, Tailwind for UI overlays).
- Prefer small focused components and minimal dependencies.
- Deliver a playable core loop first; leave extra models and polish for later iteration.

## Math & geometry
- Always prefer Three.js built-in math (`Vector3`, `Quaternion`, `Matrix4`, `MathUtils`, `Ray`, `Box3`, `Sphere`) over custom implementations.
- For additional helpers (random distributions, buffer ops, extra geometry), use `maath`.
- Do not reimplement vector, quaternion, or matrix math. Reuse `web-game-math` patterns where applicable.
- Keep heavy geometric calculations in plain TypeScript modules outside the React render path.
