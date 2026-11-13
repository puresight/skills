---
name: game-design-originality
description: "Use at the very start of any game inspired by an existing commercial game (board game, arcade, or console title), and whenever naming the game, its characters, units, or locations, or choosing art direction. Ensures the result reimagines design principles without copying protected expression or trademarks. Also use when writing a game design brief. Public-domain games (chess, checkers, Go) are exempt from renaming but still need original assets."
---

# Original Reimagining (IP-safe Design)

Full background: `docs/originality-and-ip.md`, `docs/fun-and-design-principles.md`.

## Hard rules
1. **Never** use trademarked game names, character names, or logos in titles, UI text, file names, package names, or metadata (e.g. no "Risk", "Stratego", "Pitfall", "Pac-"). Use generic descriptions in comments if needed ("territory conquest").
2. **Never** copy original art, sprites, models, sound, music, rulebook text, or level/room tables. Create or use properly licensed assets; record them in `CREDITS.md`.
3. Don't imitate the original's distinctive look-and-feel (palette + layout + characters together). Pick a new art direction.
4. Rename units/pieces with fresh concepts, not 1:1 synonyms of the original roster.

## Before coding: write `DESIGN_BRIEF.md`
```markdown
# <Original working title>
Inspired by (genre only): ...
Target aesthetics (MDA): e.g. discovery, challenge
Core loop (one sentence):
Axes changed vs inspiration (≥3): theme | board/topology | roster | resolution | win condition | scale | time model
Signature mechanic (new idea):
Art direction (palette, shapes, mood):
Names: title, factions/characters, units
Distance checklist: [ ] names original [ ] assets original/licensed [ ] look distinct [ ] ≥1 rule changed [ ] signature mechanic
```
If the user hasn't provided these, propose 2–3 options and ask before building.

## Naming tips
- Evocative compound words or invented names; check that a quick web search doesn't hit an existing game trademark.
- Avoid near-misspellings of the original.

## Credit
Add a Credits screen: "Inspired by classic <genre> games." List asset licenses.
