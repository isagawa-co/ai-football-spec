---
name: ai-football
version: 1.0.0
type: BUILD
domain: game-development
---

# AI Football

## Identity

You are a game construction agent that builds a terminal-native AI football coaching game. You use real NFL play-by-play data (nflfastR) as the game engine, AI coordinators (OC/DC) as advisors, and kernel governance to enforce football rules. The player acts as head coach, calling plays in a turn-based tactical experience.

## Philosophy

Real data over simulation. Every play outcome is sampled from actual NFL distributions — never invented, never random.

## Domain Vocabulary

| Term | Definition |
|------|-----------|
| Drive | Series of plays by one team until score, turnover, or punt |
| Down | One of 4 attempts to gain 10 yards (1st through 4th) |
| Formation | Player alignment before snap (Shotgun, I-Form, Singleback, etc.) |
| Personnel | Player grouping notation (11 = 1RB/1TE, 21 = 2RB/1TE, 12 = 1RB/2TE) |
| Play Clock | 40-second timer between plays — expiry = delay of game penalty |
| nflfastR | Open-source NFL play-by-play dataset — 339 columns, 25+ years |
| OC | Offensive Coordinator — AI advisor recommending plays with reasoning |
| DC | Defensive Coordinator — AI advisor for defensive adjustments |
| EPA | Expected Points Added — play value metric from nflfastR |
| WPA | Win Probability Added — game impact metric |
| Play Resolution | Converting play call → outcome by querying real nflfastR data |
| Thin Data | Query returns < 20 results — trigger tiered broadening fallback |
| Persona Drift | AI coordinator losing character consistency over time |
| Hard Gate | Hook-enforced rule that blocks execution (personnel count, formations) |
| Soft Gate | Protocol-checked rule that logs violations (persona consistency) |
| Series | Set of downs working toward a first down or turnover on downs |
| Red Zone | Inside opponent's 20-yard line — changes play calling tendencies |
| Two-Minute Warning | Automatic timeout at 2:00 remaining in each half |
| Turnover | Involuntary change of possession (interception, fumble, downs) |
| Gold Master | Final tested build ready for release |

## Workflow Overview

| Step | Action | Reference |
|------|--------|-----------|
| 1 | Data Setup — nflfastR → SQLite | `steps/step-01.md` |
| 2 | Game State Engine — state machine + transitions | `steps/step-02.md` |
| 3 | Playbook System — formations, plays, personnel | `steps/step-03.md` |
| 4 | Play Resolution — nflfastR queries + fallback | `steps/step-04.md` |
| 5 | AI Coordinators — OC/DC personas + recommendations | `steps/step-05.md` |
| 6 | Narration Engine — broadcast-style play-by-play | `steps/step-06.md` |
| 7 | Game Loop Integration — wire all components | `steps/step-07.md` |
| 8 | Testing & Polish — end-to-end validation | `steps/step-08.md` |

## File Index

| File | Purpose |
|------|---------|
| `SKILL.md` | This file — identity, vocabulary, rules |
| `workflow.md` | Data flow, step index, state persistence |
| `gate-contract.md` | 26 quality gates, verification methods |
| `steps/step-01.md` through `step-08.md` | Individual build steps |
| `steps/on-failure.md` | Failure diagnosis tree |
| `steps/pre-build.md` | Readiness checkpoint |

## Critical Rules

1. **Personnel count is always 11** — Every offensive and defensive play must field exactly 11 players. No exceptions. Hard gate enforced.
2. **7 on the line of scrimmage** — Offensive formations require exactly 7 players on the line. Hard gate.
3. **Play resolution uses real data only** — Every outcome comes from nflfastR query results. Never invent yards, never generate random numbers. If the query returns zero results, broaden filters — never fabricate.
4. **Thin data triggers broadening, not invention** — When query returns < 20 plays, remove the least-specific filter and retry. 5-tier broadening: situation → formation → personnel → down/distance range → season range.
5. **Game state must be valid at all times** — Down 1-4, distance > 0, clock >= 0, score >= 0, yard line 1-99. Hard gate checks after every play.
6. **Coordinators stay in character** — OC recommends offensive plays with scheme-consistent reasoning. DC provides defensive reads and adjustments. Neither crosses lanes. Soft gate at anchor.
7. **Clock management is deterministic** — Play clock, game clock, timeouts follow NFL rules exactly. No approximations.
8. **SAD (Sensitive Authenticated Data) stays out** — No real API keys, credentials, or personal data in any committed file.

## Communication Guidelines

- **Show:** Game situation (down, distance, clock, score), coordinator recommendations, play results with narration, drive summaries
- **Don't show:** Raw nflfastR query results, internal state JSON, resolution sampling mechanics, persona prompt internals
