# Game Taxonomy

Classifications help pick the right skills, architecture, and design precedents.

## 1. By time model
| Model | Examples | Architecture |
|---|---|---|
| Real-time | Arcade, platformers | Fixed-timestep loop (`web-game-core/loop.md`) |
| Turn-based | Chess, Risk-like, Stratego-like | State machine + animations (`web-game-core/state-machine.md`) |
| Simultaneous turns | Diplomacy-style orders | Collect hidden orders, resolve together |
| Real-time with pause | Some strategy games | Loop + pausable phase |

## 2. By information
| | Perfect information | Imperfect information |
|---|---|---|
| **Deterministic** | Chess, checkers, Go | Stratego-like (hidden ranks) |
| **Stochastic** | Backgammon | Risk-like (dice + hidden cards), poker |

This matters technically: imperfect-information games need **per-player views** of state (see `web-game-board`), and hotseat play needs a handoff screen.

## 3. By board / space topology
| Topology | Examples | Data structure |
|---|---|---|
| Square grid | Chess, Stratego-like | 2D array, `(file, rank)` |
| Hex grid | Wargames | Axial/cube coordinates |
| Graph / area map | Risk-like, Ticket to Ride | Nodes (territories) + adjacency edges |
| Track | Monopoly, Snakes & Ladders | Ordered list / ring |
| Continuous space | Pitfall-like, Asteroids | Positions + collision |
| Screen-flip rooms | Pitfall, Adventure (2600) | Room index + neighbor links |

Grid and graph boards both reduce to **graphs**; prefer one adjacency API so pathfinding/AI/highlighting code is shared.

## 4. By interaction / goal (board game families)
- **Abstract strategy** — no theme needed, perfect info: Chess, Go, Othello.
- **Wargame / skirmish** — combat resolution, units with stats: Stratego-like.
- **Area control / conquest** — majority or elimination over regions: Risk-like.
- **Race** — first to goal: Backgammon.
- **Economic / engine-building** — grow resources.
- **Deduction / bluffing** — hidden info is the game.
- **Cooperative** — players vs system.

## 5. By arcade/console genre (1970s–80s)
- **Fixed shooter** — Space Invaders, Galaxian.
- **Maze** — Pac-Man.
- **Platformer** — Donkey Kong, Pitfall!, Mario.
- **Multi-directional shooter** — Asteroids, Robotron.
- **Vehicle / vector 3D** — Battlezone, Tempest.
- **Paddle / ball** — Pong, Breakout.
- **Adventure** — Adventure (2600): rooms, items, keys.

## 6. Mechanic vocabulary (building blocks)
Movement patterns, capture (displacement, jump, custodial), combat resolution (deterministic rank, dice, cards), hidden information, setup/deployment phases, reinforcement, area majority, timers, lives, scoring tables, power-ups, room transitions.
Reference: *Building Blocks of Tabletop Game Design* (Engelstein & Shalev); BoardGameGeek's mechanic list.

## 7. Where the target projects sit
| Project | Time | Info | Topology | Primary fun | Key skills |
|---|---|---|---|---|---|
| 3D Chess (hotseat) | Turn | Perfect, deterministic | Square grid | Challenge, fellowship | chess, board-3d, hotseat |
| Stratego-like | Turn | Imperfect, deterministic | Square grid + lakes | Discovery, bluff, expression (setup) | board-3d, hotseat |
| Risk-like | Turn | Partly hidden, stochastic | Graph map | Narrative, fellowship, challenge | board-3d, hotseat |
| Pitfall-like | Real-time | Perfect (per screen) | Screen-flip rooms | Challenge, discovery, fantasy | platformer-2d, canvas-2d, retro-fidelity |
