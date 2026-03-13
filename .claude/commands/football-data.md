# /football-data

Set up or refresh the nflfastR data pipeline.

## Instructions

1. **Check current data:**
   - Does `franchise.db` exist?
   - What seasons are loaded?
   - When was last update?

2. **Download new data:**
   - Fetch latest season from nflfastR GitHub releases
   - Import into `franchise.db`
   - Rebuild indexes

3. **Verify data integrity:**
   - Check row counts per season
   - Verify key columns exist
   - Run sample queries to confirm data quality

4. **Report:**
   ```
   Data Status:
   - Database: franchise.db (X.X GB)
   - Seasons: 2000-2024
   - Total plays: X,XXX,XXX
   - Last updated: YYYY-MM-DD
   ```
