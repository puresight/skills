# 3D Asset Pipeline (R3F)

## 1. Format rules
- **Ship GLB only.** One binary file per model or one file containing the whole set.
- If a pack provides FBX/OBJ/Blend only, convert before use (the user may need to do this locally and upload the GLB):
  - Blender: File → Import → (FBX/OBJ) → File → Export → glTF 2.0 → Format "glTF Binary (.glb)", +Y Up, Apply Modifiers.
  - Online alternatives: gltf.report, or `npx fbx2gltf` (native binary — may not run inside WebContainers/Bolt).
- Loaders for FBX/OBJ (`FBXLoader`, `OBJLoader`) exist but are a fallback only: larger, slower, inconsistent materials.
- Avoid Draco/Meshopt compression unless the decoder is served locally (offline-first). Low-poly files are small anyway.
- Put files in `/public/models/` and reference as `/models/name.glb`. Never hotlink an asset store URL.

## 2. Inspect before coding
Do not guess node names. Ask the user for (or derive with a quick script) the scene graph:
```typescript
const { nodes, materials } = useGLTF("/models/chess.glb");
console.table(Object.keys(nodes)); console.table(Object.keys(materials));
```
Or generate a typed component: `npx gltfjsx public/models/chess.glb --types --output src/models/Chess.tsx`. Use the generated `nodes.X.geometry` references.

## 3. A set of many pieces in one file (e.g. chess sets)
- Extract each piece once by node name, then render many instances sharing the **same geometry**:
```tsx
const { nodes } = useGLTF("/models/chess.glb") as unknown as { nodes: Record<string, THREE.Mesh> };
const PIECE_NODE: Record<PieceSymbol, string> = { p: "Pawn", r: "Rook", n: "Knight", b: "Bishop", q: "Queen", k: "King" }; // adjust to real names
<mesh geometry={nodes[PIECE_NODE[type]].geometry} material={color === "w" ? whiteMat : blackMat} castShadow />
```
- If the file has separate white/black meshes, map both; otherwise recolor with two shared materials created once with `useMemo`.
- Knights must face the opponent: rotate black knights by π around Y (check the model's default facing first).
- Board: use the pack's board mesh if present and compute square size from its bounding box; otherwise build squares from 64 instanced boxes.
- Checkers pieces from the same pack reuse this exact pattern.

## 4. Normalize scale, pivot, and orientation
Asset packs rarely match your units. Normalize once in code, in one place:
```typescript
const box = new THREE.Box3().setFromObject(scene);
const size = box.getSize(new THREE.Vector3());
const center = box.getCenter(new THREE.Vector3());
// scale so the board is 8 units wide, then put its top surface at y=0
```
- For pieces: set pivot to bottom-center (`geometry.translate(-cx, -minY, -cz)` on a **clone** of the geometry) so they sit on squares at y = board top.
- Fix up-axis (Z-up exports appear lying down): rotate −π/2 on X once.

## 5. Low-poly look
- Use `flatShading: true` on `MeshStandardMaterial` for faceted style; keep roughness high (0.6–0.9), metalness 0–0.2.
- 2–4 materials total. Prefer vertex colors or a tiny palette texture with `NearestFilter`.

## 6. Performance & hygiene
- `useGLTF.preload(path)` at module level; wrap scene in `<Suspense>` with a loading screen (drei `useProgress`).
- Share geometries/materials; for >100 identical objects use `<Instances>` from drei.
- Clone with `SkeletonUtils.clone` only for skinned/animated models; static meshes can share geometry directly.

## 7. Licensing
- Record each asset's source URL, author, and license in `CREDITS.md` and an in-game Credits screen.
- Paid/itch.io packs usually forbid redistributing raw files: keep them out of public repos (add to `.gitignore` or a private repo) and ask the user before committing.
- Do not use ripped original arcade/console assets or trademarked logos; recreate the style.
