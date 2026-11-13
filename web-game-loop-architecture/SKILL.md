---
name: "web-game-loop-architecture"
description: "Use when architecting real-time game loops: fixed time-stepping with an accumulator, render interpolation, input polling per tick, pause and tab-visibility handling, starting/stopping loops safely in React (StrictMode), and running fixed-step simulation inside React Three Fiber's useFrame. Applies to Canvas, PixiJS, and R3F action games. Does not apply to purely turn-based games (use web-game-state-machine) beyond their animations."
---

# Real-Time Game Loop & State Architecture

Decouple simulation rate from display refresh rate. A 144 Hz monitor must not make the game run faster.

## 1. Fixed timestep accumulator
Every real-time game MUST use a fixed timestep (60 Hz matches original arcade hardware). The loop is a factory with no module-level state and returns a stop function.

```typescript
export const FIXED_DT = 1 / 60;

export function startLoop(
  update: (dt: number) => void,          // advance simulation exactly one tick
  render: (alpha: number) => void,       // draw; alpha in [0,1) for interpolation
  isPaused: () => boolean = () => false,
): () => void {
  let acc = 0;
  let last = performance.now();
  let raf = 0;

  const frame = (now: number) => {
    let frameTime = (now - last) / 1000;
    last = now;
    if (frameTime > 0.25) frameTime = 0.25;   // spiral-of-death clamp
    if (!isPaused()) {
      acc += frameTime;
      while (acc >= FIXED_DT) { update(FIXED_DT); acc -= FIXED_DT; }
    }
    render(acc / FIXED_DT);
    raf = requestAnimationFrame(frame);
  };
  raf = requestAnimationFrame(frame);
  return () => cancelAnimationFrame(raf);
}
```

## 2. React integration
- Start in `useEffect`, return the stop function as cleanup. React StrictMode mounts effects twice in dev; without cleanup two loops run and the game plays at double speed.
- Never store per-frame game state in React state/`useState`. Use refs or plain TS objects. Push to React only on discrete events (score change, phase change) — throttled if needed.

## 3. React Three Fiber
`useFrame` runs once per display frame with variable `delta`. Run the accumulator inside it:
```typescript
const acc = useRef(0);
useFrame((_, delta) => {
  acc.current += Math.min(delta, 0.25);
  while (acc.current >= FIXED_DT) { sim.step(FIXED_DT); acc.current -= FIXED_DT; }
  // then copy sim transforms to meshes (via refs), lerping with alpha = acc.current / FIXED_DT
});
```
Mutate `ref.current.position` directly — never `setState` inside `useFrame`.

## 4. Rendering decoupling
- The renderer is a view: it reads simulation state and draws. No rules, collisions, or state mutation in render callbacks.
- For smooth motion on high-refresh displays, keep `prev` and `curr` positions per entity and draw `lerp(prev, curr, alpha)`.
- Retro pixel games often look more authentic **without** interpolation (snap to integer pixels).

## 5. Input
- Buffer keyboard, pointer, and Gamepad input in listeners; consume inside the fixed `update` tick; clear one-shot "pressed" flags at the end of each tick. See `web-game-input`.

## 6. Pause & visibility
- Auto-pause on `document.visibilitychange` (hidden) and window `blur`; reset `last` on resume so there's no time jump.
- Pause stops simulation but keeps rendering (so the pause overlay draws).

## 7. Timers
Count timers in ticks or accumulated sim seconds inside `update`. Do not use `setTimeout`/`setInterval` for gameplay timing — they ignore pause and drift.

## 8. Turn-based games
No accumulator needed for rules. Use the state machine for flow; only animations (piece slides, camera rotation) need per-frame updates, driven by `useFrame` and finishing with an event that unlocks input.
