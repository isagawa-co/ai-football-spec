---
name: on-failure
type: failure-handling
parent: ai-football
---

# On Failure — Diagnosis Tree

## Step 1: Data Setup
- **Download fails:** Check URL, retry 3x, provide manual download link
- **Import fails:** Check pandas version, verify Parquet format, check disk space
- **Index creation fails:** Check SQL syntax, verify column names exist

## Step 2: Game State
- **Invalid transition:** Log current → attempted state, check rules in step-02
- **Missing field:** Compare against schema, add default value
- **Range violation:** Hard gate blocks — fix the transition logic, not the gate

## Step 3: Playbook
- **Personnel != 11:** Count QB(1) + OL(5) + package — must total 11
- **Formation violation:** Check on_line count, verify 7 on line of scrimmage
- **Missing play:** Check playbook.json against required 20 plays

## Step 4: Resolution
- **Zero results:** Check SQL query filters, verify franchise.db has data for the filters
- **Broadening fails:** Check each tier removes the correct filter
- **Missing yards_gained:** Check result schema mapping from nflfastR columns

## Step 5: Coordinators
- **Empty recommendations:** Check LLM prompt, verify game state is passed correctly
- **Persona drift:** Check system prompt for scheme boundaries, add guardrails
- **LLM timeout:** Implement rule-based fallback (top 3 plays by EPA for situation)

## Step 6: Narration
- **Empty output:** Check template system, verify play result is passed
- **Wrong situation:** Check game state is read before narration, not after update
- **Repetitive:** Add more templates, randomize selection

## Step 7: Integration
- **Import errors:** Check module paths, verify all components export expected functions
- **Loop hangs:** Add timeout per play (30s), force state transition
- **State corruption:** Validate state after every update, rollback on violation

## Escalation Matrix

| Severity | Action |
|----------|--------|
| Gate fails, fix is obvious | Fix and retry (max 3) |
| Gate fails, cause unclear | Read step file, check inputs, add logging |
| Multiple gates fail | Check upstream dependency (is franchise.db valid?) |
| 3 retries exhausted | Log issue, skip to next step, note gap |
| Hard gate violation | STOP — never bypass. Fix the logic. |
