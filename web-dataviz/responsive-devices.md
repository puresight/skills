# Responsive Display Modes: Mobile, Desktop, Television & Print

A web visualization must adapt not just its pixel width, but its **information density, typography, interaction grammar, and ink profile** across four physical media.

---

## 1. Display Mode Hook & Element Size Observer (`src/viz/useDisplayMode.ts`)

```typescript
import { useEffect, useState, useRef } from "react";

export type DisplayMode = "mobile" | "desktop" | "tv" | "print";

/**
 * Resolves the active display medium:
 * - "print": active during window.print() / @media print
 * - "tv": explicit ?mode=tv query param, or large 16:9 screen (>= 1920px) with coarse/no pointer or fullscreen wallboard toggle
 * - "mobile": viewport < 640px
 * - "desktop": default interactive workspace
 */
export function useDisplayMode(forcedMode?: DisplayMode): DisplayMode {
  const [mode, setMode] = useState<DisplayMode>(() => forcedMode ?? detectMode());

  useEffect(() => {
    if (forcedMode) {
      setMode(forcedMode);
      return;
    }
    const update = () => setMode(detectMode());
    const printMql = window.matchMedia("print");
    window.addEventListener("resize", update);
    printMql.addEventListener("change", update);
    return () => {
      window.removeEventListener("resize", update);
      printMql.removeEventListener("change", update);
    };
  }, [forcedMode]);

  return mode;
}

function detectMode(): DisplayMode {
  if (typeof window === "undefined") return "desktop";
  if (window.matchMedia("print").matches) return "print";
  const params = new URLSearchParams(window.location.search);
  const paramMode = params.get("mode");
  if (paramMode === "tv" || paramMode === "print" || paramMode === "mobile") {
    return paramMode;
  }
  if (window.innerWidth < 640) return "mobile";
  if (window.innerWidth >= 1920 && window.matchMedia("(pointer: coarse), (hover: none)").matches) {
    return "tv";
  }
  return "desktop";
}

/** Measure a chart container's exact pixel box via ResizeObserver. */
export function useChartSize<T extends HTMLElement = HTMLDivElement>(defaultHeight = 320) {
  const ref = useRef<T | null>(null);
  const [size, setSize] = useState({ width: 640, height: defaultHeight });

  useEffect(() => {
    const el = ref.current;
    if (!el) return;
    const ro = new ResizeObserver((entries) => {
      const box = entries[0]?.contentRect;
      if (box && box.width > 0) {
        setSize({
          width: Math.floor(box.width),
          height: Math.floor(box.height || defaultHeight),
        });
      }
    });
    ro.observe(el);
    return () => ro.disconnect();
  }, [defaultHeight]);

  return { ref, width: size.width, height: size.height };
}
```

---

## 2. Mode-Specific Density & Scale Presets (`src/viz/modePresets.ts`)

Pass the resolved `DisplayMode` into your pure-TS layout helper so tick counts, font sizes, and LTTB downsampling thresholds adjust automatically:

```typescript
import type { DisplayMode } from "./useDisplayMode";

export interface ModePreset {
  maxTicksX: number;
  maxTicksY: number;
  maxSeriesPoints: number;
  baseFontPx: number;
  strokeWidthPx: number;
  showDirectLabels: boolean;
  showPatterns: boolean;
}

export const MODE_PRESETS: Record<DisplayMode, ModePreset> = {
  mobile: {
    maxTicksX: 4,
    maxTicksY: 4,
    maxSeriesPoints: 120,
    baseFontPx: 12,
    strokeWidthPx: 2,
    showDirectLabels: true, // Avoid tiny legends on narrow screens
    showPatterns: false,
  },
  desktop: {
    maxTicksX: 10,
    maxTicksY: 7,
    maxSeriesPoints: 600,
    baseFontPx: 12,
    strokeWidthPx: 2,
    showDirectLabels: false,
    showPatterns: false,
  },
  tv: {
    maxTicksX: 6,
    maxTicksY: 5,
    maxSeriesPoints: 240,
    baseFontPx: 24, // 10-foot viewing distance legibility
    strokeWidthPx: 4,
    showDirectLabels: true, // Wallboards have no hover cursor
    showPatterns: false,
  },
  print: {
    maxTicksX: 8,
    maxTicksY: 6,
    maxSeriesPoints: 1000,
    baseFontPx: 11,
    strokeWidthPx: 1.75,
    showDirectLabels: true, // Paper has no tooltips
    showPatterns: true,     // Distinguish series in B&W laser print
  },
};
```

---

## 3. Television / 10-Foot Wallboard Mode: Auto-Tour & D-Pad Navigation

On a conference room TV or operations wallboard:
1. Lock the layout to `h-dvh w-screen overflow-hidden bg-slate-950 text-slate-50` (`16:9` bento grid, max 4–6 cards).
2. Cycle through story scenes, KPIs, or 3D camera seats on a fixed timer (`15–30 s`), pausing immediately when the user presses `Space`, `Enter`, or D-pad arrow keys.
3. Highlight the active card or data point with a high-visibility focus ring (`outline-4 outline-amber-400`).

```typescript
import { useEffect, useState } from "react";

export function useTvAutoTour(sceneCount: number, dwellMs = 18000, enabled = true) {
  const [activeIndex, setActiveIndex] = useState(0);
  const [paused, setPaused] = useState(false);

  useEffect(() => {
    if (!enabled || paused || sceneCount <= 1) return;
    const id = window.setInterval(() => {
      setActiveIndex((i) => (i + 1) % sceneCount);
    }, dwellMs);
    return () => window.clearInterval(id);
  }, [enabled, paused, sceneCount, dwellMs]);

  useEffect(() => {
    if (!enabled) return;
    const onKeyDown = (e: KeyboardEvent) => {
      if (e.code === "ArrowRight") {
        setPaused(true);
        setActiveIndex((i) => (i + 1) % sceneCount);
      } else if (e.code === "ArrowLeft") {
        setPaused(true);
        setActiveIndex((i) => (i - 1 + sceneCount) % sceneCount);
      } else if (e.code === "Space") {
        e.preventDefault();
        setPaused((p) => !p);
      }
    };
    window.addEventListener("keydown", onKeyDown);
    return () => window.removeEventListener("keydown", onKeyDown);
  }, [enabled, sceneCount]);

  return { activeIndex, setActiveIndex, paused, setPaused };
}
```

---

## 4. Print Stylesheet & High-DPI / Vector Export (`src/index.css`)

Add these print rules to your global stylesheet so `Ctrl+P` / `window.print()` produces a publication-grade multi-page brief:

```css
@custom-variant tv (&:where([data-display-mode="tv"], [data-display-mode="tv"] *));

@media print {
  @page {
    size: A4 portrait;
    margin: 14mm;
  }

  html, body {
    background: #ffffff !important;
    color: #0f172a !important;
    font-size: 10pt;
    -webkit-print-color-adjust: exact;
    print-color-adjust: exact;
  }

  /* Expand any scrollable dashboard containers so nothing is clipped */
  .overflow-auto, .overflow-y-auto, .overflow-hidden, .h-dvh {
    overflow: visible !important;
    height: auto !important;
    max-height: none !important;
  }

  /* Keep each chart figure together with its title and caption */
  figure {
    break-inside: avoid;
    page-break-inside: avoid;
    margin-bottom: 12mm;
  }
}
```

> **3D WebGL / R3F in Print**: When printing a page containing an R3F `<Canvas>`, set `gl={{ preserveDrawingBuffer: true }}` on `<Canvas>` so the browser print compositor (and `canvas.toDataURL("image/png")`) captures the rendered frame instead of a blank buffer.
