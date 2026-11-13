# Study: Hidden-Identity Skirmish

**Inspired by:** classic hidden-rank capture-the-flag wargames (the genre descends from early-1900s *L'Attaque*; modern branded versions are trademarked — never use their names, piece names, or art).
**Taxonomy:** turn-based · imperfect information, deterministic · square grid with impassable cells · wargame / bluffing

## 1. Design teardown (abstract)
- Two armies of ~40 units with numeric ranks plus specials (immobile traps, a flag, a weak unit that defeats the strongest when attacking, a trap-disarmer, a fast long-range mover).
- **Setup phase**: each player secretly arranges units in their back rows.
- Units face their owner; opponent sees only backs. Attack = reveal both; higher rank wins (ties remove both).
- Win by capturing the flag or leaving the opponent without movable units.

## 2. Why it's fun
- **Discovery** (each reveal is a mini-surprise), **expression** (setup as personal strategy), **opponent-reading** (bluffing with a weak piece acting strong).
- Memory challenge: tracking revealed enemy ranks.
- Weaknesses: long endgames; 40-piece setup is tedious → offer preset/random setups and smaller board modes.

## 3. Reimagining seeds
Change ≥3 axes:
- **Theme**: deep-sea salvage crews; rival robot workshops; myth spirits with masks.
- **Board**: hex grid, or 3D terraced island with height levels (high ground +1 rank when defending).
- **Roster**: 8–10 unit types with new names/abilities, smaller army (~16) for faster games.
- **Resolution**: rank + terrain modifier, or rock-paper-scissors cycle among three unit families.
- **Signature mechanic ideas**:
  - *Scan*: once per N turns, peek at one enemy unit (privately) — reveal is a resource.
  - *Decoys*: units that look real but vanish when attacked.
  - *Shifting fog*: hexes of mist that hide *movement* too.
- **Distance check**: no ranked military titles 1–10 mirroring the original; no red/blue box aesthetic; different flag/bomb analogues.

## 4. 3D-ification
- Occlusion IS the mechanic: units are tall tiles/standees whose fronts face their owner. Opponent camera sees emblem backs.
- Reveal moment: both units rotate to face the camera, dramatic clash, loser shatters/sinks.
- Impassable cells as visible terrain (lakes, chasms).
- Remembered enemy ranks: small floating marker on revealed units.

## 5. Tech notes
- Skills: `web-game-board-3d` (per-player views, grid), `web-game-hotseat-multiplayer` (**mandatory** pass-the-device screen — hidden info), `web-game-state-machine` (setup → play → reveal → resolve), `web-game-3d`, `web-game-lowpoly-3d`.
- Hotseat privacy: when the screen hands off, all units must render as backs until the new player confirms "I'm ready".
- Single source of truth holds true ranks; the renderer gets a **redacted view** for the viewing player.
- Rules module is small: write with unit tests.

## 6. Consulting transfer
- Per-role redacted views ↔ need-to-know dashboards, data-room permissions.
- Reveal-as-resource ↔ information-value thinking in decision analysis (value of information).
- Setup phase ↔ planning before execution; compare intended vs actual.
