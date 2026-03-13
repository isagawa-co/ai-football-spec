---
step: 3
name: Playbook System
requires: NFL formation rules
produces: playbook.json + validation module
---

# Step 3: Playbook System

## Purpose

Build the playbook — formations, plays, personnel packages, and defensive coverages. Every play the user can call lives here.

## Instructions

1. **Define formations:**
   - I-Form: 7 on line, 2 backs, 2 receivers
   - Singleback: 7 on line, 1 back, 3 receivers
   - Shotgun: 7 on line, 1 back (offset), 3 receivers
   - Under Center: 7 on line, 1 back, 3 receivers
   - Empty: 7 on line, 0 backs, 5 receivers

2. **Define personnel packages:**
   - 11 personnel: 1 RB, 1 TE, 3 WR (spread)
   - 12 personnel: 1 RB, 2 TE, 2 WR (balanced)
   - 21 personnel: 2 RB, 1 TE, 2 WR (heavy)
   - 22 personnel: 2 RB, 2 TE, 1 WR (goal line)
   - 10 personnel: 1 RB, 0 TE, 4 WR (four wide)
   - **Rule: Every package totals exactly 11 players (5 OL + QB + package)**

3. **Define v1 playbook (20 offensive plays):**
   - 8 run plays: HB Dive, HB Toss, Power Run, Inside Zone, Outside Zone, Counter, QB Sneak, Draw
   - 12 pass plays: Slant, Curl, Out, Deep Post, Play Action, Screen, Hitch, Mesh, Four Verts, TE Seam, Bootleg, Fade
   - Each play: id, name, type, formation, personnel, description

4. **Define 5 defensive coverages:**
   - Cover 1 Man, Cover 2 Zone, Cover 3 Zone, Cover 4 Zone, Blitz

5. **Validation function:**
   - `validate_play(play)` → checks personnel count == 11, formation has 7 on line
   - `validate_formation(formation)` → checks on_line == 7

## Verification

| Gate | Check |
|------|-------|
| PLAY-01 | `playbook.json` exists |
| PLAY-02 | Contains `"play_type"` field |
| PLAY-03 | All plays have personnel_count == 11 |
| PLAY-04 | All formations have on_line == 7 |

## Output

- `playbook.json` — all plays, formations, coverages
- Playbook module with validation functions
