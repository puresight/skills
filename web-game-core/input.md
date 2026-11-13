# Game Input

## 1. Action map, not keys
Game code reads **actions** (`left`, `right`, `up`, `down`, `fire`, `start`), never raw keys. Devices write into the same per-player state.

```typescript
export type Action = "up" | "down" | "left" | "right" | "fire" | "fire2" | "start" | "pause";
export const KEYMAP: Record<string, { player: 0 | 1; action: Action }> = {
  KeyW: { player: 0, action: "up" }, KeyS: { player: 0, action: "down" },
  KeyA: { player: 0, action: "left" }, KeyD: { player: 0, action: "right" },
  Space: { player: 0, action: "fire" }, Digit1: { player: 0, action: "start" },
  ArrowUp: { player: 1, action: "up" }, ArrowDown: { player: 1, action: "down" },
  ArrowLeft: { player: 1, action: "left" }, ArrowRight: { player: 1, action: "right" },
  Enter: { player: 1, action: "fire" }, Digit2: { player: 1, action: "start" },
  KeyP: { player: 0, action: "pause" }, Escape: { player: 0, action: "pause" },
};
```
For 1-player games, map both WASD and arrows to player 0.

## 2. Held vs pressed
Track `down` (held) plus `pressed` (went down since last tick). Set `pressed` in the event handler, **clear it at the end of each fixed simulation tick**. This guarantees a quick tap is never missed between frames.

- Use `event.code` (physical key), not `event.key` (layout-dependent).
- Ignore `event.repeat` for `pressed`.
- `preventDefault()` for arrows/Space to stop page scrolling — only while the game has focus.
- On `blur`/`visibilitychange`, clear all held keys (prevents stuck movement).

## 3. Gamepad
Poll `navigator.getGamepads()` once per tick (there are no button events). Standard mapping: buttons 0 = A/fire, 9 = Start, 12–15 = D-pad; axes 0/1 = left stick with a deadzone of ~0.25. Assign pads to players by index.

## 4. Touch (mobile)
- Show on-screen controls only on touch devices (`matchMedia("(pointer: coarse)")`).
- Virtual D-pad/joystick + fire button(s) as absolutely-positioned elements using Pointer Events with `setPointerCapture`; support multi-touch.
- CSS `touch-action: none` on the game area.

## 5. Pointer picking (board games / 3D)
- In R3F use mesh `onPointerDown` / `onPointerOver` with `e.stopPropagation()` so only the nearest object reacts. Use `onPointerDown` instead of `onClick` if OrbitControls drags trigger false clicks — or compare pointer-down/up distance (< 5 px = click).
- Give hit targets generous size: a flat plane per square (material `transparent`, `opacity={0}`, `depthWrite={false}`) sitting just above the board is easier to click than thin pieces. Clicking a piece should resolve to its square.
- Show hover highlight and `cursor: pointer`.

## 6. Accessibility & settings
Remappable keys stored in Dexie, a controls help overlay, and full keyboard play for menus.
