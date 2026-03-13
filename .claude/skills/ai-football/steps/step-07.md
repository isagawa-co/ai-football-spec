---
step: 7
name: Game Loop Integration
requires: all component modules (steps 1-6)
produces: game.py main loop
---

# Step 7: Game Loop Integration

## Purpose

Wire all components into the main game loop — the 10-step per-play cycle that IS the game.

## Instructions

1. **Game initialization:**
   - Load franchise.db connection
   - Load playbook.json
   - Create initial game state (kickoff)
   - Initialize coordinator personas
   - Display game intro (teams, venue, conditions)

2. **10-step play loop:**
   ```
   1. show_situation()    → Display down, distance, clock, score, field position
   2. oc_recommend()      → OC suggests 2-3 plays with reasoning
   3. player_call()       → Read user input (play name or number)
   4. gate_check()        → HARD: personnel=11, 7 on line, play clock valid
   5. resolve_play()      → Query nflfastR, sample outcome
   6. narrate_result()    → Broadcast-style play-by-play
   7. update_state()      → Down, distance, clock, score, stats
   8. dc_react()          → DC provides defensive commentary
   9. check_transitions() → Touchdown? Turnover? Quarter end? Game over?
   10. loop_or_end()      → Continue to next play or end game
   ```

3. **Transition handling:**
   - **Touchdown:** PAT/2pt choice → kickoff → swap possession
   - **Turnover (INT/fumble):** Narrate turnover → swap possession → set yard line
   - **Punt:** 4th down choice → punt → swap possession at landing spot
   - **Field goal:** Attempt → success/miss → kickoff or swap possession
   - **Quarter end:** Clock reset, swap sides after Q1/Q3, halftime after Q2
   - **Game over:** Final score, game summary, stats

4. **User input parsing:**
   - Accept play name: "slant route", "hb dive"
   - Accept play number: "1", "OFF-06"
   - Accept special: "timeout", "punt", "field goal", "spike"
   - Fuzzy match on partial names: "slant" → "Slant Route"

5. **Error handling:**
   - Invalid play name → show available plays, re-prompt
   - Gate violation → show violation, re-prompt (no penalty unless play clock)
   - LLM timeout → use rule-based fallback for coordinators

## Verification

| Gate | Check |
|------|-------|
| LOOP-01 | Game entry point file exists |
| LOOP-02 | Imports state, playbook, resolution, narration modules |
| LOOP-03 | Game initializes without error, creates starting state |

## Output

- `game.py` (or equivalent entry point) — complete game loop
- CLI argument handling (--teams, --difficulty, --season)
