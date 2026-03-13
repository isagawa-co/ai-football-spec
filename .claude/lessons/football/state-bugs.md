---
name: Game State Edge Cases
type: seeded-lesson
domain: ai-football
---

# Game State Bugs

## The Problem

Football has dozens of state transitions with edge cases. Off-by-one errors in down counting, clock management bugs, and incorrect possession swaps are the most common game-breaking bugs.

## Anti-Patterns

- **Down counter off-by-one:** Incrementing down BEFORE checking for first down. If a play gains enough yards, down goes to 2 instead of resetting to 1.
- **Clock not stopping:** Incomplete passes, out of bounds plays, and penalties should stop the clock. Running clock after these events makes the game unrealistic.
- **Touchdown yard line bug:** If a player is at yard_line 8 and gains 10 yards, the result is a touchdown — but the yard_line calculation might show -2 instead of triggering the score.
- **Turnover field position:** After an interception, the new offense starts at the yard_line where the INT happened, not mirrored. `new_yard_line = 100 - interception_yard_line`.
- **Timeout deduction without check:** Allowing timeout when team has 0 remaining.

## Correct Approach

1. Check first down BEFORE incrementing down: `if yards_gained >= distance: reset_first_down() else: down += 1`
2. Check for touchdown BEFORE updating yard_line: `if yard_line - yards_gained <= 0: touchdown()`
3. Stop clock on: incomplete pass, out of bounds, penalty, timeout, two-minute warning, scoring play, change of possession
4. On turnover: `new_yard_line = 100 - turnover_yard_line`
5. Validate state after EVERY transition — the hard gate catches bugs early
6. Test boundary conditions: 4th and goal from the 1, 99-yard touchdown, overtime, zero timeouts
