---
name: Persona Drift
type: seeded-lesson
domain: ai-football
---

# Coordinator Persona Drift

## The Problem

LLM-driven coordinators (OC/DC) lose character consistency over long game sessions. A conservative run-first OC starts calling aggressive 4-vertical plays. A zone-coverage DC starts recommending all-out blitz every play.

## Anti-Patterns

- **No scheme enforcement:** Letting the LLM freestyle without constraining recommendations to the coordinator's scheme identity.
- **Accumulating context without reset:** Passing the entire game history to the LLM on each call. Context grows, persona signal weakens.
- **No recommendation validation:** Accepting whatever the LLM returns without checking if it matches the coordinator's tendencies.

## Correct Approach

1. Include scheme identity in every system prompt: "You are a west coast offense coordinator. 60%+ of your calls should be short-to-intermediate passes."
2. Validate recommendations against scheme: if a power-run OC recommends 3 consecutive deep passes, flag it.
3. Limit context window: pass last 5 plays, not entire game. Summarize the drive, don't replay it.
4. Use soft gates at anchor to detect drift: compare recommendation distribution to expected scheme tendencies.
5. If drift detected: reinforce persona in next prompt, or reset system prompt.
