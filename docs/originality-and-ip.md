# Originality & Intellectual Property

Goal: learn from classic designs and build **original works** that don't infringe. This is general orientation, not legal advice — consult an IP attorney before a commercial release.

## 1. What is (generally) free to borrow vs protected (US-centric)
| Generally free | Generally protected |
|---|---|
| Game **mechanics**, rules, systems, procedures (17 U.S.C. §102(b): no copyright in "idea, procedure, process, system, method of operation") | **Expression**: art, sprites, models, music, sound, text, rulebook wording, code |
| Genre conventions (lives, scores, grids, dice combat) | **Trademarks**: names, logos (e.g. *Risk*, *Stratego*, *Pitfall!* are trademarks) |
| Public-domain games (chess, checkers, Go, mancala, *L'Attaque*-era concepts) | **Trade dress**: distinctive look/packaging that identifies a product |
| General themes (jungle explorer, world conquest, battlefield) | Specific characters (e.g. Pitfall Harry) and their distinctive designs |
| Mathematical facts (map adjacency of real geography) | Patented mechanics (rare; patents expire after 20 years) |

## 2. Cautionary cases
- **Tetris Holding v. Xio Interactive (2012)** — a clone copying Tetris's *look* (piece shapes' visual style, board dimensions, display of next piece, color changes) was found infringing, even though rules aren't copyrightable. **Lesson: copying the overall look-and-feel can infringe even when mechanics are free.**
- **Spry Fox v. LolApps (2012)** — *Triple Town* clone *Yeti Town* settled after the court allowed claims based on copied expressive elements and progression.
- **Data East v. Epyx (1988)** — Karate Champ vs. World Karate Championship: Epyx won; similarities flowing from the idea of karate itself (scènes à faire) aren't protected.
- **Atari v. Philips (1982)** — K.C. Munchkin found too close to Pac-Man's expressive characters despite differences.

## 3. Trademark hygiene
- Never use protected names in your title, store listing, or code-visible branding: no "Risk", "Stratego", "Pitfall", "Pac-". Describe heritage generically: "a territory-conquest strategy game", "a jungle-runner inspired by early-80s console adventures".
- Avoid confusingly similar names ("Rysk", "Strategoh", "Pitfail").
- Don't mimic box art, color schemes, or logo typography.

## 4. The reimagining process (repeatable)
1. **Distill** — write the original's core in abstract terms: *"Two armies with hidden ranks; capturing reveals; flag capture wins."* No names, no art.
2. **Identify the fun** — which MDA aesthetics does it deliver? (see `fun-and-design-principles.md`)
3. **Change at least three axes** — theme, setting, components, scale, topology, win condition, unit roster, resolution method, player count, time model.
4. **Add a signature mechanic** — one new idea that makes it *yours* (e.g. a scouting drone that peeks; territories with weather; vine-swing momentum).
5. **Design fresh expression** — your own art direction, names, sounds, UI. Original or properly licensed assets only.
6. **Distance check** — would a player describe it as "X with a new skin" or as "a game inspired by X"? Aim for the latter. Use the checklist below.
7. **Credit inspirations** in credits/README ("inspired by classic hidden-unit wargames") without trademarks in titles.

## 5. Distance checklist
- [ ] Title, logo, and character names are original and not confusingly similar.
- [ ] No ripped or traced art, sprites, sounds, music, or text.
- [ ] Screen layout and color palette are not a copy of the original's distinctive look.
- [ ] Unit/piece roster and names differ (not a 1:1 rename).
- [ ] At least one core rule or system is meaningfully changed.
- [ ] A signature mechanic exists that the original lacks.
- [ ] All third-party assets have licenses recorded in `CREDITS.md`.

## 6. Public domain as a safe base
Chess, checkers/draughts, Go, nine men's morris, mancala, fox & geese, Hnefatafl, Halma — free to implement exactly, and great starting points for 3D-ification experiments.

## 7. For client work
The same logic applies to dashboards and UI: you can adopt patterns (heatmaps, Sankey, map choropleths) freely; avoid copying a competitor's exact distinctive product design or brand.
