# Study: Chess (3D low-poly, hotseat)

**Inspired by:** Chess — public domain; may be implemented exactly.
**Taxonomy:** turn-based · perfect information, deterministic · 8×8 square grid · abstract strategy

## 1. Design teardown
- 32 pieces, 6 types with distinct movement; capture by displacement; checkmate wins.
- Special rules (castling, en passant, promotion) add depth and are the common bug sources → use `chess.js`.
- Core loop: evaluate → select → move → opponent responds.

## 2. Why it's fun
- **Challenge** (analytic uncertainty), **fellowship** (hotseat rivalry), **expression** (playing style).
- Interesting decisions every move; no randomness, so outcomes feel earned.
- Weakness for casual players: steep learning curve, long thinking downtime → mitigate with legal-move highlights, last-move markers, optional move timer.

## 3. Reimagining seeds (since chess itself is free, these are optional variants)
- Variants that are also public domain: Chess960, three-check, king of the hill (all supported by `chess.js` FEN loading where applicable).
- Original twist ideas: terrain tiles that alter movement; fog-of-war mode (see only squares your pieces attack); 3D layered board (Raumschach-like levels).

## 4. 3D-ification
- Height encodes nothing in standard chess → value is physicality, animation, camera seats.
- Signature moments: knight hop arc, capture topple, checkmate king tip-over.
- Camera rotates to active player's seat (toggleable); top-down "analysis view".

## 5. Tech notes
- Skills: `web-game-3d`, `web-game-lowpoly-3d`, `web-game-3d-assets`, `web-game-chess`, `web-game-hotseat-multiplayer`, `web-game-state-machine`, `web-game-persistence`, `web-game-audio`.
- Assets: Omies 3D Chess/Checkers Set (itch.io) — licensed, keep out of public repo; record in `CREDITS.md`.
- Bonus: same asset set enables a checkers mode (custom rules module).

## 6. Consulting transfer
- Move history + undo + PGN ↔ scenario branching & audit trails.
- Legal-move highlighting ↔ constraint-aware planning UI (show only valid options).
