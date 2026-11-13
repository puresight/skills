# Game Techniques → Consulting Visualization

Every game project here doubles as practice for client deliverables. This map shows the transfer.

## 1. Transfer matrix
| Game skill | Client application |
|---|---|
| Grid board + picking | Floor plans, warehouse slotting, seating charts, schedule grids |
| Territory map (Risk-like) | Sales territories, market share by region, service coverage, risk heatmaps |
| Hidden information (Stratego-like) | Role-based views, need-to-know data, progressive disclosure |
| Turn/phase state machine | Workflow and approval tools, wizards, scenario stepping |
| Undo / move history (chess PGN) | Version history, scenario branching, audit trails |
| Dice + seeded RNG | Monte Carlo simulation, risk modeling, reproducible scenarios |
| Camera seats & focus moves | Guided tours / storytelling ("scrollytelling") through a model |
| Side-scrolling world (Pitfall-like) | Timelines, roadmaps, process journeys you "walk" through |
| Height-as-value | Extruded maps, 3D site models with KPI overlays |
| Juice & feedback | Responsive, trustworthy UI: every action acknowledged |
| High scores / persistence | Saved scenarios, user preferences, offline-first field tools |
| Hotseat | Workshop facilitation: one screen, many stakeholders taking turns |

## 2. Scenario & planning tools (the big overlap)
Strategy games are **scenario planners with rules**. A client scenario tool needs the same parts:
1. **State model** — the "board" (resources, regions, assets).
2. **Actions** — legal moves (budget shifts, hires, routes).
3. **Resolution** — deterministic formulas or stochastic simulation.
4. **Turns** — time steps (quarters, years).
5. **Visualization** — map/board + charts + history.
6. **Branching** — save/compare alternative futures (like analyzing chess variations).

Build games with this separation (pure TS rules engine, renderer as a view) and the architecture transfers directly.

## 3. Choosing 2D vs 3D for clients
- **Use 3D** when the subject has real spatial form (sites, buildings, terrain, networks) or for engagement/storytelling moments.
- **Use 2D** for precise comparison (bars, lines, tables). Perspective distorts magnitudes.
- **Best**: linked views — 3D overview + 2D detail panels, synchronized selection.

## 4. Gamification in client work
See `gamification.md`. Use progress, feedback, and clear goals to drive adoption of tools — not manipulative loops.

## 5. Stack crossover
React + Three.js/R3F for 3D; D3/visx or Recharts for 2D charts; deck.gl or MapLibre for geographic data at scale; Dexie for offline. GeoJSON → `ExtrudeGeometry` is the same pipeline for a Risk-like map and a regional sales map.

## 6. Portfolio idea
Each finished game can yield a client-facing demo: e.g. the territory-conquest map re-skinned as "regional market coverage simulator"; the hidden-information board as "role-based asset tracker".
