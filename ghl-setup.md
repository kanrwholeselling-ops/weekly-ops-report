# GoHighLevel Setup SOP

How to wire up GoHighLevel as the data source for the weekly ops report.

**Estimated time: 20 minutes**

## Prerequisites

- Claude.ai Pro/Max/Team account
- GoHighLevel account with API access
- Gmail account (to receive reports)

## Step 1: Get GoHighLevel API Key

1. Log into GoHighLevel
2. Go to **Settings > API & Integrations > API Keys**
3. Click **Create New API Key**
4. Name: `Weekly Ops Report`
5. Select scopes:
   - `contacts.readonly`
   - `opportunities.readonly`
   - `tasks.readonly`
   - `locations.readonly`
6. Copy the API key (you'll need it in a moment)

**⚠️ Keep this key secret — don't share it or commit it to GitHub**

## Step 2: Connect Gmail MCP to Claude.ai

1. Go to claude.ai > **Settings > Connectors**
2. Search for **Gmail**
3. Click **Connect**
4. Sign in with your Gmail account (inmkgt@gmail.com)
5. Authorize Claude.ai to send emails
6. Once connected, you'll see a checkmark next to Gmail

## Step 3: Create the Claude.ai Scheduled Routine

1. Go to claude.ai/code/scheduled
2. Click **New scheduled task**
3. Fill in:
   - **Name**: `Weekly Ops Report - GHL`
   - **Repositories**: Paste your skill repo URL (https://github.com/kanrwholeselling-ops/weekly-ops-report)
   - **Schedule**: Weekly, Monday, 7:00 AM (your timezone)
   - **Connectors**: Gmail only (GHL API key goes in prompt, not connectors)
4. **Prompt**: Copy from `ROUTINE_PROMPT_TEMPLATE.txt` and fill in:
   - Replace `{GHL_API_KEY}` with your actual API key
   - Replace `{YOUR_EMAIL}` with inmkgt@gmail.com
5. Click **Create**

## Step 4: Test Before Monday

1. Click **Run now** on the routine detail page
2. Wait for execution (30-60 seconds)
3. Check:
   - Session shows no errors
   - Email arrived in your inbox
   - Report contains items from your GHL account
   - Snapshot counts look right

**If no email arrived:** Open the session and look for errors (usually "Gmail connector not authorized" or "invalid API key").

**If email arrived but says "failed":** Check the session for the actual error — usually API key issues or invalid config.

## Step 5: Adjust if Needed

If the report format doesn't match your preferences:
- Edit `report-template.md` in your GitHub repo
- Push to GitHub
- Click **Run now** again to see changes

## Troubleshooting

### "API Key not valid"
- Double-check you copied the full key (no extra spaces)
- Verify the key hasn't expired in GHL settings
- Recreate the key if unsure

### "Gmail connector not found"
- Go to claude.ai > Settings > Connectors
- Make sure Gmail is connected and shows a checkmark
- Disconnect and reconnect if issues persist

### "No items in report"
- Check that GHL_API_KEY has the right scopes (all four: contacts, opportunities, tasks, locations)
- Verify you have active opportunities/tasks in GHL (not all completed/archived)
- Check the session for API errors

### "Report looks wrong" (wrong people, wrong stages, missing items)
- Items are grouped by stage → bucket mapping in `adapters/ghl.md`
- Verify your GHL stages match standard names (discovery, negotiation, closed_won, closed_lost)
- If you use custom stages, edit `adapters/ghl.md` to add mappings

### Routine stopped running
- Check claude.ai/code/scheduled to see if "Repeats" is toggled off
- If API key expired in GHL, recreate it and update the routine prompt
- Check the last session for error messages

## After Setup

✓ Report runs every Monday 7 AM
✓ Arrives in your inbox automatically
✓ See past runs at claude.ai/code/scheduled/[routine name]
✓ Edit format anytime by pushing changes to GitHub

Enjoy your weekly ops intelligence!
