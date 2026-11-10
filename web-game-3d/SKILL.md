---
name: web-game-3d
description: "Use when building 3D games or interactive 3D scenes with React Three Fiber / Three.js. Covers fixed-timestep simulation, model loading (GLB/glTF), camera controls, simple animations, lighting, and offline-first architecture. Applies to chess, board games, arena combat, strategy titles, and general 3D experiences. Does not apply to pure 2D Canvas games or strictly low-poly/voxel-only pipelines."
---

# 3D Web Games (React Three Fiber)

Use `@react-three/fiber` + `@react-three/drei` on the React 19 + Vite stack. Keep simulation decoupled from rendering.

## Architecture
- Fixed-timestep accumulator for any simulation that needs consistent timing. Interpolate for rendering.
- Drive the scene with `useFrame`. Keep rules, AI, and heavy logic in plain TypeScript modules outside the React tree.
- Load models as GLB/glTF via `useGLTF`. Place them in `/public/models/` or import them.
- Fully offline after load. Persist state with Dexie.

## Camera & interaction
- Default to `@react-three/drei` `OrbitControls` for board and tabletop games so the user can freely orbit, pan, and zoom.
- Limit polar angle and distance when appropriate (e.g. prevent camera from going under the board or too far away).
- For turn-based games, consider temporarily disabling controls or damping during piece animations.
- Use pointer raycasting (`useThree` + `Raycaster` or drei helpers) for selecting pieces and squares. Prefer object picking on named meshes or groups.
- Support both mouse and touch. Keep interaction logic outside the render loop.

## Animation
- Prefer simple, state-driven interpolation over complex timelines for piece moves: lerp position and slerp quaternion (or use `damp` from `maath` / `MathUtils`).
- Drive animations from game-state changes (move started, capture, etc.). Do not put animation logic inside `useFrame` beyond reading the current interpolated transform.
- Common patterns: lift piece up, slide horizontally, lower; or direct slide with slight arc. Keep duration short (0.2–0.6 s).
- Use `useAnimations` only when the GLB already contains authored clips. Otherwise stick to code-driven transforms.
- Ensure animations are interruptible and that the final resting transform matches the logical board state.

## Lighting
- Start with a simple, performant setup: one directional light (sun) + ambient light + optional soft shadows on the board.
- Enable shadows only on the board and key pieces; avoid shadow-casting on every small object.
- Prefer `MeshStandardMaterial` or `MeshPhysicalMaterial` so pieces respond to light. Keep material counts low.
- For board games, a slightly warm or neutral key light from above-and-to-the-side usually looks better than flat lighting.
- Avoid heavy post-processing unless explicitly requested.

## Libraries & math
- `@react-three/fiber`, `@react-three/drei`.
- Prefer Three.js native math (`Vector3`, `Quaternion`, `Matrix4`, `MathUtils`, `Ray`, `Box3`). Reuse the `web-game-math` skill.
- Optional: `maath` for damping and extra helpers.
- Add physics (e.g. Rapier) only when rigid-body behavior is explicitly required.

## Structure
- Expose a clean simulation or board-state object that R3F components observe.
- Keep models, materials, lighting, and constants easy to swap.
- Deliver a playable core loop first.

## Constraints
- Stay within the global stack (TypeScript strict, Dexie, Zod, Tailwind for UI overlays).
- Prefer small focused components and minimal dependencies.
