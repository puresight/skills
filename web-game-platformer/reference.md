# Side-View Platformers

Use custom kinematic movement (not a physics engine) — classic platformers are hand-tuned rules. Simulate at fixed 60 Hz (`web-game-core/loop.md`).

## 1. Player state machine
`idle | run | jump | fall | climb | swing | hurt | dead`. Each state allows specific inputs. No boolean soup.

## 2. Movement & feel (constants in `config.ts`, units = native px/tick)
- Horizontal: either instant classic movement (constant speed, no acceleration — authentic to early consoles) or accel/decel for modern feel. Pick one deliberately.
- Jump: fixed arc for classic (no air control or limited), or variable height (cut upward velocity on release) for modern.
- Gravity applied per tick; clamp max fall speed.
- **Coyote time** (~6 ticks after leaving a ledge, jump still allowed) and **jump buffer** (~6 ticks before landing) — invisible, makes controls feel fair. Optional for strict retro mode.
- Collision: axis-separated against solid tiles/rects (`web-game-2d/collision.md`). Use a hitbox smaller than the sprite for hazards (forgiving), exact for floors.

## 3. Ladders
- Enter climb when overlapping ladder and pressing up/down; snap x to ladder center.
- While climbing: no gravity; exit at top onto floor or by jumping off sideways.

## 4. Swinging rope / vine
- Pendulum: angle θ, angular velocity ω; `ω += -(g / L) * sin(θ) * dt`; θ += ω·dt. Classic style: fixed-period scripted swing regardless of player.
- Grab when the player overlaps the rope end while airborne; position player at rope end each tick.
- Release on jump: launch velocity = tangential velocity of rope end (momentum swing — a good signature mechanic).

## 5. Hazards (data-driven)
| Hazard | Behavior |
|---|---|
| Pit | Static gap; falling below floor → life lost or drop to lower layer |
| Timed pit (opens/closes) | Width follows a cycle on a global tick counter |
| Rolling obstacle | Moves at constant speed, wraps in room; collision = knockback/time penalty |
| Snapping hazard | Safe/unsafe phases; can be stood on only during safe phase |
| Static hazard (fire, spikes) | Instant hit |
Drive all cycles from a global tick counter so behavior is deterministic and learnable.

## 6. World structure
- **Screen-flip rooms** (authentic early-80s): exiting the right edge loads room `i+1` and places player at left edge. Room count can be a ring (wraps).
- **Scrolling camera** (later style): camera follows with dead zone, clamps to level bounds; snap to integer pixels.
- **Layers**: an underground layer where one room spans several above-ground rooms (shortcut with risk).

## 7. Procedural rooms from a seed
```typescript
// 8-bit Galois LFSR — tiny deterministic sequence of 255 room codes
export function nextRoom(code: number, dir: 1 | -1): number {
  if (dir === 1) { const lsb = code & 1; code >>= 1; if (lsb) code ^= 0xb8; }
  else { const msb = code & 0x80; code = (code << 1) & 0xff; if (msb) code ^= 0x71; }  // inverse step
  return code;
}
// decode bits into features
export const decode = (c: number) => ({
  pit: (c >> 3) & 0b111,        // pit / pond / quicksand variants
  hazard: c & 0b111,            // which hazard
  trees: (c >> 6) & 0b11,       // background pattern
  ladder: ((c >> 3) & 1) === 1,
  rope: ((c >> 5) & 1) === 1,
});
```
Verify the inverse step with a unit test (`nextRoom(nextRoom(c, 1), -1) === c` for all 255 non-zero codes); adjust taps if it fails. Or use `mulberry32(seed + roomIndex)` for simplicity. Design your own bit layout — don't copy an original's room table.

## 8. Game frame
Countdown timer (e.g. 20:00), score, lives, treasures remaining, minimap of visited rooms. Death → brief pause → respawn at room's safe entry point. Game over → high-score initials (`web-game-2d/retro-arcade.md`, `web-game-core/persistence.md`).

## 9. 2.5D variant (R3F)
Gameplay stays 2D (x, y) in the sim; render low-poly 3D meshes with an orthographic or narrow-FOV side camera at fixed z. Parallax via background layers at different z.

## 10. Originality
Original hero, setting, hazards roster, and room design. See `docs/originality-and-ip.md` and `docs/game-studies/jungle-runner.md`.
