# AI Football Spec

Claude Code domain spec for building a terminal-native AI football coaching game powered by real NFL data.

## What This Is

A drop-in `.claude/` directory that teaches Claude Code to build a conversational AI football game. The player acts as head coach, calling plays with AI coordinators (OC/DC) providing tactical advice. Every play outcome is resolved by querying 25+ years of real NFL play-by-play data (nflfastR).

## Install

```bash
# Clone into your project
git clone https://github.com/isagawa-co/ai-football-spec.git

# Copy the .claude directory into your project root
cp -r ai-football-spec/.claude /path/to/your/project/
```

## Quick Start

1. Start Claude Code in your project
2. Run `/football-build` for the full 8-step build pipeline
3. Run `/football-play` to start a game after building

## Project Structure

```
.claude/
├── skills/ai-football/
│   ├── SKILL.md              # Agent identity, vocabulary, rules
│   ├── workflow.md            # 8-step build flow, 3-tier data architecture
│   ├── gate-contract.md       # 24 quality gates, verification methods
│   └── steps/
│       ├── step-01.md         # Data Setup (nflfastR → SQLite)
│       ├── step-02.md         # Game State Engine
│       ├── step-03.md         # Playbook System (20 plays, 5 formations)
│       ├── step-04.md         # Play Resolution Engine
│       ├── step-05.md         # AI Coordinators (OC/DC)
│       ├── step-06.md         # Narration Engine
│       ├── step-07.md         # Game Loop Integration
│       ├── step-08.md         # Testing & Polish
│       ├── on-failure.md      # Failure diagnosis tree
│       └── pre-build.md       # Readiness checkpoint
├── commands/
│   ├── football-build.md      # Full 8-step build pipeline
│   ├── football-play.md       # Start a game
│   └── football-data.md       # Data setup/refresh
└── lessons/
    ├── lessons.md             # Topic index
    └── football/
        ├── nflfastr-queries.md    # Query patterns, thin data handling
        ├── persona-drift.md       # Coordinator consistency
        └── state-bugs.md         # Game state edge cases
```

## Game Architecture

**3-tier data model:**
- **Behavior** (Markdown) — Persona prompts, playbook rules, lessons
- **Facts** (SQLite) — 25+ years of nflfastR play-by-play data
- **Live State** (JSON) — Current game state, updated every play

**10-step game loop:** Show situation → OC recommends → Player calls → Gate check → Resolve play → Narrate result → Update state → DC reacts → Check transitions → Loop

## Commands

| Command | Purpose |
|---------|---------|
| `/football-build` | Build the game from scratch (8-step pipeline) |
| `/football-play` | Start a new game |
| `/football-data` | Set up or refresh nflfastR data |

## Validation

24/24 functional gates passed. 8/8 anti-functional tests correctly rejected bad data.

## Built With

[Isagawa Spec Factory](https://github.com/isagawa-co/domain-spec-factory)
