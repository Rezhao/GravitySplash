# Gravity Splash: Configuration Document (Initial, G1)

**Owner:** Rebecca Zhao (Tech Lead, with Creative Director approval) · **Version:** Initial, G1

**Status labels.** Everything here is **Planned**. Nothing has been implemented or verified yet. Unresolved items are marked **OPEN** and listed in Section 8.

I am the Executive Producer and Creative Director. Agents do delegated work and make recommendations. I decide what to accept.

---

## 1. Production agent team: planned role coverage

Production agents run in Codex. Shared models are allowed. Each role below will have an executable configuration file in `agents/` (paths are planned, not yet created). Narrative and Audio are scaled down because the game has no story and a small sound vocabulary.

| Role | Executable artifact | Purpose | Context | Tools / permissions | Outputs | Recipient and handoff | Evaluation | Human approval boundary |
|---|---|---|---|---|---|---|---|---|
| **Producer** | `agents/producer.md` | Plan, scope, risks, gate evidence | GDD, plan, risk register | Read docs; edit `/docs` | Task lists, risk updates | Me, other agents | Plan matches GDD | I approve scope and commitments |
| **Design Lead** | `agents/design.md` | Mechanics, level rules, playtest questions | GDD, playtest log | Read/write GDD | Design specs, test questions | Tech Lead, QA | Playtest reveals intended behavior | I approve design changes |
| **Tech Lead** | `agents/tech.md` | Game code, validator, agent interface, builds | GDD, config, repo | Read/write code, run tests | Runnable build, tests | QA, me | Tests pass; build runs | I review before merge |
| **Art Lead** | `agents/art.md` | Gray-to-color visual direction, readability | GDD, art notes | Read docs; produce asset specs | Visual specs, assets | Tech Lead | Art Direction Peer Review (Section 5.3) | I approve the look |
| **Narrative & Audio** | `agents/audio.md` | Minimal sound/feedback cues (no story) | GDD feedback section | Read docs | Sound/cue list | Tech Lead | Cues are readable in playtest | I approve content |
| **QA** | `agents/qa.md` | Reproduce failures, verify fixes | Build, test cases | Run tests; no code merge | Bug reports, test results | Tech Lead, me | Failures reproduced, fixes verified | I decide what blocks release |
| **BI** | `agents/bi.md` | Summarize telemetry and playtest data | Telemetry, playtest log | Read data | Measures with limitations | Design Lead, me | States sample limits | I decide what findings mean |
| **Publishing & Release** | `agents/release.md` | Build instructions, packaging, archive | Build, README | Read repo; draft docs | Release instructions | Me | Another person can run the build | I approve release |

## 2. In-game agent: Level Generator

| Field | Specification |
|---|---|
| **Purpose** | Propose the next level as structured data, aimed at color/gravity combinations the player has not yet tried. |
| **Model / settings** | Gemini 3.5 Flash-Lite (`gemini-3.5-flash-lite`). Low thinking, JSON-only output. **[OPEN: temperature and thinking settings]** |
| **Prompt / system instructions** | Stored at `agents/level_generator/prompt.md` (planned). Instructs: use only the fixed tile library, output only the level JSON, use only unlocked abilities. |
| **Context sources** | Level format spec, unlocked ability set, summarized telemetry (deaths per section, colors used, combinations tried). |
| **Knowledge boundary** | The agent does **not** see live physics frames or raw player input. It cannot read or change world state. |
| **Tools / actions** | One action: return a level proposal as JSON. No tool access. |
| **Prohibited actions** | Loading a level itself, changing rules or abilities, introducing tiles outside the library. |
| **Memory policy** | None between sessions. Each call is stateless and receives the summary it needs. |
| **Validation** | A solver checks the exit is reachable using only unlocked abilities. The validator decides what loads. |
| **Timeout / retry / spending limits** | Target about 3 s per attempt. Maximum 3 attempts per level. Session cap $0.20. Fallback if the cap or timeout is exceeded. |
| **Fallback** | Load one of the 3 hand-made levels. The game stays playable if the model is unavailable. |
| **Failure modes** | Unsolvable level; invalid JSON; slow response; repeated easy levels; spending overrun. |
| **Logging** | Log each request, response, validation result, attempts, latency, and cost per level. |
| **Human override** | I can force fallback levels and disable the agent with a config flag. |
| **Version** | `v0.1-initial` |

## 3. Planned executable extension artifact

At least one agent must ship as an executable artifact. My plan: the **Level Generator** ships as a function-calling interface (a function that returns a level in the level format, with a manifest). **[OPEN: confirm the form required by the syllabus, and the manifest fields]**

## 4. Evaluation plan

- **Deterministic checks:** the validator accepts known-solvable levels and rejects known-unsolvable ones; output always matches the level format; fallback loads on forced failure and timeout.
- **Bounded live trials:** run the generator on a fixed set of telemetry summaries; record valid-level rate, attempts needed, latency, and cost.
- **Human playtest:** compare generated levels against the hand-made baseline on distinct color/gravity combinations tried (the playtest question in the GDD).
- **Key measure:** valid-level rate is the largest uncertainty. If it is low, retries raise cost and latency and fallbacks replace the agent.

## 5. Human decisions versus agent recommendations

| Item | Source | Outcome |
|---|---|---|
| Core concept: flip gravity and paint a colorless world | Mine | Kept |
| Color effects: red bouncy, blue slippery, yellow moving platforms | Mine | Kept |
| Using an AI agent to generate levels, compared with hand-made levels | Mine | Kept |
| Sharpen "what the player loses" to adapting to what this player hasn't tried | Agent | Accepted |
| Solver-based validator with hand-made fallbacks and a 3-try limit | Agent | Accepted (unsolvable levels were my biggest worry) |
| Agent knowledge boundary (summaries only) | Agent | Accepted |
| 5-second latency budget | Agent | Changed to "hidden by prefetch" after checking generation speed |

## 6. Configuration versions

| Version | Date | Change |
|---|---|---|
| `v0.1-initial` | G1 | First planned configuration |

## 7. Test commands and CI

To be added once the repository has code. Planned: `[test command]` for validator tests, and a CI workflow at `.github/workflows/[name].yml`. **[OPEN]**

## 8. Open questions

1. Which tool/platform runs the production agents?
2. The extension artifact form and manifest fields required by the syllabus.
3. Model settings (thinking level, temperature) and the prompt text.
4. How the validator is implemented (and how I will prove a generated level is completable).
5. How telemetry is summarized and passed to the agent.
6. Whether to cache the fixed tile library and format spec in the prompt to reduce cost.
