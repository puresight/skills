---
name: web-dataviz
description: "Use when building 2D or 3D data visualizations, interactive dashboards, storytelling reports, or spatial/scenario models in websites: chart selection from first principles, pure-TS scales & data transforms (src/viz/), responsive SVG/Canvas/R3F rendering, Ark UI (@ark-ui/react) interactive controls, and multi-device adaptation across mobile phones, desktop, 10-foot televisions/wallboards, and print/PDF."
---

# Web Data Visualization (2D, 3D, Ark UI & Multi-Device)

For deep theory and taxonomy, see `docs/dataviz-first-principles.md`. Pair with `web-game-3d` (and `web-game-board`) when building 3D spatial maps, extruded regions, terrains, or digital twins.

```bash
pnpm add @ark-ui/react d3-scale d3-array d3-shape d3-format lucide-react
```

## Rules at a glance
1. **Pure-TS data & scale core (`src/viz/`)**: keep Zod schemas, aggregations, binning, LTTB downsampling, color palettes, and scale projections in `src/viz/` (zero React/DOM/Three imports). Renderers only project `src/viz/` output and emit filter/selection actions. → [scales-and-charts.md](scales-and-charts.md)
2. **First-principles encoding & renderer choice**:
   - Map primary magnitude to **aligned position/length** (bars must start at `0`; size bubbles by **area** $r \propto \sqrt{v}$, never radius).
   - Use **SVG + HTML** up to ~2,000 marks (crisp vector text, accessible, print-ready), **Canvas 2D** for $2\text{k}–50\text{k}$ points (with `rbush`/quadtree hit-testing), and **R3F (`<Instances>` / `ExtrudeGeometry`)** for true 3D spatial/volumetric data or linked 3D+2D views.
   - Always offer an **Orthographic / Top-Down toggle** on 3D data scenes to eliminate perspective distortion, and pair color with pattern/shape/labels (OkLCH colorblind-safe palettes). → [scales-and-charts.md](scales-and-charts.md), [spatial-3d-dataviz.md](spatial-3d-dataviz.md)
3. **Ark UI (`@ark-ui/react`) control plane**: use headless Ark UI primitives (`SegmentGroup`, `Select`, `Slider`, `Tabs`, `Switch`, `Popover`, `Tooltip`, `Dialog`, `Splitter`) styled with Tailwind CSS for all chart filters, metric selectors, time-range scrubbers, and drill-down drawers. Never build ad-hoc unaccessible dropdowns or sliders. → [ark-ui-controls.md](ark-ui-controls.md)
4. **Device continuum (Mobile → Desktop → TV → Print)**:
   - **Mobile (`<640px`)**: `ResizeObserver`-driven width, horizontal bars instead of rotated labels, touch-scrub crosshairs, and Ark UI `Dialog` bottom sheets for filters.
   - **TV / 10-foot wallboard (`16:9` fullscreen)**: zero scrollbars (`h-dvh overflow-hidden`), minimum `24px` axis text / `36px+` KPIs, D-pad arrow-key focus ring navigation, and an auto-cycling story/camera tour (pausable) with direct data labels (never rely on hover).
   - **Print / PDF (`@media print`)**: force light theme, hide interactive chrome (`print:hidden`), expand scroll containers, render SVG pattern fills/dashes alongside color, and inline all critical annotations so the static page is 100% self-explanatory. → [responsive-devices.md](responsive-devices.md)
5. **Offline & persistence**: bundle all fonts, GeoJSON/TopoJSON, and datasets locally in `/public/` or `src/data/`. Persist saved filter views, custom thresholds, and scenario branches in Dexie + Zod (`src/db.ts`).

## Sibling files (open only what you need, and copy code blocks verbatim)
- [scales-and-charts.md](scales-and-charts.md) — pure-TS scale/binning/LTTB helpers (`src/viz/`), responsive SVG chart shell, OkLCH & SVG pattern palettes, Canvas high-density scatter
- [ark-ui-controls.md](ark-ui-controls.md) — copy-ready `@ark-ui/react` filter bar, metric `SegmentGroup`, range `Slider` scrubber, accessible `Tooltip`/`Popover` inspector, and `Dialog` drill-down
- [responsive-devices.md](responsive-devices.md) — `useDisplayMode` (`mobile | desktop | tv | print`), `ResizeObserver` hook, 10-foot TV auto-tour & D-pad navigation, and `@media print` / SVG export stylesheet
- [spatial-3d-dataviz.md](spatial-3d-dataviz.md) — 3D prism maps, surface heightfields, instanced 3D scatterclouds, perspective ↔ orthographic camera rig, and linked 3D+2D selection
