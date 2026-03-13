---
name: pre-build
type: checkpoint
parent: ai-football
---

# Pre-Build Readiness Checkpoint

Run these checks before starting the build pipeline.

## Checklist

| # | Check | How to Verify | Required |
|---|-------|---------------|----------|
| 1 | Python installed (3.10+) | `python --version` | Yes |
| 2 | pandas available | `python -c "import pandas"` | Yes |
| 3 | SQLite available | `python -c "import sqlite3"` | Yes |
| 4 | Internet access (for nflfastR download) | `curl -I https://github.com` | Yes (Step 1 only) |
| 5 | Disk space >= 5 GB | `df -h .` | Yes |
| 6 | SKILL.md read | Agent has read identity + vocabulary | Yes |
| 7 | workflow.md read | Agent knows step sequence | Yes |
| 8 | gate-contract.md read | Agent knows quality gates | Yes |
| 9 | Existing domain pack available | Check path to ai-football-domain-pack | Recommended |
| 10 | LLM API key configured | Check for ANTHROPIC_API_KEY or equivalent | Step 5 only |

## If Check Fails

- Checks 1-3: Install Python and dependencies first
- Check 4: Download nflfastR data manually, place in data/raw/
- Check 5: Free up disk space or use external drive
- Check 10: Skip LLM coordinators, use rule-based fallback
