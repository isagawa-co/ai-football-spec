---
name: ai-football-workflow
type: workflow
parent: ai-football
---

# AI Football — Workflow

## Data Flow

```
nflfastR Parquet ──→ [Step 1: Data Setup] ──→ franchise.db (SQLite)
                                                    │
                          ┌─────────────────────────┘
                          ▼
                   [Step 2: Game State] ──→ game_state.json + state engine
                          │
                          ▼
                   [Step 3: Playbook] ──→ playbook.json + play validation
                          │
                          ▼
                   [Step 4: Resolution] ──→ query engine + fallback logic
                          │
                          ▼
                   [Step 5: Coordinators] ──→ OC/DC personas + recommendations
                          │
                          ▼
                   [Step 6: Narration] ──→ broadcast-style output engine
                          │
                          ▼
                   [Step 7: Game Loop] ──→ integrated game (10-step per-play loop)
                          │
                          ▼
                   [Step 8: Testing] ──→ validated gold master
```

## Step Index

| Step | Input | Output | Fail Action |
|------|-------|--------|-------------|
| 1 | nflfastR Parquet URL | `franchise.db` with indexes | Retry download, provide manual URL, verify schema |
| 2 | State schema from design | `game_state.py` + JSON schema | Fix transition logic, validate all field ranges |
| 3 | NFL formation rules | `playbook.json` + validator | Fix personnel counts, verify 7-on-line rule |
| 4 | franchise.db + playbook | `resolution.py` with broadening | Fix SQL queries, verify broadening tiers, check sample sizes |
| 5 | Persona specs + LLM API | `coordinators.py` + persona files | Fix prompts, verify character boundaries, test recommendations |
| 6 | Play results + state | `narration.py` + templates | Fix situational awareness, verify broadcast tone |
| 7 | All components | `game.py` main loop | Fix integration points, verify 10-step cycle completes |
| 8 | Integrated game | Test suite + README | Fix failing tests, verify end-to-end game plays |

## Pre-Step Reads

| Step | Must Read First |
|------|----------------|
| 1 | `SKILL.md` (vocabulary), existing pack `data/setup.md` |
| 2 | `steps/step-02.md`, existing pack `components/game-state/description.md` |
| 3 | `steps/step-03.md`, existing pack `components/playbook/description.md` |
| 4 | `steps/step-04.md`, existing pack `components/play-resolution/description.md` |
| 5 | `steps/step-05.md`, existing pack `components/coordinators/description.md` |
| 6 | `steps/step-06.md`, existing pack `components/narration/description.md` |
| 7 | `steps/step-07.md`, existing pack `game-loop.md` |
| 8 | `gate-contract.md` |

## Game Loop (Per Play — 10 Steps)

```
1. Show Situation ──→ down, distance, clock, score, field position
2. OC Recommends  ──→ 2-3 plays with reasoning (LLM)
3. Player Calls   ──→ user input: select play or free text
4. Gate Check     ──→ HARD: personnel=11, 7 on line, play clock
5. Resolve Play   ──→ query nflfastR, sample from distribution
6. Narrate Result ──→ broadcast-style play-by-play
7. Update State   ──→ down, distance, clock, score, stats
8. DC Reacts      ──→ defensive adjustment commentary (LLM)
9. Transitions    ──→ touchdown? turnover? quarter end? timeout?
10. Loop or End   ──→ next play or game over
```

## 3-Tier Data Architecture

| Tier | Format | Purpose | Updated |
|------|--------|---------|---------|
| Behavior | Markdown | Persona prompts, playbook rules, lessons | By kernel learn loop |
| Facts | SQLite | nflfastR play-by-play, rosters, contracts | At setup + annual refresh |
| Live State | JSON | Current game state, season state | Every play |

## Thin Data Broadening (5 Tiers)

When nflfastR query returns < 20 results, broaden in order:

1. Remove specific game situation (e.g., red zone filter)
2. Remove formation filter
3. Remove personnel filter
4. Expand down/distance range (±2 yards)
5. Expand season range (add ±2 years)

Stop broadening when sample >= 20. Log the broadening level used.

## State Persistence

```json
{
  "game_state": {
    "quarter": 1,
    "clock": "15:00",
    "down": 1,
    "distance": 10,
    "yard_line": 25,
    "possession": "home",
    "score": {"home": 0, "away": 0},
    "timeouts": {"home": 3, "away": 3},
    "drive_plays": 0,
    "drive_yards": 0
  },
  "build_state": {
    "current_step": 1,
    "completed_steps": [],
    "components_built": [],
    "tests_passed": 0,
    "tests_failed": 0
  }
}
```

## Execution Rules

1. **Steps are sequential** — Step N must complete before Step N+1 begins
2. **Each step produces a testable artifact** — file, module, or config
3. **Gate check after each step** — verify output before proceeding
4. **Existing pack is reference, not template** — read it for domain knowledge, don't copy structure
5. **Data layer first** — everything depends on franchise.db existing
6. **Integration last** — wire components only after all are independently tested
7. **Gold master requires all gates green** — no skipping validation at Step 8
