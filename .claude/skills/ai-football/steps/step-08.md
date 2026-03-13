---
step: 8
name: Testing & Polish
requires: integrated game (step 7)
produces: test suite, README, gold master
---

# Step 8: Testing & Polish

## Purpose

Validate the complete game end-to-end. Run all gates. Fix failures. Produce a gold master ready for release.

## Instructions

1. **Unit tests per component:**
   - State engine: validate all transitions, boundary conditions
   - Playbook: personnel counts, formation rules, play existence
   - Resolution: query returns results, broadening works, outcomes have required fields
   - Coordinators: return recommendations, stay in character
   - Narration: produce non-empty output, include situational details

2. **Integration tests:**
   - Full play cycle: situation → recommendation → call → resolve → narrate → update
   - Drive sequence: multiple plays until score/turnover/punt
   - Quarter transition: clock expires → new quarter
   - Game completion: play until game over, verify final state

3. **Gate validation:**
   - Run all 26 gates from gate-contract.md
   - Every gate must PASS
   - Run 8 anti-functional tests — all must CORRECTLY REJECT bad data

4. **Edge cases to test:**
   - 4th and goal from the 1 → touchdown on 1-yard gain
   - Two-minute warning trigger
   - Overtime rules (if implemented)
   - Zero timeouts remaining
   - Safety (tackled in own end zone)
   - Turnover on downs (4th down failure)

5. **Polish:**
   - README with install instructions, quick start, project structure
   - Clean up debug output, remove development scaffolding
   - Consistent code formatting
   - Error messages are user-friendly

## Verification

| Gate | Check |
|------|-------|
| All 26 gates | Must PASS |
| All 8 anti-tests | Must CORRECTLY REJECT |
| STRUCT-01 | All spec files present |

## Output

- Test suite covering all components
- README.md
- Gold master — tested, validated, ready for release
