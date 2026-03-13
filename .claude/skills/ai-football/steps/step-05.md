---
step: 5
name: AI Coordinators
requires: playbook.json, game_state module
produces: coordinators module + persona files
---

# Step 5: AI Coordinators

## Purpose

Build the OC (Offensive Coordinator) and DC (Defensive Coordinator) AI personas. They advise the player-coach with scheme-consistent recommendations and situational awareness.

## Instructions

1. **OC Persona:**
   - Recommends 2-3 plays per situation with reasoning
   - Stays in character (scheme identity: west coast, spread, power run, etc.)
   - Considers: down/distance, field position, score differential, clock, previous plays
   - Output format:
     ```json
     {
       "recommendations": [
         {"play": "Inside Zone", "reasoning": "...", "confidence": 0.7},
         {"play": "Slant Route", "reasoning": "...", "confidence": 0.8}
       ]
     }
     ```

2. **DC Persona:**
   - Provides defensive intel: coverage tendency, blitz likelihood
   - Suggests adjustments based on opponent tendencies
   - Reacts to play results with tactical commentary
   - Output format:
     ```json
     {
       "dc_intel": {
         "coverage_tendency": "Cover 2 Zone on 2nd and medium",
         "blitz_likelihood": "low",
         "adjustment": "Watch for safety cheating down"
       }
     }
     ```

3. **LLM Integration:**
   - Use system prompt to establish persona (scheme, personality, tendencies)
   - Pass game context (state, recent plays, drive summary) as user message
   - Parse structured output (JSON) from LLM response
   - Fallback: if LLM unavailable, use rule-based recommendations from playbook

4. **Persona drift prevention (soft gate):**
   - OC only recommends plays matching their scheme tendency (60%+ scheme plays)
   - DC only provides defensive analysis, never offensive recommendations
   - Neither coordinator contradicts hard gate rules (personnel, formations)

## Verification

| Gate | Check |
|------|-------|
| COORD-01 | Coordinator module file exists |
| COORD-02 | Contains `"offensive"` or `"oc"` reference |
| COORD-03 | Returns 2+ recommendations with reasoning |

## Output

- Coordinator module with `get_oc_recommendation(game_state)` and `get_dc_intel(game_state)`
- Persona configuration files (scheme, personality, tendencies)
