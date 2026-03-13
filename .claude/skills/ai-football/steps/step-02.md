---
step: 2
name: Game State Engine
requires: data_config.json
produces: game_state module + JSON schema
---

# Step 2: Game State Engine

## Purpose

Build the state machine that tracks every aspect of a football game — down, distance, clock, score, possession, timeouts, and drive progress.

## Instructions

1. **Define state schema:**
   ```json
   {
     "quarter": 1,
     "clock": "15:00",
     "down": 1,
     "distance": 10,
     "yard_line": 25,
     "possession": "home",
     "score": {"home": 0, "away": 0},
     "timeouts": {"home": 3, "away": 3},
     "drive_plays": 0,
     "drive_yards": 0,
     "play_clock": 40,
     "two_minute_warning": false
   }
   ```

2. **Implement state transitions:**
   - **After play:** Update down (+1), distance (yards remaining), yard_line, clock
   - **First down:** Reset to down=1, distance=10
   - **Touchdown:** Score +6, trigger PAT/2pt decision, kickoff
   - **Turnover:** Swap possession, set yard_line to 100 - current
   - **Quarter end:** Reset clock to 15:00 (or 0:00 for halftime)
   - **Field goal:** Score +3, kickoff
   - **Safety:** Score +2, free kick

3. **Validation rules (hard gates):**
   - Down: 1-4 only
   - Distance: > 0
   - Yard line: 1-99
   - Clock: >= "00:00"
   - Score: >= 0 for both teams
   - Timeouts: 0-3 per team per half

4. **Special transitions:**
   - Two-minute warning: auto-timeout at 2:00 in each half
   - Turnover on downs: 4th down failure → swap possession
   - Touchback: Kick into end zone → ball at 25-yard line

## Verification

| Gate | Check |
|------|-------|
| STATE-01 | `game_state.json` exists |
| STATE-02 | Contains `"quarter"` field |
| STATE-03 | All state values within valid ranges |

## Output

- Game state module with `create_initial_state()`, `update_state()`, `validate_state()`
- `game_state.json` — initial state template
