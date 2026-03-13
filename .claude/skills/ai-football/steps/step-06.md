---
step: 6
name: Narration Engine
requires: resolution module, game_state module
produces: narration module
---

# Step 6: Narration Engine

## Purpose

Build broadcast-style play-by-play narration that brings each play to life. The narration transforms raw yards_gained into an immersive football experience.

## Instructions

1. **Narration components:**
   - **Pre-snap:** Describe the situation (down, distance, field position, clock)
   - **Action:** Describe the play as it happens (snap, handoff/throw, catch/run)
   - **Result:** Describe the outcome (yards gained, first down, touchdown, etc.)
   - **Post-play:** Updated game state, next situation

2. **Broadcast tone:**
   - Use active voice, present tense during the play
   - Include player position references ("the quarterback", "the slot receiver")
   - Vary sentence structure — not every play sounds the same
   - Add tension for big moments (red zone, two-minute drill, 4th down)

3. **Situational awareness:**
   - Red zone: heightened language ("inside the 20", "scoring territory")
   - Two-minute drill: clock urgency ("racing against the clock")
   - Big plays (15+ yards): excitement ("breaks free!", "wide open!")
   - Turnovers: dramatic shift ("intercepted!", "the ball is loose!")
   - Touchdowns: celebration language ("touchdown!", "they score!")

4. **Template system:**
   - Create templates for common situations (run, short pass, deep pass, sack, INT)
   - Fill with specific details from play result
   - Randomize template selection to avoid repetition

5. **Output format:**
   ```
   "Second and seven from the 35. Williams takes the snap from shotgun,
   fires a quick slant to the slot receiver — caught! He breaks a tackle
   at the 40 and picks up 12 yards for a first down!"
   ```

## Verification

| Gate | Check |
|------|-------|
| NAR-01 | Narration module file exists |
| NAR-02 | Contains situation-describing function |
| NAR-03 | Given play result, returns narrative string with yardage info |

## Output

- Narration module with `narrate_play(play_result, game_state)` function
- Template collection for different play types and situations
