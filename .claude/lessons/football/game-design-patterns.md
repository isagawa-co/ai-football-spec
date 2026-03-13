---
name: Game Design Patterns
type: seeded-lesson
domain: ai-football
---

# Game Design Patterns

## The Problem

Building a game without a structured design document leads to scope creep, incoherent systems, and balance nightmares. Even a terminal-native game with text output needs disciplined system decomposition.

## Key Patterns

### 1. Core Loop First

Define the single repeating cycle before anything else. Everything hangs off it.

- **Football:** Show situation → recommend → call play → resolve → narrate → update → loop
- **4X pattern:** Explore → Expand → Exploit → Exterminate (per turn)
- **Roguelike pattern:** Move → encounter → resolve → loot → advance

If you can't state the core loop in one sentence, the design isn't ready.

### 2. Data-Driven Design

Hard-code nothing. Every tunable value lives in a config file or database.

- Play outcomes from real data (nflfastR), not random number generators
- Formation rules from config, not if/else chains
- Persona traits from markdown files, not embedded strings

**Why:** Balancing a game with hard-coded values requires code changes. Data-driven design lets you tune without touching logic.

### 3. System Decomposition

Each game system is an independent module with a clear interface:

| System | Input | Output |
|--------|-------|--------|
| State Engine | play result | updated game state |
| Resolution | play call + state | outcome (yards, result type) |
| AI/Coordinators | state + history | recommendation |
| Narration | outcome + state | player-facing text |

**Rule:** Systems communicate through state, not direct calls. The game loop orchestrates — systems don't know about each other.

### 4. Balance Levers

Identify 3-5 variables that most affect difficulty/fun. Document them explicitly.

For ai-football:
- **Broadening threshold** — how many query results before broadening (default: 20)
- **Coordinator aggressiveness** — how often OC suggests risky plays
- **Clock speed** — seconds consumed per play type
- **Turnover probability** — base rates from real data, but adjustable

**Anti-pattern:** Tuning balance by changing resolution logic. Always tune through data/config, never through code paths.

### 5. Phased Development

Build in layers. Each phase produces a playable (if minimal) artifact:

1. **Phase 1 — Skeleton:** Core loop runs with stub data. You can "play" but outcomes are random.
2. **Phase 2 — Data:** Real data powers resolution. Outcomes feel authentic.
3. **Phase 3 — Intelligence:** AI coordinators add strategic depth.
4. **Phase 4 — Polish:** Narration, edge cases, full game flow.

**Rule:** Every phase must be testable end-to-end. Never build Phase 3 features if Phase 1 doesn't loop.

### 6. State Machine Discipline

Game states are explicit, transitions are validated, and illegal states are impossible.

```
PREGAME → KICKOFF → PLAYING → SCORING → PLAYING → HALFTIME → PLAYING → GAME_OVER
                                  ↑                                ↑
                                  └── TIMEOUT / TWO_MINUTE ────────┘
```

- Every state has exactly one owner (the state engine)
- Transitions are whitelisted — anything not listed is rejected
- State is validated after EVERY transition (the hard gate catches bugs early)

## Applying to ai-football

The existing domain pack (`D:\my_ai_projects\project_test_repos\ai-football-domain-pack`) demonstrates these patterns:

- `GAME-DESIGN.md` — Core loop definition, system boundaries
- `FRAMEWORK.md` — Architecture decisions, component interfaces
- `game-loop.md` — 10-step per-play cycle (the core loop implementation)
- `components/*/description.md` — System decomposition per module

Read these before building. They encode the "why" behind architectural choices.
