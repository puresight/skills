---
name: web-game-3d
description: "Use when building any 3D game or 3D board/map scene with React Three Fiber and Three.js: Canvas setup, CameraControls/OrbitControls, pointer picking, state-driven animations, lighting/shadows, low-poly/voxel/vector-wireframe styling, and importing GLB models (Blockbench, itch.io packs)."
---

# 3D Web Games (React Three Fiber, Style & Assets)

Pair with `web-game-core`. For turn-based boards/maps also load `web-game-board` (and `web-game-chess` for chess).

```bash
pnpm add three @react-three/fiber @react-three/drei maath
pnpm add -D @types/three
```

## Rules at a glance
1. **Canvas**: `<Canvas shadows dpr={[1, 2]} frameloop={isTurnBased ? "demand" : "always"}>`. For `"demand"`, call `invalidate()` while animations run. HUD/menus are Tailwind DOM overlays outside `<Canvas>`. → [scene.md](scene.md)
2. **Camera & picking**: drei `CameraControls` (smooth `rotateTo` for hotseat seats) or `OrbitControls`; clamp polar angle so the camera never goes under the board (`0.2..1.35`). Use mesh `onPointerDown`/`onPointerOver` with `e.stopPropagation()` and ignore drags (>5 px move). → [scene.md](scene.md)
3. **Animation & perf**: state-driven damping (`maath/easing` `damp3` in `useFrame`); emit completion to unlock the state machine. Mutate `ref.current.position`, never `setState` in `useFrame`, and never allocate vectors inside `useFrame`. → [scene.md](scene.md)
4. **Offline lighting & style**: ambient + one shadow-casting directional light or `<ContactShadows>`. **Do not** use `<Environment preset="...">` (fetches CDN HDRs, breaks offline). Low-poly: `MeshStandardMaterial` with `flatShading`, small palette in `palette.ts`. Vector wireframe: `EdgesGeometry` + `LineSegments` on black. → [style.md](style.md)
5. **Assets**: ship `.glb` in `/public/models/`. Inspect `Object.keys(nodes)` or run `npx gltfjsx` — never guess node names. Normalize scale/pivot once (`Box3`), share geometries/materials across piece instances, record licenses in `CREDITS.md`, and keep paid packs out of public repos. → [assets.md](assets.md)

## Sibling files
- [scene.md](scene.md) — Canvas setup, controls, picking, animation, lighting, folder structure
- [style.md](style.md) — low-poly materials, procedural primitive fallbacks, retro vector wireframe
- [assets.md](assets.md) — GLB conversion, node inspection, multi-piece sets, scale/pivot normalization, licensing
