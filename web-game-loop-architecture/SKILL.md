---
name: "web-game-loop-architecture"
description: "Use when architecting game loops, managing input polling, handling delta-time integration, or syncing physics state to renderers like Canvas or PixiJS. Covers fixed time-stepping, state interpolation, and tick decoupling. Does not apply to turn-based or non-realtime web applications."
---

# Real-Time Game Loop & State Architecture

Ensure robust decoupling between the simulation update frequency and display refresh rate.

## 1. Fixed Timestep Accumulator Pattern

Every real-time game MUST use an explicit fixed timestep with an accumulator to prevent physics instability, tunnelling, and hardware-dependent speed variations.

```typescript
const FIXED_TIMESTEP = 1 / 60; // 16.66ms
let accumulator = 0;
let lastTime = performance.now();

export function startLoop(
  updatePhysics: (dt: number) => void,
  render: (alpha: number) => void,
) {
  function frame(currentTime: number) {
    let frameTime = (currentTime - lastTime) / 1000;
    if (frameTime > 0.25) frameTime = 0.25; // Spiral-of-death clamp
    lastTime = currentTime;

    accumulator += frameTime;

    while (accumulator >= FIXED_TIMESTEP) {
      updatePhysics(FIXED_TIMESTEP);
      accumulator -= FIXED_TIMESTEP;
    }

    const alpha = accumulator / FIXED_TIMESTEP;
    render(alpha);

    requestAnimationFrame(frame);
  }

  requestAnimationFrame(frame);
}
```

## 2. Rendering Decoupling

- The Canvas or PixiJS renderer is strictly a view layer. It reads physics transforms and renders sprites.
- Never write game rules, collision checks, or state mutations inside draw/render callbacks.
- If interpolating positions between frames for high-refresh displays, use `alpha` to lerp from `previousPosition` to `currentPosition`.

## 3. Input Buffer & Polling

- Buffer keyboard, mouse, and Gamepad API inputs in event listeners.
- Consume and clear the input state exclusively inside the fixed `updatePhysics` tick, never directly inside DOM event handlers.
