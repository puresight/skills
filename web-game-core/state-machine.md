# Game Flow State Machine

Ad-hoc booleans (`isPaused`, `isGameOver`, `showMenu`, `isAnimating`) cause impossible states. Use one discriminated union.

## 1. Arcade flow
```typescript
export type Phase =
  | { kind: "attract" }
  | { kind: "ready"; players: 1 | 2; timer: number }        // "PLAYER 1 READY"
  | { kind: "playing" }
  | { kind: "paused"; resume: "playing" }
  | { kind: "dying"; timer: number }
  | { kind: "levelClear"; timer: number }
  | { kind: "gameOver"; timer: number }
  | { kind: "enterInitials"; rank: number; letters: string };
```
- Transitions happen in one `step(phase, input, dt)` function inside the fixed tick. Timers count down in ticks/seconds there, not with `setTimeout` (which ignores pause).
- Only `playing` advances the world simulation; other phases may animate but not change scores.

## 2. Turn-based flow (chess, board games)
```typescript
export type TurnPhase =
  | { kind: "idle" }                                        // waiting for active player to pick a piece
  | { kind: "selected"; from: Square; targets: Square[] }
  | { kind: "promoting"; from: Square; to: Square }
  | { kind: "animating"; move: Move }                       // input locked
  | { kind: "rotatingCamera" }                              // input locked (hotseat)
  | { kind: "gameOver"; result: string };
```
Input handlers check `phase.kind` first and ignore clicks in locked phases.

## 3. Bridge to React
- Keep the machine in a store outside components; expose via `useSyncExternalStore` or a tiny `zustand` store. Re-render React only on phase change, not every frame.
- Overlays (title, pause, game over, promotion picker) are Tailwind components keyed on `phase.kind`.

## 4. Rules
- Every phase has an exit. Write the transition table as a comment at the top of the file.
- `XState` is acceptable for complex flows, but a typed union + `switch` with exhaustive `never` check is usually enough.
