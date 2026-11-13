---
name: web-game-2d
description: "Use when building a 2D raster or vector game in the browser (arcade shooters, maze games, paddle/ball, top-down): pixel-perfect integer-scaled Canvas 2D, procedural pixel sprites, tilemaps, vector glow, arcade collision vs Matter.js/Rapier2D, and retro arcade conventions (attract mode, lives, high-score initials)."
---

# 2D Arcade Games (Canvas, Collision, Retro Flow)

Pair with `web-game-core` and `game-design-brief`. For side-view jumping/climbing games also load `web-game-platformer`.

## Rules at a glance
1. **Resolution & scaling**: render at native resolution (e.g. 224×288 vertical, 256×240 horizontal, 160×192 early console). Scale the `<canvas>` CSS size by an **integer** factor (`Math.floor(min(vw/W, vh/H))`) with `image-rendering: pixelated` and `ctx.imageSmoothingEnabled = false`. Draw at integer coords (`x | 0`), keep sub-pixel floats in sim. → [canvas.md](canvas.md)
2. **Sprites & tiles**: bake string-grid sprites and static tilemap layers to offscreen canvases once at startup; never re-parse per frame. Vector games: draw lines twice (wide faint + thin bright) with `"lighter"` composite on black. → [canvas.md](canvas.md)
3. **Collision choice**: most 1970s–80s games use **simple arcade rules** (AABB, circle, tile lookups, axis-separated resolution, paddle bounce angle from hit offset) — NOT a physics engine. Add `matter-js` or `@dimforge/rapier2d-compat` (await `RAPIER.init()`) only for pinball, stacking, joints, or ragdolls. Sub-step fast bullets/balls so they can't tunnel. → [collision.md](collision.md)
4. **Retro flow**: attract/title cycle → credit/start → `"PLAYER 1 READY"` → play → death → game over → 3-letter initials entry. HUD shows `1UP`, `HIGH SCORE`, lives icons, level. Bundle a local pixel font; optional toggleable CSS scanline overlay. Keep all per-level tuning in `config.ts`. → [retro-arcade.md](retro-arcade.md)

## Sibling files
- [canvas.md](canvas.md) — React `<canvas>` mount, integer scaling, string-grid sprite baker, tilemaps, vector glow
- [collision.md](collision.md) — arcade collision rules, when to use Matter.js / Rapier2D, WebContainer notes
- [retro-arcade.md](retro-arcade.md) — native resolutions, palettes, attract mode, scoring, difficulty ramps
