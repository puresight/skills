# Chess Rules & Board Model

Never hand-write chess move generation. Small rule bugs (castling through check, en passant pins, promotion, threefold repetition) are the #1 failure in AI-generated chess. Use `chess.js`.

```bash
pnpm add chess.js
```

## 1. Single source of truth
Wrap one `Chess` instance in a plain TypeScript module outside React. The UI/3D scene only reads from it and calls its methods.

```typescript
// src/game/chessGame.ts
import { Chess, type Move, type Square, type PieceSymbol } from "chess.js";

export class ChessGame {
  private chess = new Chess();
  private listeners = new Set<() => void>();

  fen() { return this.chess.fen(); }
  turn() { return this.chess.turn(); }                 // "w" | "b"
  board() { return this.chess.board(); }               // 8x8, rank 8 first
  legalTargets(from: Square): Square[] {
    return this.chess.moves({ square: from, verbose: true }).map((m) => m.to);
  }
  needsPromotion(from: Square, to: Square): boolean {
    return this.chess.moves({ square: from, verbose: true })
      .some((m) => m.to === to && m.promotion !== undefined);
  }
  move(from: Square, to: Square, promotion?: PieceSymbol): Move | null {
    try {
      const m = this.chess.move({ from, to, promotion });
      this.emit();
      return m;
    } catch {
      return null; // chess.js v1 throws on illegal moves
    }
  }
  undo() { const m = this.chess.undo(); this.emit(); return m; }
  status() {
    const c = this.chess;
    if (c.isCheckmate()) return { kind: "checkmate", winner: c.turn() === "w" ? "b" : "w" } as const;
    if (c.isStalemate()) return { kind: "stalemate" } as const;
    if (c.isThreefoldRepetition()) return { kind: "threefold" } as const;
    if (c.isInsufficientMaterial()) return { kind: "insufficient" } as const;
    if (c.isDraw()) return { kind: "fifty-move" } as const;
    return { kind: c.inCheck() ? "check" : "playing" } as const;
  }
  pgn() { return this.chess.pgn(); }
  loadPgn(pgn: string) { this.chess.loadPgn(pgn); this.emit(); }
  subscribe(fn: () => void) { this.listeners.add(fn); return () => this.listeners.delete(fn); }
  private emit() { this.listeners.forEach((fn) => fn()); }
}
```

React: read it with `useSyncExternalStore(game.subscribe, game.fen)`.

## 2. Gotchas
- chess.js v1+ **throws** on illegal moves; wrap `move()` in try/catch. Older examples that check for `null` are wrong.
- `board()[0]` is rank **8**. Convert carefully: `file = "abcdefgh"[col]`, `rank = 8 - row`.
- Always ask for promotion piece (Q/R/B/N) when `needsPromotion` is true; never silently auto-queen unless a setting says so.
- Castling moves the rook too, en passant removes a pawn from a different square. Animate from the returned `Move` (`flags` contains `k`/`q` for castling, `e` for en passant, `captured` for captures) — do not diff the board by hand.
- Offer resign and draw-by-agreement buttons; chess.js cannot detect those.

## 3. Square ↔ world coordinates (3D)
Keep one pure function pair and use it everywhere (pieces, highlights, raycast hits):

```typescript
export const SQUARE_SIZE = 1;
export function squareToWorld(sq: Square): [number, number, number] {
  const file = sq.charCodeAt(0) - 97;      // a..h -> 0..7
  const rank = Number(sq[1]) - 1;          // 1..8 -> 0..7
  return [(file - 3.5) * SQUARE_SIZE, 0, (3.5 - rank) * SQUARE_SIZE]; // white at +z
}
export function worldToSquare(x: number, z: number): Square | null {
  const file = Math.round(x / SQUARE_SIZE + 3.5);
  const rank = Math.round(3.5 - z / SQUARE_SIZE);
  if (file < 0 || file > 7 || rank < 0 || rank > 7) return null;
  return `${"abcdefgh"[file]}${rank + 1}` as Square;
}
```
- Square a1 must be dark, h1 light: `(file + rank) % 2 === 0` → dark.
- Give each piece a stable id (e.g. `"w-p-e2"` assigned at start) so React keys survive moves and animations track the right mesh.

## 4. Interaction flow
1. Click own piece → select, highlight `legalTargets`.
2. Click highlighted square → `move()`; if promotion needed, show picker first.
3. Click another own piece → switch selection. Click elsewhere → deselect.
4. Lock input while a move animation plays; unlock when it finishes.
5. Show last-move highlight, check indicator on the king, captured-piece trays, and SAN move list.

## 5. Persistence
Save PGN (not just FEN) to Dexie after every move so history and repetition survive reloads. See `web-game-core/persistence.md`.

## 6. Optional AI
Only when asked. Use `stockfish` (WASM build, runs in a Web Worker) via UCI messages; never block the main thread. Hotseat 2-player needs no AI.

## Checkers
chess.js does not do checkers. For draughts, write a small pure rules module with unit tests (mandatory captures, multi-jump chains, kinging) and keep the same square↔world mapping pattern.
