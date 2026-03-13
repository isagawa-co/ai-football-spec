---
name: ai-football-gates
type: gate-contract
parent: ai-football
total_gates: 26
anti_tests: 8
---

# AI Football — Gate Contract

## Verification Methods

| Method | How orchestrator checks |
|--------|------------------------|
| `file_exists` | File exists at specified path |
| `grep` | Search file content for specific pattern |
| `run_code` | Execute Python code and check result |
| `manual` | Orchestrator reads and judges structural integrity |

## Data Setup Gates (Step 1)

| ID | Check | Method | Pass Criteria | Fail Action |
|----|-------|--------|---------------|-------------|
| DATA-01 | Database file exists | `file_exists` | `franchise.db` exists | Run data setup script |
| DATA-02 | Play-by-play table exists | `grep` | `data_config.json` contains `"pbp_table"` | Create table from nflfastR |
| DATA-03 | Indexes created | `run_code` | Query returns data within timeout | Add indexes on key columns |

## Game State Gates (Step 2)

| ID | Check | Method | Pass Criteria | Fail Action |
|----|-------|--------|---------------|-------------|
| STATE-01 | State file exists | `file_exists` | `game_state.json` exists | Create initial state file |
| STATE-02 | All required fields present | `grep` | `game_state.json` contains `"quarter"` | Add missing fields |
| STATE-03 | Valid state transitions | `run_code` | Down 1-4, distance > 0, yard_line 1-99, clock >= 0 | Fix transition logic |

## Playbook Gates (Step 3)

| ID | Check | Method | Pass Criteria | Fail Action |
|----|-------|--------|---------------|-------------|
| PLAY-01 | Playbook file exists | `file_exists` | `playbook.json` exists | Create playbook |
| PLAY-02 | Minimum plays present | `grep` | `playbook.json` contains `"play_type"` | Add plays to playbook |
| PLAY-03 | Personnel counts valid | `run_code` | All plays have exactly 11 offensive players | Fix personnel assignments |
| PLAY-04 | Formations legal | `run_code` | All formations have 7 on line of scrimmage | Fix formation definitions |

## Resolution Gates (Step 4)

| ID | Check | Method | Pass Criteria | Fail Action |
|----|-------|--------|---------------|-------------|
| RES-01 | Resolution engine exists | `file_exists` | `resolution.py` or resolution module exists | Create resolution engine |
| RES-02 | nflfastR query pattern present | `grep` | Resolution module contains `"SELECT"` or query function | Implement query logic |
| RES-03 | Broadening fallback works | `run_code` | Thin data query triggers broadening, returns >= 20 results | Fix broadening logic |
| RES-04 | Outcome includes yards gained | `run_code` | Resolution returns dict with `yards_gained` key | Fix result schema |

## Coordinator Gates (Step 5)

| ID | Check | Method | Pass Criteria | Fail Action |
|----|-------|--------|---------------|-------------|
| COORD-01 | Coordinator module exists | `file_exists` | `coordinators.py` or coordinator module exists | Create coordinator module |
| COORD-02 | OC persona defined | `grep` | Coordinator module contains `"offensive"` or `"oc"` | Define OC persona |
| COORD-03 | Play recommendations generated | `run_code` | OC returns list of 2+ recommended plays with reasoning | Fix recommendation logic |

## Narration Gates (Step 6)

| ID | Check | Method | Pass Criteria | Fail Action |
|----|-------|--------|---------------|-------------|
| NAR-01 | Narration module exists | `file_exists` | `narration.py` or narration module exists | Create narration module |
| NAR-02 | Broadcast style present | `grep` | Narration module contains situation-describing function | Add narration templates |
| NAR-03 | Result narration works | `run_code` | Given play result dict, returns narrative string with yardage | Fix narration output |

## Integration Gates (Step 7)

| ID | Check | Method | Pass Criteria | Fail Action |
|----|-------|--------|---------------|-------------|
| LOOP-01 | Game entry point exists | `file_exists` | `game.py` or main game module exists | Create game loop module |
| LOOP-02 | All components imported | `grep` | Game module imports state, playbook, resolution, narration | Add missing imports |
| LOOP-03 | Game loop executable | `run_code` | Game initializes without error, creates starting state | Fix initialization |

## Structural Gate

| ID | Check | Method | Pass Criteria | Fail Action |
|----|-------|--------|---------------|-------------|
| STRUCT-01 | Spec structural integrity | `manual` | All skill files, commands, lessons, step files present | Create missing files |

## Anti-Functional Tests

Tests that verify gates CORRECTLY REJECT bad data:

| # | Test Name | Target Gate | Mutation | Expected |
|---|-----------|-------------|----------|----------|
| 1 | Invalid personnel count | PLAY-03 | Set offensive players to 10 | FAIL |
| 2 | Illegal formation | PLAY-04 | Set line players to 6 | FAIL |
| 3 | Invalid game state | STATE-03 | Set down to 5 | FAIL |
| 4 | Zero query results | RES-03 | Query with impossible filters | FAIL (triggers broadening) |
| 5 | Missing yards in result | RES-04 | Remove `yards_gained` from result | FAIL |
| 6 | Empty play recommendations | COORD-03 | Return empty recommendation list | FAIL |
| 7 | Missing narration output | NAR-03 | Return empty string from narration | FAIL |
| 8 | Game loop crash on init | LOOP-03 | Remove required state file | FAIL |

## Anti-Functional Test Code

```python
# Anti-test verification functions

def check_play03(data):
    """All plays must have exactly 11 offensive players."""
    for play in data.get("plays", []):
        if play.get("personnel_count") != 11:
            return "FAIL"
    return "PASS"

def check_play04(data):
    """All formations must have 7 on line of scrimmage."""
    for formation in data.get("formations", []):
        if formation.get("on_line") != 7:
            return "FAIL"
    return "PASS"

def check_state03(data):
    """Game state must be valid: down 1-4, distance > 0, yard_line 1-99."""
    gs = data.get("game_state", {})
    if gs.get("down", 0) < 1 or gs.get("down", 0) > 4:
        return "FAIL"
    if gs.get("distance", 0) <= 0:
        return "FAIL"
    if gs.get("yard_line", 0) < 1 or gs.get("yard_line", 0) > 99:
        return "FAIL"
    return "PASS"

def check_res03(data):
    """Resolution must return >= 20 results after broadening."""
    return "PASS" if data.get("sample_size", 0) >= 20 else "FAIL"

def check_res04(data):
    """Resolution result must include yards_gained."""
    return "PASS" if "yards_gained" in data.get("result", {}) else "FAIL"

def check_coord03(data):
    """OC must return 2+ recommendations with reasoning."""
    recs = data.get("recommendations", [])
    if len(recs) < 2:
        return "FAIL"
    for r in recs:
        if not r.get("reasoning"):
            return "FAIL"
    return "PASS"

def check_nar03(data):
    """Narration must return non-empty string with yardage."""
    narration = data.get("narration", "")
    return "PASS" if len(narration) > 10 else "FAIL"

def check_loop03(data):
    """Game must initialize with valid starting state."""
    gs = data.get("game_state", {})
    required = ["quarter", "clock", "down", "distance", "yard_line", "possession", "score"]
    for field in required:
        if field not in gs:
            return "FAIL"
    return "PASS"
```

## Autonomy Rules

- **Fully autonomous:** Data setup, state engine, playbook, resolution, narration, game loop
- **Ask before:** Choosing tech stack (Python vs Rust), LLM provider selection, playbook size beyond v1 (20 plays)

## Stop Conditions

- **Max retries per gate:** 3
- **Escalation:** After 3 failures on same gate, log issue and skip to next step
- **Hard stop:** If franchise.db cannot be created (Step 1), abort — all subsequent steps depend on it
