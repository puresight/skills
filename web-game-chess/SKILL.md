---
name: web-game-chess
description: "Use when implementing chess (or checkers/draughts) in a browser game: legal moves, check/checkmate/draws, promotion, castling, en passant, PGN/FEN save and undo via chess.js, and 8x8 square-to-3D world coordinate mapping."
---

# Chess & Checkers Rules

Pair with `web-game-core`, `web-game-3d`, and `web-game-board`.

```bash
pnpm add chess.js
```

## Rules at a glance
1. **Delegate all chess rules to `chess.js`** — never hand-write move generation. Wrap a single `Chess` instance in `src/game/chessGame.ts` and read it in React with `useSyncExternalStore`.
2. **chess.js v1+ throws on illegal moves** — always wrap `chess.move()` in `try/catch`.
3. **Board indexing**: `board()[0]` is rank **8** (`file = "abcdefgh"[col]`, `rank = 8 - row`). Square `a1` is dark: `(file + rank) % 2 === 0`.
4. **Special moves**: check `needsPromotion(from, to)` and show a Q/R/B/N picker before calling `move()`. Animate rook castling and en passant removals from the returned `Move.flags` (`k`, `q`, `e`), not by diffing the board. Give each piece a stable start-square id so React keys survive moves.
5. **Save PGN** (not just FEN) to Dexie after every move so history, undo, and threefold repetition survive reload. Offer resign and draw-by-agreement buttons.
6. **Checkers**: write a small pure rules module (mandatory captures, multi-jump chains, kinging) and reuse the same `squareToWorld` / `worldToSquare` functions.

## Sibling file (copy `ChessGame` and `squareToWorld` verbatim)
- [chess-game.md](chess-game.md) — complete `ChessGame` wrapper, `squareToWorld`/`worldToSquare`, interaction flow, optional Stockfish Worker notes.
