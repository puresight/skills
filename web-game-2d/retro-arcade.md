# Retro Arcade & Console Fidelity

The goal is a game that *feels* like the original: same timing, same rules, same screen flow. Get the core loop right before adding polish.

## 1. Write a spec first (short `GAME_SPEC.md`)
Before coding, list from memory/research:
- Platform & year, native resolution, orientation (many arcade games are vertical 3:4, e.g. Pac-Man 224×288, Galaga 224×288; Space Invaders 224×256 rotated; NES 256×240; Atari 2600 ~160×192).
- Controls (joystick directions, buttons, paddles/trackball/spinner).
- Entities, speeds (in pixels per frame at 60 Hz), scoring table, extra-life score, level progression, known quirks (e.g. Space Invaders speeds up as aliens die; Pac-Man ghost scatter/chase timers).
- Screens: attract → title/insert coin → play → death → game over → high-score entry.
Mark anything uncertain as "approximate" rather than inventing precise numbers.

## 2. Resolution & scaling
- Render to a fixed native-size canvas (or offscreen buffer) and scale up by an **integer** factor with `image-rendering: pixelated`. Letterbox the rest with black.
- Simulate in native pixel units at a fixed 60 Hz tick (see `web-game-core/loop.md`). Express speeds per tick, matching original "pixels per frame".
- Never scale with fractional factors that blur or shimmer pixels.

## 3. Look
- **Raster:** limited palette per platform (NES ~54 colors, 2600 NTSC 128). Define the palette as constants; no gradients or anti-aliasing.
- **Vector** (Asteroids, Battlezone, Tempest, Star Wars): draw lines on black with `lineCap: "round"` and a glow (draw twice: wide low-alpha + thin bright, or `shadowBlur`). In 3D, use `LineSegments` / `EdgesGeometry` with additive blending.
- **Optional CRT filter** (setting, default subtle/off): scanlines via CSS overlay, slight vignette. Must be toggleable and cheap.
- Fonts: use a pixel font (e.g. "Press Start 2P" bundled locally, not from a CDN) for HUD/score.

## 4. Arcade flow conventions
- Attract mode: demo/title cycle when idle; any key or "insert coin" (key `5` or `C`) adds a credit; `1`/`2` starts 1P/2P. Also provide a clear on-screen Start button for touch users.
- HUD: `1UP` score, `HIGH SCORE`, `2UP`, lives as icons, level indicator.
- Extra life at a fixed score (commonly 10,000 or 20,000).
- High score table: top 10 with 3-letter initials entered via joystick-style letter cycling; persist in Dexie (`web-game-core/persistence.md`).
- Pause (P / Esc) and auto-pause on tab hide — a modern courtesy not in originals.

## 5. Difficulty
- Reproduce the original ramp (speed, enemy count, aggression) per level/wave. Keep all tuning numbers in one `config.ts`.
- Use seeded RNG (e.g. a small mulberry32) so behavior is reproducible and testable; some originals were fully deterministic (Pac-Man patterns).

## 6. Sound
Square/triangle/noise chip-style sound effects synthesized with Web Audio (`web-game-core/audio.md`). No original audio samples.

## 7. Legal / IP
- Recreate mechanics and style; do not copy ROM data, original sprites, sound samples, or trademarked names/logos into a public release. For personal projects, still prefer an homage title (e.g. "Space Defenders") unless the user says otherwise.
- Credit inspirations in a Credits screen.

## Related skills
`game-design-brief` (naming, IP), `web-game-platformer`, `web-game-2d/canvas.md` (raster), `web-game-3d` / `web-game-3d/style.md` (3D remakes), `web-game-core/input.md`, `web-game-core/state-machine.md`, `web-game-board/hotseat.md`.
