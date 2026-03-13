# /football-play

Start a new football game.

## Instructions

1. **Verify game is built:**
   - Check that all components exist (game.py, franchise.db, playbook.json)
   - If not built, suggest `/football-build` first

2. **Configure game:**
   - Select home and away teams (or use defaults)
   - Set difficulty (affects coordinator advice quality)
   - Set season year for data queries

3. **Launch game loop:**
   - Initialize game state (kickoff)
   - Enter the 10-step play loop
   - Continue until game ends (4 quarters complete)

4. **Post-game:**
   - Display final score and key stats
   - Drive summary with play-by-play highlights
   - Option to play again or exit
