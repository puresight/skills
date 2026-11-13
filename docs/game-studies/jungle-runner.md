# Study: Jungle Runner

**Inspired by:** early-1980s console platform adventures, notably David Crane's 1982 Atari 2600 classic (title and hero are trademarks of their owner — never use them or the hero's likeness).
**Taxonomy:** real-time · perfect information per screen · screen-flip rooms (horizontal ring) + underground layer · platformer / adventure

## 1. Design teardown (abstract)
- Hero runs left/right through a long looping jungle of single-screen rooms; 20-minute timer; collect treasures for points.
- Hazards: pits, tar/water pits that open and close, quicksand, rolling logs, crocodile heads (time jumps), fire, snakes, scorpions underground.
- Vines to swing over hazards; ladders to an underground tunnel where one underground screen = several above-ground screens (shortcut with risk).
- Lives: 3; falling into the underground costs points, not lives.
- World generated deterministically from a linear-feedback shift register — 255 rooms in a few bytes.

## 2. Why it's fun
- **Challenge** (timing jumps, swing release), **discovery** (what's next screen?), **fantasy** (explorer), **mastery through memorization** (route planning with the shortcut tunnel).
- Clear, readable screens; each room is a small puzzle with a timing solution.
- Constraints made it elegant — a lesson for MVP thinking.

## 3. Reimagining seeds
- **Theme/setting**: coral-reef diver; desert canyon courier; overgrown ruined city; micro-scale insect explorer in a garden.
- **Hero**: original character silhouette, palette, and name.
- **Structure**: same screen-flip readability, but with branching vertical layers, or a day/night cycle changing hazards.
- **Signature mechanic ideas**:
  - *Momentum swing*: vine/rope physics where release timing sets jump distance.
  - *Tool slot*: one carried item (rope, lantern, shovel) changes how you solve rooms.
  - *Map reveal*: rooms you've seen appear on a minimap — exploration as progress visualization.
  - *Seeded daily world*: everyone plays the same procedurally generated jungle each day (shared leaderboard).
- **Distance check**: no 2600-identical palette of the original's screens, no crocodile-head bridges as signature set-piece, different hazards roster, no ripped sounds.

## 4. Look options
- **Faithful-era 2D**: native ~160×192 or 320×200 canvas, integer scaling, limited palette (`retro-arcade-fidelity`, `web-game-canvas-2d`).
- **2.5D**: low-poly 3D world on rails with side-view camera (side-scroller in R3F) — practices 3D while keeping 2D gameplay.

## 5. Tech notes
- Skills: `web-game-platformer-2d` (core movement), `web-game-canvas-2d`, `web-game-loop-architecture`, `web-game-input`, `web-game-audio`, `retro-arcade-fidelity`, `web-game-math` (seeded generator).
- Room generation: seeded PRNG/LFSR → room descriptor (bits pick pit type, tree pattern, ladder, hazard, treasure). Deterministic = testable and shareable.
- Timer and score in HUD; treasure count; minimap of visited rooms.

## 6. Consulting transfer
- Procedural rooms from a seed ↔ reproducible scenario generation.
- Side-scrolling world ↔ timeline/roadmap "walkthrough" visualizations.
- Minimap of explored rooms ↔ progress/coverage visualization in onboarding or audits.
