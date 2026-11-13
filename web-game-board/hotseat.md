# Local Multiplayer (Hotseat & Shared Screen)

## 1. Pick the mode explicitly
| Mode | Examples | Key rule |
|---|---|---|
| Turn-based hotseat | Chess, checkers, Othello | Game state decides whose turn it is; UI follows. |
| Alternating arcade | Pac-Man, Donkey Kong, Galaga 2P | Each player has own lives/score/level state; swap on life lost. |
| Simultaneous | Pong, Combat, Joust, Warlords | Separate input maps per player; one shared simulation. |

## 2. Turn-based hotseat
- The rules module (e.g. `chess.js` `turn()`) is the authority for the active player. Never keep a separate `currentPlayer` that can drift out of sync.
- On turn change: lock input → finish move animation → rotate view → unlock input.
- Show an always-visible turn banner ("White to move") with player color/name.
- Optional "Pass the device" interstitial (setting, default off for chess since nothing is hidden). Required for games with hidden information (cards, Battleship).

## 3. Rotating the camera to the active player (3D)
- Store a camera "seat" per player: white looks from +z toward the center, black from −z.
- Animate the **camera** around the board's Y axis, not the board itself (keeps world coordinates and raycasting stable).
- Use drei `CameraControls` (supports `rotateTo`/`setLookAt` with smooth transitions) or damp spherical coordinates manually with `maath/easing`. If using `OrbitControls`, disable it during the rotation and call `controls.update()` after.
- Rotate the shortest way (180° either direction is fine; pick one consistently). Duration 0.6–1.0 s, ease in-out.
- Provide a setting: "Auto-rotate board each turn" (on/off) and a manual "Flip view" button. Respect `prefers-reduced-motion` by snapping instead of animating.
- 2D boards: flip by rendering ranks/files reversed, never by mutating game state.

```typescript
// seats in spherical coords around board center
export const SEATS = {
  w: { azimuth: 0, polar: Math.PI / 3.2, distance: 11 },
  b: { azimuth: Math.PI, polar: Math.PI / 3.2, distance: 11 },
} as const;
// with drei CameraControls: controlsRef.current.rotateTo(seat.azimuth, seat.polar, true)
```

## 4. Alternating arcade players
- Keep `players: PlayerState[]` each with `score`, `lives`, `level`, and level-specific state (e.g. remaining dots, destroyed bunkers).
- On life lost: save current level state into that player, switch `activeIndex` to next player with lives remaining, show "PLAYER 2 — READY" for ~2 s.
- Cocktail-cabinet option: flip the screen 180° for player 2 (CSS `transform: rotate(180deg)` on the canvas wrapper) as a toggle.
- Game over only when all players are out; show both scores.

## 5. Simultaneous play
- Default keyboard split: P1 = WASD + Space/F, P2 = Arrow keys + Enter/Right-Ctrl. Make it remappable.
- Assign gamepads by connection order; show "P1 controller connected".
- Watch for keyboard ghosting: avoid needing >3 simultaneous keys per player.
See `web-game-core/input.md`.

## 6. Persist
Save the in-progress match (players' names, colors, full move history/PGN, settings) to Dexie so a reload resumes the same game.
