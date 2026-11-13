---
name: web-game-platformer
description: "Use when building a side-view 2D or 2.5D platformer or platform-adventure (jungle runners, ladder-and-girder games): run/jump feel, coyote time, ladders, swinging ropes/vines, timed pits and rolling hazards, screen-flip rooms or scrolling camera, and seeded procedural room generation."
---

# Side-View Platformers

Pair with `web-game-core`, `web-game-2d` (or `web-game-3d` for 2.5D), and `game-design-brief`.

## Rules at a glance
1. **Kinematic movement, not a physics engine**: player state machine `idle | run | jump | fall | climb | swing | hurt | dead`. All speeds/gravity in `config.ts` as native px/tick at 60 Hz.
2. **Jump feel**: include ~6-tick coyote time and ~6-tick jump buffer (toggleable for strict retro). Axis-separated tile/rect collision; hazard hitboxes slightly smaller than sprites.
3. **Ladders & ropes**: snap to ladder center on up/down; disable gravity while climbing. Rope = pendulum (or fixed-period cycle); release launches player with tangential velocity.
4. **Hazards & rooms**: drive timed pits, snapping hazards, and rolling logs from a global tick counter so rooms are deterministic puzzles. Screen-flip at edges (`room + 1` / `room - 1`); optional underground shortcut layer where 1 room = N surface rooms.
5. **Procedural rooms**: generate from an 8-bit reversible LFSR or `mulberry32(seed + roomIndex)` — design an original bit-to-feature mapping.

## Sibling file
- [reference.md](reference.md) — full movement rules, ladder/vine math, hazard table, and tested 255-room reversible LFSR template.
