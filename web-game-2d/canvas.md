# 2D Canvas Rendering for Retro Games

## 1. Canvas inside React
- One `<canvas>` owned by a component; get the context in `useEffect`, start the loop there, and **cancel `requestAnimationFrame` in the cleanup** (React StrictMode mounts twice in dev — otherwise two loops run at double speed).
- Game state lives in plain TS objects, never in React state. React renders only menus/overlays (Tailwind) on top of the canvas.

```tsx
useEffect(() => {
  const canvas = ref.current!;
  const ctx = canvas.getContext("2d", { alpha: false })!;
  ctx.imageSmoothingEnabled = false;
  const stop = startLoop(update, (alpha) => render(ctx, alpha)); // startLoop returns a cancel fn
  return stop;
}, []);
```

## 2. Pixel-perfect scaling
- Canvas `width/height` = native resolution (e.g. 224×288). Scale with CSS by the largest integer that fits:
```typescript
const scale = Math.max(1, Math.floor(Math.min(innerWidth / W, innerHeight / H)));
canvas.style.width = `${W * scale}px`; canvas.style.height = `${H * scale}px`;
```
- CSS: `image-rendering: pixelated;`. Set `ctx.imageSmoothingEnabled = false` again after any resize.
- Draw at integer coordinates: `Math.round(x)` (or `| 0`) when drawing; keep sub-pixel positions in the simulation.

## 3. Sprites
- No external art required: define sprites as string grids and bake them to offscreen canvases once at startup.
```typescript
const INVADER = [
  "..X.....X..",
  "...X...X...",
  "..XXXXXXX..",
  ".XX.XXX.XX.",
  "XXXXXXXXXXX",
  "X.XXXXXXX.X",
  "X.X.....X.X",
  "...XX.XX...",
];
function bake(rows: string[], color: string): HTMLCanvasElement {
  const c = document.createElement("canvas");
  c.width = rows[0].length; c.height = rows.length;
  const g = c.getContext("2d")!; g.fillStyle = color;
  rows.forEach((r, y) => [...r].forEach((ch, x) => ch === "X" && g.fillRect(x, y, 1, 1)));
  return c;
}
```
- Animation frames = array of baked canvases; advance on a tick counter, not wall time.
- With a sprite sheet image: `drawImage(sheet, sx, sy, w, h, dx, dy, w, h)` using a frame table in JSON.

## 4. Tilemaps & mazes
- Store levels as string arrays or `Uint8Array` grids (`#` wall, `.` dot, `o` power pellet).
- Pre-render static layers (walls) to an offscreen canvas once; redraw only dynamic layers each frame.
- Grid movement: entities move between tile centers; allow turns only when aligned to the center (with a small tolerance "cornering" window like Pac-Man).
- Collision with walls = tile lookup, not physics.

## 5. Text
Bitmap/pixel font bundled locally. Draw text in native resolution so it scales crisply.

## 6. Vector games
`ctx.globalCompositeOperation = "lighter"`, draw each shape twice (thick faint + thin bright) for phosphor glow; clear with slight alpha (`rgba(0,0,0,0.35)`) for optional trails.

## 7. When to use PixiJS
Only for >1,000 sprites or heavy effects. Canvas 2D is enough for virtually all 1970s-80s games.
