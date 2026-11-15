# Gravity Splash: Initial Project Plan (G1)

**Owner:** Rebecca Zhao (Producer) · **Version:** Initial, G1. This plan moves into Asana after G1, where owners, acceptance criteria, dependencies, and the full Risk Register follow at G2/G3.

## Gate dates

| Date | Gate / milestone |
|---|---|
| Nov 9 | First-round pitch (done) |
| Nov 16 | G1 review (live for 6 to 8 students; others reviewed from recording) |
| Nov 17 | G1 decision (Approve or Hold) posted as a comment on the post |
| Nov 19 | Two Braintrust replies due |
| Nov 23 | Graded G1 feedback returned |
| **TBD** | **G2 Prototype** (shares a review with G3 Production) |
| **TBD** | G3 Production, scope lock |

## First tasks through G2

Order follows the pitch: movement, gravity, and paint first, then the generator and validator. The Hold condition (a solver proving one generated level is completable) is task 5, so it is checked early.

| # | Task | GDF role | Outcome (done when) | Target |
|---|---|---|---|---|
| 1 | Set up repo, run a blank game in the browser | Tech Lead | The game window runs in the browser | 11/16 |
| 2 | Player movement and jump | Tech Lead / Design Lead | Player moves and jumps with acceptable feel | 11/18 |
| 3 | Gravity flip | Tech Lead | Player walks on the ceiling after a flip | 11/20 |
| 4 | Paint layer with red, blue, yellow | Tech Lead / Design Lead | Each color changes physics as designed | 11/22 |
| 5 | Level format and solver for one level | Tech Lead | Solver proves one level is completable (Hold condition) | 11/24 |
| 6 | Build 3 hand-made levels | Design Lead | Three fallback levels, playable start to exit | [date] |
| 7 | Gemini call that returns a level | Tech Lead | A level in the agreed format returns from the API | [date] |
| 8 | Validator, retry limit, and fallback | Tech Lead / QA | Invalid level triggers retry, then fallback | [date] |
| 9 | Telemetry summary to the agent | Tech Lead / BI | Agent receives deaths and colors used per section | [date] |
| 10 | Measure valid-level rate, latency, cost | QA / BI | Numbers recorded and compared with the budgets | [date] |
| 11 | Playtest with an external player | Design Lead / BI | Initial Playtest Log written | [date] |
| 12 | Update GDD, Configuration, plan, Risk Register | Producer | Update versions in the repo | [date] |
| 13 | Prepare executable extension artifact (agent) | Tech Lead | Agent ships as the required executable artifact | [date] |

## What G2 needs (from the course reader)

First playable with its agent and a recording, Initial Playtest Log, Update GDD and Configuration Document, updated plan and Risk Register, and at least one external player observed using the core loop.

## If time runs short

Cut yellow moving platforms first, then the third fallback level.
