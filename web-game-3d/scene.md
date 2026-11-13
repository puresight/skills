# 3D Web Games (React Three Fiber)

Stack: React 19 + Vite + TypeScript strict, `three`, `@react-three/fiber` v9, `@react-three/drei`. Rules and simulation live in plain TS modules; R3F only renders them.

```bash
pnpm add three @react-three/fiber @react-three/drei maath
pnpm add -D @types/three
```

## 1. Canvas setup
```tsx
<Canvas shadows dpr={[1, 2]} camera={{ position: [0, 9, 9], fov: 45 }}
        frameloop={isTurnBased ? "demand" : "always"}>
  <Suspense fallback={null}><Scene /></Suspense>
</Canvas>
<Loader />  {/* drei loading bar, outside Canvas */}
```
- Turn-based games: `frameloop="demand"` saves battery; call `invalidate()` while animations run (controls with damping invalidate automatically).
- Canvas fills its parent; give the parent explicit size (`h-dvh w-full`). HUD/menus are Tailwind DOM overlays on top, not 3D text.

## 2. Camera & controls
- Board/tabletop: drei `CameraControls` (smooth programmatic moves like `rotateTo`, good for hotseat seat changes) or `OrbitControls` (`enableDamping`, `makeDefault`).
- Clamp: `minPolarAngle ≈ 0.2`, `maxPolarAngle ≈ 1.35` (never below the board), `minDistance`/`maxDistance`, disable pan or bound it.
- Disable controls during scripted camera moves; re-enable after.
- Action games: custom follow/first-person camera updated in `useFrame` from sim state.

## 3. Picking
- Use mesh pointer events (`onPointerDown`, `onPointerOver`, `onPointerOut`) with `e.stopPropagation()`.
- Distinguish click vs orbit-drag (pointer moved < 5 px).
- Board games: clicks resolve to a **square**, whether the user hit a piece or the board. See `web-game-core/input.md`.

## 4. Animation
- State-driven: game state says where a piece should be; a component damps its mesh toward that target in `useFrame` (`easing.damp3` from `maath/easing`) or runs a short tween (0.25–0.5 s; lift → slide → drop or a parabolic arc).
- Knights may hop in an arc; captured pieces sink/fade then move to a side tray.
- On completion, emit "animation done" so the state machine unlocks input. Final transform must equal the logical square exactly.
- Use `useAnimations` only for clips authored in the GLB.

## 5. Lighting & materials
- `ambientLight` (≈0.4) + one `directionalLight` (castShadow, shadow-mapSize 2048, tight shadow camera bounds around the board) + optional `hemisphereLight`.
- Board `receiveShadow`, pieces `castShadow`. drei `<ContactShadows>` is a cheap, pretty alternative.
- drei `<Environment preset="...">` loads HDRs from a CDN — not offline-safe. Bundle a local `.hdr` in `/public` or skip it.
- Selection/legal-move feedback: emissive tint or a flat ring/disc slightly above the square (`polygonOffset` or y+0.01 to avoid z-fighting). drei `<Outlines>` for hover.

## 6. Performance
- Share geometries/materials; use drei `<Instances>` for many identical objects.
- No object allocation in `useFrame` (reuse temp `Vector3`s at module scope).
- R3F disposes objects it creates on unmount; dispose manually anything created with `new` outside JSX.
- Add `<Perf>`/`r3f-perf` only in dev.

## 7. Libraries & math
- Three.js math classes (`Vector3`, `Quaternion`, `MathUtils`, `Box3`, `Ray`); see `web-game-core/math.md`. `maath` for damping.
- Physics (`@react-three/rapier`) only when rigid bodies are truly needed; board games never need it.

## 8. Structure
```
src/game/      rules, state machine, sim (no React, no three)
src/scene/     Board.tsx, Pieces.tsx, CameraRig.tsx, Lights.tsx
src/ui/        HUD, menus, dialogs (Tailwind)
src/db.ts      Dexie
public/models/ GLB files
```
Deliver a playable core loop first; polish after.

## Related
`web-game-3d/style.md`, `web-game-3d/assets.md`, `web-game-chess`, `web-game-board/hotseat.md`, `web-game-core/state-machine.md`, `web-game-core/loop.md`.
