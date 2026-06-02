# Weekly Ops Report Agent

A Claude AI skill that pulls business metrics from GoHighLevel, formats them into a structured intelligence report, and emails every Monday at 7 AM.

## What It Does

Pulls from GoHighLevel:
- **Opportunities** (pipeline deals by stage)
- **Tasks** (assigned to workspace)
- **Contacts** (added in past 7 days)

Organizes into report sections:
- Snapshot (high-level metrics)
- Completed this week
- In progress
- Blocked / at risk
- Upcoming (next 7 days)

Emails to your inbox every Monday 7 AM.

## Quick Start

1. [Set up connector and routine](./ghl-setup.md) (20 minutes)
2. Click "Run now" to test
3. Confirm email arrives
4. Edit `report-template.md` to customize format (optional)

## Files

| File | Purpose |
|------|---------|
| `SKILL.md` | Orchestration & verification logic |
| `report-template.md` | Report format & section caps |
| `email-sender.md` | Gmail delivery |
| `adapters/ghl.md` | GoHighLevel API adapter |
| `ghl-setup.md` | Setup & troubleshooting |
| `ROUTINE_PROMPT_TEMPLATE.txt` | What to paste in Claude.ai |

## Customization

- **Change report format**: Edit `report-template.md`, push to GitHub
- **Change schedule/time**: Edit routine at claude.ai/code/scheduled
- **Filter by location/team**: Edit routine prompt
- **Add new fields**: Extend `adapters/ghl.md`

## Monitoring

Each run creates a session at claude.ai/code/scheduled. Check the first 4 Mondays for errors; after that, trust it.

## Issues

See `ghl-setup.md` for troubleshooting.
