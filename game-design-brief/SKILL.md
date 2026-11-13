---
name: game-design-brief
description: "Use when starting of any game project, and when naming the game, characters, units, or locations, choosing art direction, or reimagining a classic board, arcade, or console game. Covers writing a design brief, originality and trademark safety, and retro specs. Does not cover implementation code."
---

# Design Brief & Originality

Before writing code, create `DESIGN_BRIEF.md` (and `GAME_SPEC.md` for arcade/console remakes).

## Hard rules
1. **No trademarks**: never put protected game names, character names, or logos in titles, UI, code, file names, or package names (no "Risk", "Stratego", "Pitfall", "Pac-", etc.). Public-domain games (chess, checkers, Go) may use their names.
2. **No copied expression**: never copy art, sprites, 3D models, audio, rulebook text, or level/room tables. Use procedural art or licensed assets recorded in `CREDITS.md`. Keep paid asset packs out of public repos.
3. **Distinct look-and-feel**: do not reproduce an original's palette + layout + character silhouette together.
4. **Reimagining rule**: change ≥3 axes (theme, topology, roster, resolution, win condition, scale) and add one **signature mechanic** the inspiration lacked.

## `DESIGN_BRIEF.md` template
```markdown
# <Original Working Title>
Inspired by (genre only, no trademarks):
Target fun (MDA aesthetics): e.g. challenge, discovery, fellowship
Core loop (1 sentence):
Axes changed (≥3): theme | topology | roster | resolution | win condition | scale
Signature mechanic:
Resolution / camera / palette:
Controls (actions):
Screens / phases:
Checklist: [ ] original names [ ] licensed/procedural assets [ ] distinct look [ ] ≥3 axes changed [ ] signature mechanic
```
If the user hasn't chosen these for a non-public-domain game, propose 2–3 options and ask before coding.

## Sibling files (read only when needed)
- [originality.md](originality.md) — IP rules, distance checklist, naming guidance.
- Deeper reference for humans: `../docs/originality-and-ip.md`, `../docs/fun-and-design-principles.md`, `../docs/game-studies/`.
