# Weekly Ops Report Skill

Orchestrates the data pull → format → verify → email pipeline.

## Configuration

Required in routine prompt:

```
PLATFORM: ghl
GHL_API_KEY: {your-api-key}
EMAIL_TO: {your-email}
WORKSPACE_SCOPE: all
```

Optional:

```
STYLE_RULES: (ban_words, date_format, length_limit, etc.)
```

## Execution Flow

```
1. Validate config (API key, email, platform)
   ↓
2. Load appropriate adapter (adapters/ghl.md)
   ↓
3. Call adapter with GHL_API_KEY
   → Returns normalized data
   ↓
4. Count items by bucket (snapshot)
   ↓
5. Format report using report-template.md
   ↓
6. Phase 3 Verification:
   - Hallucination check
   - Style check
   - Length check
   ↓
7. Send email via email-sender.md
   ↓
8. Log completion and item counts
```

## Phase 1: Config Validation

Check that:
- GHL_API_KEY is provided (not empty)
- EMAIL_TO is valid email format
- PLATFORM = "ghl"
- WORKSPACE_SCOPE is "all" or valid location ID

Fail if any missing.

## Phase 2: Data Pull & Normalization

1. Call adapters/ghl.md with GHL_API_KEY
2. Parse response (contacts, opportunities, tasks)
3. Normalize each item to standard shape
4. Group by bucket (completed, in_progress, blocked, upcoming)

Output: normalized_data with snapshot + items

## Phase 3: Verification

### Sub-phase 3a: Hallucination Check

After formatting the report:
1. Extract all item names from the report
2. Cross-check against the normalized_data.items list
3. For each item in report:
   - Must exist in normalized_data.items by name or ID
   - Fail if not found (report hallucinated an item)

### Sub-phase 3b: Style Check

Verify:
- No em-dashes (—), only hyphens (-)
- Dates in format "Mon DD" (e.g., "Jun 08")
- No markdown emphasis (bold, italic) except section titles
- If STYLE_RULES provided, check against those

### Sub-phase 3c: Length Check

- Count words in report body (exclude subject)
- If > 500 words, truncate sections (keep top 8 items, add "…and N more")
- Reformat and re-count
- If still > 500, escalate warning (report too noisy, may need tuning)

## Output

If all phases pass:

```
✓ Report generated successfully
- Completed: N
- In progress: N
- Blocked: N
- Upcoming: N
- Total items: N
- Email sent to: EMAIL_TO
```

If any phase fails, detailed error message.

## Testing Hooks

For local preview/testing, accept optional parameters:
- `preview_date` (instead of today)
- `mock_ghl_data` (for testing without real API)

Example:

```
PREVIEW_DATE: 2026-06-02
MOCK_GHL_DATA: {... sample JSON ...}
```
