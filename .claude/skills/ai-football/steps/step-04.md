---
step: 4
name: Play Resolution Engine
requires: franchise.db, playbook.json
produces: resolution module
---

# Step 4: Play Resolution Engine

## Purpose

Build the engine that converts a play call into a game outcome by querying real NFL data. This is the core game mechanic — every yard gained or lost comes from actual NFL play distributions.

## Instructions

1. **Query construction:**
   - Base query: `SELECT yards_gained, epa, wpa, pass_length, pass_location, run_location, run_gap FROM play_by_play WHERE play_type = ? AND down = ? AND ydstogo BETWEEN ? AND ?`
   - Add filters: formation (shotgun/under center), yardline_100 range, score differential bucket
   - Minimum sample size: 20 plays

2. **Outcome sampling:**
   - Query returns distribution of real outcomes
   - Sample one outcome randomly (weighted by frequency)
   - Return: yards_gained, play details, EPA, WPA

3. **Thin data broadening (5 tiers):**
   When query returns < 20 results, broaden in order:
   1. Remove game situation filter (red zone, two-minute, etc.)
   2. Remove formation filter
   3. Remove personnel filter
   4. Expand down/distance range (±2 yards)
   5. Expand season range (add ±2 years)
   - Log which broadening level was used
   - Never return fewer than 20 samples

4. **Result schema:**
   ```json
   {
     "yards_gained": 8,
     "play_type": "pass",
     "pass_length": "short",
     "pass_location": "middle",
     "complete_pass": 1,
     "epa": 0.42,
     "wpa": 0.02,
     "broadening_level": 0
   }
   ```

5. **Special outcomes:**
   - Interception: query `interception == 1` plays for INT probability
   - Fumble: query `fumble == 1` plays for fumble probability
   - Sack: query `sack == 1` plays for sack probability on pass plays
   - Touchdown: `yards_gained >= yards_to_endzone`

## Verification

| Gate | Check |
|------|-------|
| RES-01 | Resolution module file exists |
| RES-02 | Contains SQL SELECT or query function |
| RES-03 | Broadening returns >= 20 results |
| RES-04 | Result includes `yards_gained` key |

## Output

- Resolution module with `resolve_play(play, game_state, db)` function
- Broadening logic with 5-tier fallback
