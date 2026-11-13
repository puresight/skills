# Fun & Design Principles

"Fun" is not one thing. These frameworks give vocabulary to name it, design for it, and test for it.

## 1. MDA — Mechanics, Dynamics, Aesthetics
(Hunicke, LeBlanc, Zubek, 2004)

- **Mechanics** — the rules and components (move one piece per turn; roll dice to attack).
- **Dynamics** — behavior that emerges when players use the rules (bluffing, alliances, rushing).
- **Aesthetics** — the emotional responses (tension, discovery, triumph).

Designers build mechanics → hoping for dynamics → to produce aesthetics. Players experience it in reverse. **Always state the target aesthetic first**, then pick mechanics that create it.

### The eight kinds of fun (MDA aesthetics)
| Aesthetic | Feeling | Classic example |
|---|---|---|
| Sensation | Sensory pleasure | Juicy explosions, satisfying piece "clack" |
| Fantasy | Make-believe | Being a jungle explorer |
| Narrative | Drama unfolding | A comeback from one territory |
| Challenge | Obstacle course | Precise jumps over a pit |
| Fellowship | Social bonds | Hotseat banter |
| Discovery | Exploring the unknown | Revealing a hidden piece |
| Expression | Self-discovery, style | Choosing a setup formation |
| Submission | Pastime, relaxation | Calm, repetitive loops |

## 2. Flow
(Csikszentmihalyi) Engagement peaks when **challenge matches skill**. Too hard → anxiety; too easy → boredom. Tools: difficulty ramps, adaptive AI, optional challenges, handicaps for mismatched human players.

## 3. Core loop & layers
- **Core loop** (seconds): the action repeated most — jump/run/avoid; select/move/resolve.
- **Session loop** (minutes): a level, a round, a game.
- **Meta loop** (days): high scores, unlocks, campaign progress.
If the core loop isn't fun with gray boxes, no art will save it. Prototype it first.

## 4. Meaningful choices
(Sid Meier: "a game is a series of interesting decisions.") A choice is interesting when:
- options are **different**, not just bigger/smaller,
- there are **trade-offs** (risk vs reward, short vs long term),
- the player has **enough information** to reason, but not certainty,
- the outcome **matters** and is **visible**.

## 5. Uncertainty — where tension comes from
(Greg Costikyan, *Uncertainty in Games*)
| Source | Example |
|---|---|
| Hidden information | Unknown piece ranks |
| Randomness | Dice battles |
| Opponent's mind | Bluffing, reading intent |
| Performative skill | Will I nail this jump? |
| Analytic complexity | Chess calculation |
Chess = analytic + opponent. Stratego-like = hidden info + bluff. Risk-like = randomness + diplomacy. Pitfall-like = performative + discovery.

## 6. Feedback & "juice"
- Every input gets an immediate response (sound, motion, highlight) within ~100 ms.
- Exaggerate important events: screen shake, slow-mo, particles, rising tones.
- Communicate state constantly: whose turn, what's legal, what's at risk.
- Juice amplifies good mechanics; it can't fix bad ones.

## 7. Game balance precepts
- **Positive feedback** (winners snowball) ends games; **negative feedback** (catch-up) keeps them close. Risk-like games need some brake on runaway leaders.
- **Dominant strategies** kill choice — playtest for them.
- **Symmetric** starts are fair but can be dry; **asymmetric** starts are rich but need tuning.
- **First-player advantage** — mitigate (komi, setup phases, simultaneous reveals).
- **Downtime** — in multiplayer turn games, keep idle players engaged (watching dice, defending).

## 8. Learnability
- "Easy to learn, hard to master" (Bushnell's Law — Nolan Bushnell, Atari).
- Teach by doing: first screen = first action. Pitfall teaches by letting you fall.
- Show, don't tell: highlight legal moves instead of explaining movement rules.
- Progressive disclosure: introduce one mechanic at a time.

## 9. Constraints breed creativity
Atari 2600 games ran in 128 bytes of RAM and 4 KB of ROM. Pitfall's 255 screens came from a polynomial counter, not stored maps. Constraint forces focus on the essential pleasure — a useful discipline for prototypes and client MVPs.

## 10. Precepts (short list)
1. Name the target aesthetic before the mechanic.
2. Prototype the core loop ugly; keep only what's fun.
3. Every choice needs a trade-off.
4. Make state legible; make feedback immediate.
5. Tension needs uncertainty; fairness needs information.
6. Respect the player's time (fast restarts, short turns, save/resume).
7. Playtest with someone else; watch, don't explain.

## Further reading
- Hunicke, LeBlanc, Zubek — *MDA: A Formal Approach to Game Design and Game Research* (2004)
- Raph Koster — *A Theory of Fun for Game Design*
- Jesse Schell — *The Art of Game Design: A Book of Lenses*
- Tracy Fullerton — *Game Design Workshop*
- Greg Costikyan — *Uncertainty in Games*
- Steve Swink — *Game Feel*
- Katie Salen & Eric Zimmerman — *Rules of Play*
