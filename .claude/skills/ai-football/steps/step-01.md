---
step: 1
name: Data Setup
requires: nflfastR source URL
produces: franchise.db (SQLite)
---

# Step 1: Data Setup

## Purpose

Download and import NFL play-by-play data (nflfastR) into a local SQLite database. This is the foundation — every play resolution queries this database.

## Instructions

1. **Download nflfastR data:**
   - Source: `https://github.com/nflverse/nflverse-data/releases/download/pbp/play_by_play_{year}.parquet`
   - Download seasons 2000-2024 (25 years of data)
   - Use Python `requests` or `urllib` for download
   - Store Parquet files in `data/raw/`

2. **Create SQLite database:**
   - Database: `franchise.db`
   - Table: `play_by_play`
   - Import all Parquet files using pandas `read_parquet()` → `to_sql()`

3. **Key columns to index:**
   - `play_type` (run, pass, punt, field_goal, etc.)
   - `down` (1-4)
   - `ydstogo` (yards to first down)
   - `yardline_100` (yards from opponent end zone)
   - `shotgun`, `no_huddle`, `qb_scramble`
   - `pass_length`, `pass_location`, `run_location`, `run_gap`
   - `epa`, `wpa`, `wp`, `score_differential`

4. **Create indexes:**
   - `idx_play_type` on `play_type`
   - `idx_down_distance` on `(down, ydstogo)`
   - `idx_situation` on `(down, ydstogo, yardline_100, play_type)`

5. **Create config file:**
   - `data_config.json` — database path, table name, seasons, key columns, index definitions

## Verification

| Gate | Check |
|------|-------|
| DATA-01 | `franchise.db` exists |
| DATA-02 | `data_config.json` contains `"pbp_table"` |
| DATA-03 | Query returns data within timeout |

## Failure Modes

- **Download fails:** Retry 3x, then provide manual download URL
- **Parquet parse error:** Check pandas version, try alternative parser
- **Disk space:** 25 years of data ~ 2-3 GB. Warn if insufficient space.

## Output

- `franchise.db` — SQLite database with indexed play-by-play data
- `data_config.json` — Configuration for database access
