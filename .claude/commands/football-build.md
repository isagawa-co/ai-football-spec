# /football-build

Build the AI Football game from scratch using the 8-step pipeline.

## Instructions

1. **Read spec files:**
   - `.claude/skills/ai-football/SKILL.md`
   - `.claude/skills/ai-football/workflow.md`
   - `.claude/skills/ai-football/gate-contract.md`

2. **Run pre-build checkpoint:**
   - Read `steps/pre-build.md`
   - Verify all 10 checks pass

3. **Execute build pipeline:**
   - Step 1: Data Setup → `franchise.db`
   - Step 2: Game State → state engine + schema
   - Step 3: Playbook → `playbook.json` + validator
   - Step 4: Resolution → nflfastR query engine
   - Step 5: Coordinators → OC/DC personas
   - Step 6: Narration → broadcast engine
   - Step 7: Integration → game loop
   - Step 8: Testing → full validation

4. **On failure at any step:**
   - Read `steps/on-failure.md`
   - Fix → retry (max 3)
   - If fix involves a lesson, invoke `/kernel/learn`

5. **On completion:**
   - All 26 gates must PASS
   - All 8 anti-tests must CORRECTLY REJECT
   - Invoke `/kernel/complete`
