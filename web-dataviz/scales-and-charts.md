# 2D Scales, Data Transforms & Chart Shells

Keep all data math in `src/viz/` (pure TypeScript, no React or DOM). Renderers consume pre-computed coordinates, ticks, and accessible descriptions.

```bash
pnpm add d3-scale d3-array d3-shape d3-format zod
```

## 1. Pure-TS Scales, LTTB Downsampling & Palettes (`src/viz/scales.ts`)

```typescript
import { scaleLinear, scaleBand, scalePoint } from "d3-scale";
import { extent, max, bin } from "d3-array";
import { format } from "d3-format";

export interface Margin {
  top: number;
  right: number;
  bottom: number;
  left: number;
}

export interface ChartDimensions {
  width: number;
  height: number;
  margin: Margin;
  innerWidth: number;
  innerHeight: number;
}

export function createDimensions(
  width: number,
  height: number,
  margin: Margin = { top: 24, right: 24, bottom: 36, left: 52 },
): ChartDimensions {
  return {
    width,
    height,
    margin,
    innerWidth: Math.max(0, width - margin.left - margin.right),
    innerHeight: Math.max(0, height - margin.top - margin.bottom),
  };
}

/**
 * Perceptually balanced, colorblind-safe (Okabe-Ito / OkLCH tuned) categorical palette
 * paired with SVG dash/pattern identifiers for print & monochrome legibility.
 */
export const VIZ_PALETTE = [
  { id: "cobalt", hex: "#2563EB", darkHex: "#60A5FA", dash: "none", pattern: "pattern-dots" },
  { id: "amber",  hex: "#D97706", darkHex: "#FBBF24", dash: "6 3",  pattern: "pattern-lines" },
  { id: "teal",   hex: "#0D9488", darkHex: "#2DD4BF", dash: "2 2",  pattern: "pattern-cross" },
  { id: "rose",   hex: "#E11D48", darkHex: "#FB7185", dash: "8 3 2 3", pattern: "pattern-diag" },
  { id: "violet", hex: "#7C3AED", darkHex: "#A78BFA", dash: "4 4",  pattern: "pattern-grid" },
  { id: "slate",  hex: "#475569", darkHex: "#94A3B8", dash: "1 3",  pattern: "pattern-solid" },
] as const;

/** Area-proportional circle radius (never scale radius linearly with value). */
export function radiusFromValue(value: number, maxValue: number, maxRadius = 24): number {
  if (value <= 0 || maxValue <= 0) return 0;
  return maxRadius * Math.sqrt(value / maxValue);
}

/**
 * Largest-Triangle-Three-Buckets (LTTB) downsampling for time series.
 * Preserves visual extrema & spikes when reducing thousands of points for mobile/SVG.
 */
export function downsampleLTTB<T>(
  data: readonly T[],
  threshold: number,
  getX: (d: T) => number,
  getY: (d: T) => number,
): T[] {
  const n = data.length;
  if (threshold >= n || threshold < 3) return [...data];

  const sampled: T[] = [data[0]!];
  const every = (n - 2) / (threshold - 2);
  let a = 0;

  for (let i = 0; i < threshold - 2; i++) {
    const avgRangeStart = Math.floor((i + 1) * every) + 1;
    const avgRangeEnd = Math.min(Math.floor((i + 2) * every) + 1, n);
    let avgX = 0;
    let avgY = 0;
    const avgRangeLength = Math.max(1, avgRangeEnd - avgRangeStart);

    for (let j = avgRangeStart; j < avgRangeEnd; j++) {
      avgX += getX(data[j]!);
      avgY += getY(data[j]!);
    }
    avgX /= avgRangeLength;
    avgY /= avgRangeLength;

    const rangeOffs = Math.floor(i * every) + 1;
    const rangeTo = Math.floor((i + 1) * every) + 1;
    const pointAX = getX(data[a]!);
    const pointAY = getY(data[a]!);

    let maxArea = -1;
    let nextA = rangeOffs;

    for (let j = rangeOffs; j < rangeTo; j++) {
      const area = Math.abs(
        (pointAX - avgX) * (getY(data[j]!) - pointAY) -
          (pointAX - getX(data[j]!)) * (avgY - pointAY),
      );
      if (area > maxArea) {
        maxArea = area;
        nextA = j;
      }
    }
    sampled.push(data[nextA]!);
    a = nextA;
  }

  sampled.push(data[n - 1]!);
  return sampled;
}

export const formatCompact = format(".3~s");
export const formatPercent = format(".1%");
export const formatInteger = format(",d");
```

---

## 2. Print & Colorblind-Safe SVG Pattern Definitions (`src/components/charts/SvgPatternDefs.tsx`)

Include `<SvgPatternDefs />` inside your `<svg>` so bar/area fills remain distinguishable in grayscale laser prints and for colorblind readers.

```tsx
export function SvgPatternDefs() {
  return (
    <defs>
      <pattern id="pattern-lines" width="8" height="8" patternUnits="userSpaceOnUse" patternTransform="rotate(45)">
        <line x1="0" y1="0" x2="0" y2="8" stroke="currentColor" strokeWidth="2.5" strokeOpacity="0.35" />
      </pattern>
      <pattern id="pattern-dots" width="8" height="8" patternUnits="userSpaceOnUse">
        <circle cx="3" cy="3" r="1.6" fill="currentColor" fillOpacity="0.4" />
      </pattern>
      <pattern id="pattern-cross" width="8" height="8" patternUnits="userSpaceOnUse">
        <path d="M 0 4 L 8 4 M 4 0 L 4 8" stroke="currentColor" strokeWidth="1.5" strokeOpacity="0.35" />
      </pattern>
      <pattern id="pattern-diag" width="8" height="8" patternUnits="userSpaceOnUse" patternTransform="rotate(-45)">
        <line x1="0" y1="0" x2="0" y2="8" stroke="currentColor" strokeWidth="2" strokeOpacity="0.35" />
      </pattern>
    </defs>
  );
}
```

---

## 3. Accessible, Multi-Device SVG Chart Shell (`src/components/charts/ChartFrame.tsx`)

Automatically adapts typography and margins for `mobile`, `desktop`, `tv`, and `print`, and exposes an accessible `<title>`, `<desc>`, and optional screen-reader summary table.

```tsx
import type { ReactNode } from "react";
import { SvgPatternDefs } from "./SvgPatternDefs";

export interface ChartFrameProps {
  title: string;
  subtitle?: string;
  summary: string; // Screen-reader & print takeaway
  width: number;
  height: number;
  margin: { top: number; right: number; bottom: number; left: number };
  sourceNote?: string;
  actions?: ReactNode; // Ark UI controls slot (hidden in print)
  children: ReactNode;
}

export function ChartFrame({
  title,
  subtitle,
  summary,
  width,
  height,
  margin,
  sourceNote,
  actions,
  children,
}: ChartFrameProps) {
  const titleId = `chart-title-${title.replace(/\W+/g, "-").toLowerCase()}`;
  const descId = `${titleId}-desc`;

  return (
    <figure className="flex flex-col gap-2 rounded-xl border border-slate-200 bg-white p-4 shadow-xs dark:border-slate-800 dark:bg-slate-900 tv:p-8 print:break-inside-avoid print:border-slate-300 print:bg-white print:shadow-none">
      <header className="flex flex-wrap items-start justify-between gap-3">
        <div>
          <h2 className="text-base font-semibold tracking-tight text-slate-900 dark:text-slate-100 tv:text-3xl print:text-black">
            {title}
          </h2>
          {subtitle && (
            <p className="text-xs text-slate-500 dark:text-slate-400 tv:text-xl print:text-slate-700">
              {subtitle}
            </p>
          )}
        </div>
        {actions && <div className="print:hidden">{actions}</div>}
      </header>

      {width > 0 && height > 0 && (
        <svg
          role="graphics-document"
          aria-labelledby={`${titleId} ${descId}`}
          width={width}
          height={height}
          viewBox={`0 0 ${width} ${height}`}
          className="overflow-visible select-none font-sans"
        >
          <title id={titleId}>{title}</title>
          <desc id={descId}>{summary}</desc>
          <SvgPatternDefs />
          <g transform={`translate(${margin.left},${margin.top})`}>{children}</g>
        </svg>
      )}

      {sourceNote && (
        <figcaption className="text-[11px] text-slate-500 dark:text-slate-400 tv:text-lg print:text-slate-700">
          {sourceNote}
        </figcaption>
      )}
    </figure>
  );
}
```

---

## 4. High-Density 2D Canvas Scatterplot with `rbush` Spatial Picking

When rendering $2,000–50,000$ marks, SVG DOM nodes bottleneck layout. Render to a HiDPI `<canvas>` and index screen coordinates in `rbush` for $O(\log N)$ hover/tap inspection.

```typescript
import RBush from "rbush";

export interface IndexedPoint<T> {
  minX: number;
  minY: number;
  maxX: number;
  maxY: number;
  cx: number;
  cy: number;
  r: number;
  datum: T;
}

export function buildSpatialIndex<T>(
  items: readonly T[],
  getX: (d: T) => number,
  getY: (d: T) => number,
  getR: (d: T) => number,
): RBush<IndexedPoint<T>> {
  const tree = new RBush<IndexedPoint<T>>();
  const boxes: IndexedPoint<T>[] = items.map((datum) => {
    const cx = getX(datum);
    const cy = getY(datum);
    const r = Math.max(4, getR(datum)); // Minimum 4px hit radius
    return { minX: cx - r, minY: cy - r, maxX: cx + r, maxY: cy + r, cx, cy, r, datum };
  });
  tree.load(boxes);
  return tree;
}

export function pickNearestPoint<T>(
  tree: RBush<IndexedPoint<T>>,
  x: number,
  y: number,
  touchTolerance = 12,
): IndexedPoint<T> | null {
  const candidates = tree.search({
    minX: x - touchTolerance,
    minY: y - touchTolerance,
    maxX: x + touchTolerance,
    maxY: y + touchTolerance,
  });
  let best: IndexedPoint<T> | null = null;
  let bestDistSq = Infinity;
  for (const c of candidates) {
    const dx = c.cx - x;
    const dy = c.cy - y;
    const d2 = dx * dx + dy * dy;
    if (d2 < bestDistSq) {
      bestDistSq = d2;
      best = c;
    }
  }
  return best;
}
```
