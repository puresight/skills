# From 2D Boards to 3D Scenes

Turning a flat board or map into 3D should add **delight and clarity**, never cost legibility. These principles apply equally to client data scenes.

## 1. Why go 3D at all?
Good reasons:
- **Physicality** — pieces feel like objects; satisfying to pick up and move.
- **Height as a channel** — encode a value (army size, rank revealed, elevation, risk) as height.
- **Occlusion as mechanic** — hidden info can literally face away from the viewer (Stratego-like pieces show their back).
- **Spectacle at key moments** — capture, battle, conquest.
- **Spatial memory** — users remember "the tall stack in the northeast".

Bad reasons: "because 3D looks modern". If height or perspective encodes nothing, a tilted 2D board may be better.

## 2. Legibility precepts
1. **Default camera ≈ 45–60° tilt**, not top-down (flat) and not low (occluding). Offer a "top view" toggle for analysis.
2. **Board stays the stage**: matte, low-contrast; pieces and highlights carry contrast.
3. **Silhouettes must differ** — identify pieces by shape at a glance, not only by color (colorblind-safe).
4. **Never rely on color alone** — pair with shape, icon, or pattern.
5. **Highlights float**: legal moves as discs/rings slightly above squares; selected piece lifts.
6. **Labels in screen space** (drei `<Html>` or billboards) so text is always readable; hide when crowded.
7. **Limit perspective distortion** — moderate FOV (35–50°). Orthographic camera is a valid choice for strategy maps.
8. **Shadows ground objects** — contact shadows tell height and placement.
9. **Motion explains change** — animate moves; never teleport state the user needs to follow.
10. **Reduce clutter** — level-of-detail: aggregate (one stack with a number) when zoomed out; detail when zoomed in.

## 3. Board → 3D patterns
| 2D element | 3D treatment |
|---|---|
| Square grid | Tiles as slightly beveled slabs; alternate tones; optional raised frame with coordinates |
| Impassable cells (lakes) | Recessed water planes or raised rocks — topology becomes visible |
| Area map (territories) | Extruded polygons (`ExtrudeGeometry` from shapes/GeoJSON); height = troops or elevation; borders as lines |
| Region groups (continents) | Shared base tint + floating label; subtle plinth |
| Piece stacks / counts | Stacked tokens up to N, then a single token + number badge |
| Hidden piece | Piece faces owner's camera; back side shows faction emblem only |
| Paths / adjacency | Arcs or dashed lines between centers (great for sea links) |
| Dice | Physical-looking dice tumbling (scripted, results pre-determined by seeded RNG) |

## 4. Camera choreography
- Each player has a **seat** (see `web-game-hotseat-multiplayer`); rotate between seats on turn change.
- **Focus moves**: briefly dolly toward a battle, then return. Keep < 1 s; skippable.
- Clamp angles so the user can't get lost; provide "reset view".
- Respect `prefers-reduced-motion`.

## 5. Same principles in data visualization
- 3D bar charts usually hurt accuracy (perspective lies). Use 3D when the **spatial referent is real** (a map, a site, a building, a supply network) or when height is a secondary, glanceable channel with exact numbers on hover.
- Pair the 3D view with a 2D view/table for precise reading.
- See `visualization-for-consulting.md`.

## 6. Technical notes
- Grid ↔ world mapping as pure functions (see `web-game-chess` pattern; generalized in `web-game-board-3d`).
- Graph maps: store territories as data (id, polygon, centroid, neighbors) separate from meshes.
- Hit targets larger than visuals; raycast against invisible tile planes.
