# Gravity Splash: Game Design Document (Initial, G1)

**Owner:** Rebecca Zhao (Creative Director / Design Lead) · **Version:** Initial, G1 · **Date:** November 2026

**Status labels.** Every item at G1 is **Planned**. Later versions will mark items **Implemented** or **Verified**. Unresolved questions are marked **OPEN** and collected in Section 16.

---

## 1. Overview

**The X.** A 2D platformer where you flip gravity and paint a colorless world to unlock abilities, so you feel like a creative problem-solver in challenging levels.

**Player and motivation.** Fans of Mario, Celeste, and puzzle-platformers. Motivations: experimentation, mastery, discovery.

**Observable hypothesis.** If it works, in a playtest players voluntarily retry a level using a different color/gravity combination and say things like "what if I…". If it fails, they repeat one safe trick or stop experimenting.

**Genre:** 2D puzzle-platformer. **Platform:** web browser. **Engine/language:** Python **[OPEN: Pygame/Arcade, how it runs in the browser; see Section 16]**.

## 2. Pillars

1. **Creative Expression:** the player chooses what to paint and when, and several routes can work.
2. **Visual Transformation:** the world starts colorless and visibly comes to life as the player paints.
3. **Fair Challenge:** a level is always completable, and failure is quick to recover from.

## 3. Core loop

| Step | What happens |
|---|---|
| **Act** | Jump, flip gravity, or paint a surface. |
| **Respond** | The surface and physics change (Section 4). |
| **Interpret** | The player reads the new physics and works out a route. |
| **Next action** | The player tries the route, fails or succeeds, and the loop repeats until the exit is reached. |

## 4. Mechanics

**Movement.** Run left/right and jump, with Mario-style jump feel and Celeste-style tight controls. **[OPEN: exact values]**

**Gravity flip.** Gravity has two states: down (floor) and up (ceiling). Flipping lets the player walk on ceilings. **[OPEN: any flip cooldown or restricted zones]**

**Paint.** The player paints a surface or object. Each color has one effect:

| Color | Effect |
|---|---|
| Red | Surface becomes bouncy. |
| Blue | Surface becomes slippery. |
| Yellow | Activates moving platforms. |

**Why the systems multiply.** Every color behaves differently depending on gravity direction. For example, red on a ceiling bounces the player downward, and blue on a ceiling lets the player slide the other way after a flip. A few simple rules produce many different puzzles.

## 5. Dynamics and intended aesthetics

- **Dynamics:** the player combines paint and gravity to reach otherwise unreachable places, and tests ideas by trial and error.
- **Aesthetics:** discovery, expression, and the satisfaction of color returning to a gray world.

## 6. Progression

Five generated levels, each ending at an exit, with three hand-made fallback levels held in reserve. Colors are unlocked by progression (**assumption**: red first, then blue, then yellow). The set of unlocked colors is part of world state and is given to the level generator. **[OPEN: confirm the unlock order and whether painting an object is what unlocks the ability]**

## 7. Win, failure, checkpoints, scoring

- **Win:** reach the exit.
- **Failure:** touching a hazard or falling out of bounds.
- **Recovery:** respawn at the nearest checkpoint with no penalty beyond lost time (forgiving checkpoints, as in Celeste).
- **Scoring:** none. Deaths, time, and colors used are recorded for telemetry only.

## 8. Controls and feedback

**Controls (placeholder).** Move: arrow keys or A/D. Jump: Space. Flip gravity: F. Select color: 1/2/3. Paint: [key/click]. **[OPEN]**

**Feedback.** A painted surface changes from gray to its color immediately. Surfaces show their effect (bounce animation, slide trail). Gravity flips with a short visual cue. Failure and respawn are quick and clear.

## 9. Edge cases

- The player paints a surface that already has paint (overwrite or block). **[OPEN]**
- The player flips gravity in midair or while touching a moving platform.
- Yellow moving platforms interact with a flipped player.
- The generator returns a level that is invalid or too slow: the game loads a hand-made fallback (Section 11).
- The player is stuck in an unwinnable state: allow restart from the last checkpoint.

## 10. Balance

The main balance concern is the size of the color × gravity space. The first tuning target is that a level has at least two valid routes using different color or gravity combinations, so experimentation is rewarded. Tuning starts with the hand-made levels (the Genre / Mechanics mitigation). **[OPEN: paint limits per level, number of required paints]**

## 11. AI agent: level generator

**Bounded behavior.** Generates the next level from a fixed tile and mechanics library, so it can only rearrange existing pieces.

**Experience it affects.** How much the player experiments. The agent aims at color/gravity combinations this player has not yet tried.

**Scripted baseline.** A hand-made level sequence of the same length.

**Playtest question.** Do players with generated levels try more distinct color/gravity combinations than players with the scripted sequence?

**What the player loses without the agent.** Everyone gets the same path. A player who never uses blue never gets a level that rewards it.

### World state versus what the agent knows

| World state (owned by the game) | Agent context (what the agent receives) |
|---|---|
| Tile grid, paint layer per surface, gravity direction, player position, unlocked colors | Level format spec, the unlocked ability set, and **summarized** telemetry (deaths per section, colors used, combinations tried) |
| | **Not given:** live physics frames and raw player input |

**Agent action.** Proposes the next level as structured data.

### Level format (draft sketch, OPEN)

```json
{
  "id": "gen-03",
  "size": [40, 18],
  "tiles": ["...fixed library ids placed on the grid..."],
  "paint_targets": [{"id": "p1", "pos": [12, 7], "allowed": ["red", "blue"]}],
  "gravity_zones": [],
  "hazards": [],
  "exit": [38, 3]
}
```

### Validation and fallback

1. A solver or simulated playthrough checks that the exit is reachable using only unlocked abilities.
2. If validation fails, regenerate (maximum 3 attempts).
3. After the third failure, load a hand-made fallback level. The game must remain playable if the model is unavailable.

The validator, not the agent, decides what loads.

## 12. Content scope

- **Included:** 3 colors, gravity flip, 5 generated levels, 3 hand-made fallback levels, one art theme.
- **Excluded:** combat, multiplayer, level editor, story, additional colors.
- **Cut first:** yellow moving platforms, then the third fallback level.

## 13. Budgets (basis: Gemini 3.5 Flash-Lite at $0.30 in / $2.50 out per 1M tokens)

- **Cost:** about $0.03 expected and about $0.16 worst case per session (cap $0.20). Basis: 5 levels × 3k tokens in / 2k out; worst case 4k out and 3 attempts per level.
- **Latency:** about 3 s per attempt (about 1.2k tokens at about 400 tokens/s), prefetched during the previous level; about 15 s worst case before fallback.
- Throughput comes from a third-party benchmark and **must be verified in the prototype**.

## 14. References and attribution

| Source | Borrow / Change | Terms |
|---|---|---|
| *Super Mario* | Jump feel and level pacing; I add gravity flip and paint | Copyrighted. No assets or code used; inspiration only |
| *Celeste* | Forgiving checkpoints and tight controls | Copyrighted. No assets or code used; inspiration only |
| *VVVVVV* | Gravity-flip moment; I change it by layering paint on top | Copyrighted. Mechanic idea only; no code or assets |
| Python and libraries | **[OPEN: list the ones used]** | **[Confirm each license before use]** |
| Google Gemini API | Level generation | Google's API terms |
| Art and audio | **[Original / asset pack]** | **[License]** |

## 15. A valid play session (example)

The player starts level 1 on a gray floor with red unlocked. A gap is too wide to jump. They paint a red patch on the floor and bounce across. Level 2 introduces blue and a ceiling corridor, so they flip gravity, walk the ceiling, and paint blue to slide to the exit. If they die, they respawn at the checkpoint. After each level the game records deaths and colors used, and the generator uses that summary to propose the next level, which the validator checks before it loads.

## 16. Open questions

1. Which Python library (Pygame/Arcade), and how does it run in the browser (pygbag, Pyodide, or a backend)?
2. Exact movement and jump values; whether gravity flip has a cooldown.
3. How painting works: selection, limits per level, and overwriting existing paint.
4. Color unlock order, and whether painting is what unlocks an ability.
5. The level format and the solver approach (graph search over position, gravity, and paint state?).
6. The telemetry fields the agent receives, and how "untried combinations" are defined.
7. Art style and asset sources.
8. How many levels a playtest session covers.
