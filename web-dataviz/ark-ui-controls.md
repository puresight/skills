# Ark UI (`@ark-ui/react`) Controls for Data Visualization

**Ark UI** (`@ark-ui/react`) is our baseline headless UI component library. Powered by Zag.js state machines, it provides WAI-ARIA compliance, keyboard/D-pad navigation, and touch support out of the box while letting us style every data-part with Tailwind CSS.

```bash
pnpm add @ark-ui/react lucide-react
```

## 1. Which Ark UI Primitive to Use

| DataViz Need | Ark UI Component | Why It Fits |
|---|---|---|
| Switching metric or 2D/3D camera projection (`Linear \| Log`, `2D \| 3D \| Ortho`) | `SegmentGroup` | Single-tap / arrow-key radio group with animated indicator (`[data-part="indicator"]`) |
| Filtering dimensions or cohorts | `Select` or `Combobox` | Full keyboard typeahead, multi-select support, portal positioning |
| Time-window or threshold scrubbing | `Slider` | Dual-thumb range (`value={[min, max]}`) with keyboard step increments |
| Comparing views or dashboard sections | `Tabs` | Lazy mount (`lazyMount`, `unmountOnExit`) for heavy 3D/Canvas tabs |
| Layer visibility (`Show Confidence Band`, `Normalize Per Capita`) | `Switch` / `Checkbox` | Accessible toggle state (`data-state="checked"`) |
| Data point inspection on focus/click | `Popover` / `Tooltip` | Collision-aware floating placement via Floating UI |
| Mobile filter drawer or deep drill-down table | `Dialog` | Focus trap, `Escape` dismissal, backdrop blur |
| Side-by-side 3D Map + 2D Table layout | `Splitter` | Draggable + keyboard-resizable split panes |

---

## 2. Copy-Ready Metric & View Switcher (`SegmentGroup`)

```tsx
import { SegmentGroup } from "@ark-ui/react/segment-group";

export interface MetricSwitcherProps<T extends string> {
  label: string;
  value: T;
  options: readonly { value: T; label: string }[];
  onChange: (next: T) => void;
}

export function MetricSwitcher<T extends string>({
  label,
  value,
  options,
  onChange,
}: MetricSwitcherProps<T>) {
  return (
    <SegmentGroup.Root
      value={value}
      onValueChange={(details) => {
        if (details.value) onChange(details.value as T);
      }}
      orientation="horizontal"
      className="inline-flex items-center gap-1 rounded-lg bg-slate-100 p-1 dark:bg-slate-800 print:hidden"
    >
      <SegmentGroup.Label className="sr-only">{label}</SegmentGroup.Label>
      <SegmentGroup.Indicator className="rounded-md bg-white shadow-xs transition-all duration-150 dark:bg-slate-700" />
      {options.map((opt) => (
        <SegmentGroup.Item
          key={opt.value}
          value={opt.value}
          className="relative z-10 cursor-pointer rounded-md px-3 py-1 text-xs font-medium text-slate-600 transition-colors data-[state=checked]:text-slate-900 focus-visible:outline-2 focus-visible:outline-blue-500 dark:text-slate-400 dark:data-[state=checked]:text-white tv:px-5 tv:py-2 tv:text-xl"
        >
          <SegmentGroup.ItemText>{opt.label}</SegmentGroup.ItemText>
          <SegmentGroup.ItemControl />
          <SegmentGroup.ItemHiddenInput />
        </SegmentGroup.Item>
      ))}
    </SegmentGroup.Root>
  );
}
```

---

## 3. Copy-Ready Dual-Thumb Range Scrubber (`Slider`)

Ideal for filtering time ranges (e.g. years `2015–2026`), value thresholds, or 3D slicing planes.

```tsx
import { Slider } from "@ark-ui/react/slider";

export interface RangeScrubberProps {
  label: string;
  min: number;
  max: number;
  step?: number;
  value: [number, number];
  formatValue?: (v: number) => string;
  onChange: (next: [number, number]) => void;
}

export function RangeScrubber({
  label,
  min,
  max,
  step = 1,
  value,
  formatValue = String,
  onChange,
}: RangeScrubberProps) {
  return (
    <Slider.Root
      min={min}
      max={max}
      step={step}
      value={value}
      onValueChange={(details) => onChange([details.value[0]!, details.value[1]!])}
      className="flex w-full max-w-xs flex-col gap-1.5 print:hidden"
    >
      <div className="flex items-center justify-between text-xs font-medium text-slate-700 dark:text-slate-300 tv:text-xl">
        <Slider.Label>{label}</Slider.Label>
        <span className="tabular-nums font-semibold">
          {formatValue(value[0])} – {formatValue(value[1])}
        </span>
      </div>
      <Slider.Control className="relative flex h-5 items-center select-none touch-none">
        <Slider.Track className="h-1.5 w-full overflow-hidden rounded-full bg-slate-200 dark:bg-slate-700">
          <Slider.Range className="h-full bg-blue-600 dark:bg-blue-400" />
        </Slider.Track>
        {[0, 1].map((index) => (
          <Slider.Thumb
            key={index}
            index={index}
            className="h-4 w-4 rounded-full border-2 border-blue-600 bg-white shadow-xs focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-blue-500 dark:border-blue-400 dark:bg-slate-900 tv:h-6 tv:w-6"
          >
            <Slider.HiddenInput />
          </Slider.Thumb>
        ))}
      </Slider.Control>
    </Slider.Root>
  );
}
```

---

## 4. Copy-Ready Dimension Filter (`Select`)

```tsx
import { Select, createListCollection } from "@ark-ui/react/select";
import { Portal } from "@ark-ui/react/portal";
import { Check, ChevronDown } from "lucide-react";
import { useMemo } from "react";

export interface DimensionSelectProps {
  label: string;
  items: readonly { label: string; value: string }[];
  value: string;
  onChange: (value: string) => void;
}

export function DimensionSelect({ label, items, value, onChange }: DimensionSelectProps) {
  const collection = useMemo(() => createListCollection({ items: [...items] }), [items]);

  return (
    <Select.Root
      collection={collection}
      value={[value]}
      onValueChange={(details) => {
        if (details.value[0]) onChange(details.value[0]);
      }}
      className="flex flex-col gap-1 print:hidden"
    >
      <Select.Label className="text-xs font-medium text-slate-600 dark:text-slate-400 tv:text-lg">
        {label}
      </Select.Label>
      <Select.Control>
        <Select.Trigger className="inline-flex min-w-40 items-center justify-between gap-2 rounded-lg border border-slate-300 bg-white px-3 py-1.5 text-xs font-medium text-slate-800 shadow-2xs focus-visible:outline-2 focus-visible:outline-blue-500 dark:border-slate-700 dark:bg-slate-800 dark:text-slate-100 tv:px-5 tv:py-2.5 tv:text-xl">
          <Select.ValueText placeholder="Select..." />
          <Select.Indicator>
            <ChevronDown className="h-3.5 w-3.5 opacity-60 tv:h-5 tv:w-5" />
          </Select.Indicator>
        </Select.Trigger>
      </Select.Control>
      <Portal>
        <Select.Positioner>
          <Select.Content className="z-50 min-w-40 rounded-lg border border-slate-200 bg-white p-1 shadow-lg dark:border-slate-700 dark:bg-slate-800">
            {collection.items.map((item) => (
              <Select.Item
                key={item.value}
                item={item}
                className="flex cursor-pointer items-center justify-between rounded-md px-2.5 py-1.5 text-xs text-slate-700 data-highlighted:bg-slate-100 dark:text-slate-200 dark:data-highlighted:bg-slate-700 tv:text-lg"
              >
                <Select.ItemText>{item.label}</Select.ItemText>
                <Select.ItemIndicator>
                  <Check className="h-3.5 w-3.5 text-blue-600 dark:text-blue-400" />
                </Select.ItemIndicator>
              </Select.Item>
            ))}
          </Select.Content>
        </Select.Positioner>
      </Portal>
      <Select.HiddenSelect />
    </Select.Root>
  );
}
```

---

## 5. Resizable 3D + 2D Linked Workspace (`Splitter` + `Dialog` Mobile Fallback)

On desktop/TV, use `Splitter` so users can adjust the ratio between a 3D spatial view and a 2D analytical chart/table. On mobile (`< 640px`), stack them vertically or place filters inside an Ark UI `Dialog` bottom sheet.

```tsx
import { Splitter } from "@ark-ui/react/splitter";
import type { ReactNode } from "react";

export function LinkedVizSplitView({
  primaryView,
  secondaryView,
}: {
  primaryView: ReactNode;
  secondaryView: ReactNode;
}) {
  return (
    <Splitter.Root
      panels={[{ id: "primary", minSize: 35 }, { id: "secondary", minSize: 25 }]}
      defaultSize={[60, 40]}
      className="flex flex-col gap-4 lg:flex-row lg:gap-0 print:flex-col"
    >
      <Splitter.Panel id="primary" className="min-w-0 flex-1">
        {primaryView}
      </Splitter.Panel>
      <Splitter.ResizeTrigger
        id="primary:secondary"
        aria-label="Resize visualization panels"
        className="hidden w-2 cursor-col-resize items-center justify-center bg-transparent hover:bg-blue-500/20 focus-visible:bg-blue-500/40 lg:flex print:hidden"
      >
        <div className="h-8 w-1 rounded-full bg-slate-300 dark:bg-slate-700" />
      </Splitter.ResizeTrigger>
      <Splitter.Panel id="secondary" className="min-w-0 flex-1">
        {secondaryView}
      </Splitter.Panel>
    </Splitter.Root>
  );
}
```
