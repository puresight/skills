# Persistence (Dexie + Zod)

```bash
pnpm add dexie dexie-react-hooks zod
```

## 1. One database module
```typescript
// src/db.ts
import Dexie, { type Table } from "dexie";
import { z } from "zod";

export const HighScore = z.object({ id: z.number().optional(), game: z.string(), initials: z.string().length(3), score: z.number().int().nonnegative(), at: z.number() });
export const Settings = z.object({ key: z.literal("settings"), volume: z.number().min(0).max(1).default(0.6), muted: z.boolean().default(false), crt: z.boolean().default(false), autoRotate: z.boolean().default(true) });
export const SavedMatch = z.object({ id: z.string(), game: z.string(), pgn: z.string().optional(), state: z.unknown().optional(), players: z.array(z.string()), updatedAt: z.number() });

class GameDB extends Dexie {
  highScores!: Table<z.infer<typeof HighScore>, number>;
  settings!: Table<z.infer<typeof Settings>, string>;
  matches!: Table<z.infer<typeof SavedMatch>, string>;
  constructor() {
    super("retro-games");
    this.version(1).stores({ highScores: "++id, game, score", settings: "key", matches: "id, game, updatedAt" });
  }
}
export const db = new GameDB();
```

## 2. Rules
- **Validate on read** with `schema.safeParse`; on failure fall back to defaults instead of crashing (old saves, manual edits).
- Bump `this.version(n)` with an `.upgrade()` when changing indexes; never edit an existing version's schema.
- Write at meaningful moments (move made, game over, setting changed) — never every frame.
- High scores: keep top 10 per game; seed with classic-style defaults (e.g. "AAA 10000") so the table isn't empty.
- Reading in React: `useLiveQuery(() => db.highScores.where("game").equals(id).reverse().sortBy("score"))`.
- Wrap DB access in try/catch: IndexedDB can be unavailable (private mode); the game must still run.
- Provide "Reset high scores" and "Delete saved game" in settings.
