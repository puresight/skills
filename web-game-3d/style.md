# Low-Poly / Voxel / Vector 3D Style

General R3F architecture (Canvas, controls, picking, loop) lives in `web-game-3d`. This skill covers the **look**.

## 1. Materials
- `MeshStandardMaterial` with `flatShading` for faceted geometry; roughness 0.6–0.9, metalness 0–0.2. `MeshLambertMaterial` is cheaper and fine for low-poly.
- Small palette (4–8 colors) defined in one `palette.ts`. Pieces: two materials (light/dark side). Board: two square colors + frame.
- Prefer vertex colors or a 16×16 palette texture (`NearestFilter`, `colorSpace = SRGBColorSpace`) over many materials.
- Toon look: `MeshToonMaterial` with a 3-step gradient map (NearestFilter).

## 2. Geometry
- Imported models: see `web-game-3d/assets.md` (GLB, normalize scale/pivot, share geometry).
- No models? Build from primitives with low segment counts: `cylinderGeometry args={[r, r, h, 8]}`, `coneGeometry` with 6–8 radial segments, `icosahedronGeometry detail={0}`. A full chess set can be made from stacked low-segment lathe/cylinder parts as a placeholder.
- Voxel/Blockbench: boxes with `NearestFilter` textures; merge static voxels (`BufferGeometryUtils.mergeGeometries`) or use `<Instances>`.

## 3. Lighting & atmosphere
- Strong single directional key light + soft ambient/hemisphere fill gives clean faceted shading.
- Solid or gradient background color (`<color attach="background" />`) and optional `<fog>` matching it.
- Soft shadows: drei `<ContactShadows>` or `<SoftShadows>`; keep the shadow map small.
- Post-processing (bloom, outline) only if requested; `@react-three/postprocessing` costs performance on mobile.

## 4. Retro vector look (Battlezone, Red Baron, Tempest, Star Wars)
- Black background, `LineSegments` with `EdgesGeometry` (threshold ~20°) and `LineBasicMaterial` in green/cyan/white.
- Hidden-line removal: render the mesh in black with `polygonOffset`, then edges on top.
- Glow: additive blending, or `@react-three/postprocessing` `<Bloom>` if allowed.
- Lines are always 1 px in WebGL; for thick lines use drei `<Line lineWidth>`.

## 5. Constraints
- Fully offline: no CDN HDRs/fonts/textures; bundle everything in `/public`.
- Keep model/material/palette constants swappable so art can be iterated later in Cursor or Antigravity.
- Playable first; art polish second.
