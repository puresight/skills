# Study: Territory Conquest

**Inspired by:** classic world-conquest area-control games (the best-known is trademarked — never use its name, map art, or card designs).
**Taxonomy:** turn-based · partly hidden (cards), stochastic (dice) · graph/area map · area control / elimination · 2–6 players

## 1. Design teardown (abstract)
- Map of ~40 territories grouped into ~6 regions; adjacency graph including sea links.
- Turn: **reinforce** (based on territories held + region bonuses + card sets) → **attack** (dice battles between adjacent territories; attacker up to 3 dice, defender up to 2; compare highest) → **fortify** (one move along connected owned territories).
- Conquering at least one territory per turn earns a card; sets trade for escalating reinforcements.
- Win: eliminate all opponents (or secret mission variants).

## 2. Why it's fun
- **Narrative** (empires rise and fall), **fellowship** (alliances, betrayal), **challenge** (risk management under dice uncertainty).
- Region bonuses create clear strategic goals and natural conflict hot spots.
- Weaknesses: long games, player elimination (idle players), runaway leader via escalating cards → design brakes.

## 3. Reimagining seeds
- **Theme**: rival colonies on a fictional archipelago or moon; corporate market expansion; ecological spread (fungi vs forest).
- **Map**: original fictional map (avoids real-world map look; also removes geopolitical baggage). Procedural maps (Voronoi) for replayability.
- **Shorter games**: victory points for holding objectives after N rounds instead of elimination.
- **Resolution alternatives**: deterministic strength + one die; or card-based combat to reduce swinginess.
- **Signature mechanic ideas**:
  - *Supply lines*: territories not connected to your capital get no reinforcement.
  - *Seasons/weather*: regions periodically block or boost attacks.
  - *Influence* rather than troops: territories flip by majority over time.
- **Anti-snowball**: reinforcement cap; underdog bonuses; fixed (non-escalating) card values.

## 4. 3D-ification
- Territories as extruded polygons; **height or stack size = troop count** (glanceable strength map).
- Owner color + pattern/emblem (colorblind safety).
- Battles: camera focuses on the two territories; dice tumble (results from seeded RNG, physics only cosmetic).
- Region outlines and bonus labels floating above.
- Overview vs detail: zoomed out shows numbers; zoomed in shows unit figures.

## 5. Tech notes
- Skills: `web-game-board` (graph maps, territory extrusion), `web-game-board/hotseat.md` (2–6 players, seat per player or fixed overhead camera), `web-game-core/state-machine.md` (reinforce → attack → fortify phases), `web-game-core/math.md` (seeded RNG), `web-game-core/persistence.md` (long games need save/resume).
- Map data as JSON: `{ id, name, region, polygon: [[x,z]...], centroid, neighbors: [] }`, validated with Zod.
- Pathfinding for fortify: BFS over owned territories.
- Battle odds helper (show attacker win probability) — great learning UI and a direct consulting skill.

## 6. Consulting transfer
- Territory map ↔ sales territories, service coverage, market share by region.
- Dice odds display ↔ communicating risk/probability to stakeholders.
- Reinforce/attack/fortify ↔ resource allocation cycles (budget → deploy → rebalance).
- Procedural Voronoi maps ↔ catchment-area analysis.
