# 3D Spatial, Volumetric & Linked Data Visualization

Pair this reference with `web-game-3d` (for R3F setup, offline lighting, and damping) and `web-game-board/boards.md` (for polygon extrusion and graph topologies).

```bash
pnpm add three @react-three/fiber @react-three/drei maath
pnpm add -D @types/three
```

## 1. Golden Rules for 3D Data Scenes
1. **Earn the 3rd dimension**: use 3D only when $x, y, z$ represent real spatial/topological coordinates (extruded regional maps, terrains, multi-floor buildings, 3D embeddings) or when height encodes a secondary variable alongside a 2D spatial layout.
2. **Perspective ↔ Orthographic toggle**: perspective foreshortens distant bars/prisms. Always wire an Ark UI `SegmentGroup` (`3D Perspective | 3D Ortho | 2D Top-Down`) so analysts can verify exact heights without perspective distortion.
3. **Preserve drawing buffer for print**: pass `gl={{ preserveDrawingBuffer: true }}` and `frameloop="demand"` to `<Canvas>` so static views consume zero idle GPU and print cleanly via `@media print`.
4. **Screen-space labels**: use drei `<Html center distanceFactor={...}>` or `<Text>` billboards for KPI callouts; never extrude 3D text for data labels.

---

## 2. Perspective ↔ Orthographic & Top-Down Camera Rig (`src/scene/DataCameraRig.tsx`)

```tsx
import { CameraControls, OrthographicCamera, PerspectiveCamera } from "@react-three/drei";
import { useEffect, useRef } from "react";
import type CameraControlsImpl from "camera-controls";

export type CameraProjection = "perspective" | "orthographic" | "top-down";

export function DataCameraRig({
  projection,
  focusTarget,
}: {
  projection: CameraProjection;
  focusTarget?: [number, number, number] | null;
}) {
  const controlsRef = useRef<CameraControlsImpl | null>(null);

  useEffect(() => {
    const ctrl = controlsRef.current;
    if (!ctrl) return;
    if (projection === "top-down") {
      void ctrl.setLookAt(0, 18, 0.01, 0, 0, 0, true);
    } else {
      void ctrl.setLookAt(0, 11, 12, 0, 0, 0, true);
    }
  }, [projection]);

  useEffect(() => {
    const ctrl = controlsRef.current;
    if (!ctrl || !focusTarget) return;
    const [tx, ty, tz] = focusTarget;
    void ctrl.setLookAt(tx + 4, ty + 6, tz + 6, tx, ty, tz, true);
  }, [focusTarget]);

  return (
    <>
      {projection === "perspective" ? (
        <PerspectiveCamera makeDefault position={[0, 11, 12]} fov={40} near={0.1} far={200} />
      ) : (
        <OrthographicCamera makeDefault position={[0, 14, 14]} zoom={36} near={-100} far={200} />
      )}
      <CameraControls
        ref={controlsRef}
        makeDefault
        minPolarAngle={0.05}
        maxPolarAngle={projection === "top-down" ? 0.1 : 1.35}
        minDistance={4}
        maxDistance={40}
      />
    </>
  );
}
```

---

## 3. Instanced 3D Scatter / Hex-Spike Cloud (`src/scene/InstancedDataCloud.tsx`)

For $500–50,000$ 3D data points or hex spikes, never mount individual `<mesh>` components. Use a single `<instancedMesh>` with per-instance matrix and color attributes, updating smoothly when filters change.

```tsx
import { useLayoutEffect, useRef } from "react";
import * as THREE from "three";
import { invalidate } from "@react-three/fiber";

export interface DataPoint3D {
  id: string;
  position: [number, number, number];
  scale: [number, number, number];
  colorHex: string;
}

const tempObj = new THREE.Object3D();
const tempColor = new THREE.Color();

export function InstancedDataCloud({
  points,
  selectedId,
  onSelect,
}: {
  points: readonly DataPoint3D[];
  selectedId: string | null;
  onSelect: (id: string) => void;
}) {
  const meshRef = useRef<THREE.InstancedMesh | null>(null);

  useLayoutEffect(() => {
    const mesh = meshRef.current;
    if (!mesh) return;

    points.forEach((pt, i) => {
      tempObj.position.set(pt.position[0], pt.position[1], pt.position[2]);
      const isSelected = pt.id === selectedId;
      const boost = isSelected ? 1.25 : 1.0;
      tempObj.scale.set(pt.scale[0] * boost, pt.scale[1] * boost, pt.scale[2] * boost);
      tempObj.updateMatrix();
      mesh.setMatrixAt(i, tempObj.matrix);

      tempColor.set(isSelected ? "#FBBF24" : pt.colorHex);
      mesh.setColorAt(i, tempColor);
    });

    mesh.instanceMatrix.needsUpdate = true;
    if (mesh.instanceColor) mesh.instanceColor.needsUpdate = true;
    invalidate();
  }, [points, selectedId]);

  return (
    <instancedMesh
      ref={meshRef}
      args={[undefined, undefined, points.length]}
      castShadow
      receiveShadow
      onClick={(e) => {
        e.stopPropagation();
        if (e.instanceId !== undefined && points[e.instanceId]) {
          onSelect(points[e.instanceId]!.id);
        }
      }}
    >
      <boxGeometry args={[1, 1, 1]} />
      <meshStandardMaterial roughness={0.45} metalness={0.1} />
    </instancedMesh>
  );
}
```

---

## 4. Linked 3D Scene + 2D Analytical View Pattern

Always synchronize selection and hover state (`selectedId`, `hoveredId`, `activeFilter`) in a single parent or pure-TS store so clicking a 3D prism/point highlights the exact row in the 2D bar chart / table, and vice versa:

```
[Ark UI Filter Toolbar: Metric SegmentGroup | Cohort Select | Range Slider | Camera Projection]
        │
        ▼
  Pure-TS View State (filteredData, scales, selectedId, cameraProjection)
        ├──► 3D R3F Scene (Extruded regions / Instanced spikes + Screen-space Html badge)
        └──► 2D SVG ChartFrame + Tabular Summary (Exact baseline comparison + Print fallback)
```
