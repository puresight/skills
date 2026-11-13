---
name: web-game-core
description: "Use when building any browser game for the foundation: fixed-timestep game loop, screen/turn phase state machine, keyboard/gamepad/touch/pointer input, Web Audio chip-style sound effects, Dexie + Zod persistence (high scores, settings, saves), and deterministic math/RNG. Load first; read sibling .md files only for the parts you need."
---

# Core Game Foundation

Every game uses these patterns. Keep rules/simulation in `src/game/` (plain TypeScript, no React, no renderer imports). React renders overlays only.

## Rules at a glance
1. **Loop**: fixed 60 Hz (`FIXED_DT = 1/60`) with accumulator; `startLoop` returns a cancel fn — always call it in `useEffect` cleanup (StrictMode runs effects twice). Auto-pause on `visibilitychange`/`blur`. Turn-based games don't run a sim loop; only animations tick. → [loop.md](loop.md)
2. **State machine**: one discriminated union for screens/phases (`attract | playing | paused | gameOver` or `idle | selected | animating | rotatingCamera`). Never boolean flags (`isPaused && !isGameOver`). Count phase timers inside the fixed tick, never `setTimeout`. → [state-machine.md](state-machine.md)
3. **Input**: map devices to **actions** (`up, down, left, right, fire, start, pause`). Use `event.code`. Track `down` (held) and `pressed` (one-shot); clear `pressed` at the end of each fixed tick. Clear all held keys on `blur`. → [input.md](input.md)
4. **Audio**: one lazy `AudioContext`, resumed on first user gesture. Synthesize retro SFX with oscillator + gain ramps and a shared noise buffer. Trigger from sim events, never render code. Mute on tab hide. → [audio.md](audio.md)
5. **Persistence**: Dexie + Zod (`highScores`, `settings`, `matches`). Validate reads with `safeParse` and fall back to defaults. Write on discrete events (move, game over, setting), never per frame. Wrap in try/catch for private-browsing mode. → [persistence.md](persistence.md)
6. **Math & RNG**: use Three.js math (3D) or `gl-matrix` with destination passing (2D). Never hand-write vector/quat math. Use squared distances in loops, `rbush` above ~30 entities, and `mulberry32(seed)` instead of `Math.random()` for gameplay. → [math.md](math.md)

## Sibling files (open only what you need, and copy code blocks verbatim)
- [loop.md](loop.md) — `startLoop` factory + R3F `useFrame` accumulator
- [state-machine.md](state-machine.md) — arcade & turn-based phase unions
- [input.md](input.md) — action map, gamepad polling, touch controls, 3D picking
- [audio.md](audio.md) — Web Audio synth (`tone`, `noise`) + recipes
- [persistence.md](persistence.md) — `GameDB` Dexie + Zod template
- [math.md](math.md) — vectors, angles, `rbush`, `mulberry32`, wrap-around
