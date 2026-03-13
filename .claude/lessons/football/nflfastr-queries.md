---
name: nflfastR Query Patterns
type: seeded-lesson
domain: ai-football
---

# nflfastR Query Patterns

## The Problem

nflfastR has 339 columns per play and 25+ years of data. Queries that are too specific return zero results. Queries that are too broad return meaningless distributions.

## Anti-Patterns

- **Over-filtering:** Querying with 5+ filters (formation AND personnel AND down AND distance AND yardline AND score_differential) often returns < 10 plays. Broadening then loses all specificity.
- **Ignoring play_type_nfl vs play_type:** The `play_type` column is the cleaned version. Use it, not `play_type_nfl`.
- **Not handling NULLs:** Many columns have NULL values (e.g., `pass_length` is NULL for run plays). Filter appropriately.
- **Season bias:** Recent seasons have different play distributions (more passing, more shotgun). Weight recent data or filter to last 5 years for current tendencies.

## Correct Approach

1. Start with 2-3 filters: `play_type`, `down`, `ydstogo` range (±2)
2. Add situation filters only if sample > 50
3. Use the 5-tier broadening strategy when sample < 20
4. Log the broadening level so you know how specific the data was
5. For rare plays (trick plays, QB sneaks on 3rd and long), accept broader data — it's better than inventing outcomes
