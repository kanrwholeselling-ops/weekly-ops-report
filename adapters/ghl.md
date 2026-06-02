# GoHighLevel Adapter

Fetches data from GoHighLevel API and normalizes to standard report shape.

## Configuration

Set these in the routine prompt:

```
PLATFORM: ghl
GHL_API_KEY: {your-api-key}
WORKSPACE_SCOPE: all
```

## API Calls

1. **Contacts** (GET `/contacts`)
   - Filter: created_at >= 7 days ago
   - Extract: id, name, email, created_at

2. **Opportunities** (GET `/opportunities`)
   - Extract: id, name, stage, owner_name, updated_at, due_date, value
   - Filter: updated_at >= 7 days ago (for reporting) OR due_date <= 7 days out

3. **Tasks** (GET `/tasks`)
   - Extract: id, title, assignee_name, status, due_date, created_at
   - All tasks (filter in normalization phase)

## Normalization Rules

### Opportunity Buckets

```javascript
if (opportunity.stage === "Won" || opportunity.stage === "closed_won") {
  bucket = "completed";
} else if (opportunity.stage === "Lost" || opportunity.stage === "closed_lost") {
  bucket = "blocked";
  reason = "Lost";
} else if (opportunity.due_date && daysUntilDue(opportunity.due_date) <= 7) {
  bucket = "upcoming";
} else {
  bucket = "in_progress";
}
```

### Task Buckets

```javascript
if (task.status === "completed" || task.status === "done") {
  bucket = "completed";
} else if (task.due_date && isPast(task.due_date)) {
  bucket = "blocked";
  reason = "Overdue";
} else if (task.due_date && daysUntilDue(task.due_date) <= 7) {
  bucket = "upcoming";
} else {
  bucket = "in_progress";
}
```

### Standard Shape Output

Each item normalizes to:

```javascript
{
  id: string,
  type: "opportunity" | "task" | "contact",
  name: string,
  bucket: "completed" | "in_progress" | "blocked" | "upcoming",
  owner: string,
  stage_or_status: string,
  days_in_stage: number,
  due_date: "YYYY-MM-DD" or null,
  reason: string or null (for blocked items)
}
```

## Error Handling

- **Missing API key**: Log error, fail with setup instructions
- **Rate limit (429)**: Log warning, return partial data
- **Invalid stage**: Skip item, log warning
- **Network timeout**: Retry once, then fail with error message

## Date Math

- ISO timestamp → "X days ago" (if past) or "X days until" (if future)
- Examples: "5 days ago", "2 days until", "today"

## Sample Response

```json
{
  "contacts": [
    {"id": "c1", "name": "Alice Chen", "email": "alice@co.com", "created_at": "2026-05-30T10:00:00Z"}
  ],
  "opportunities": [
    {
      "id": "opp1",
      "name": "Acme Deal",
      "stage": "negotiation",
      "owner_name": "Bob Smith",
      "value": 50000,
      "updated_at": "2026-06-01T15:00:00Z",
      "due_date": "2026-06-15"
    },
    {
      "id": "opp2",
      "name": "BigCorp Won",
      "stage": "closed_won",
      "owner_name": "Carol Davis",
      "value": 100000,
      "updated_at": "2026-06-01T09:00:00Z",
      "due_date": null
    }
  ],
  "tasks": [
    {
      "id": "task1",
      "title": "Follow up with Acme",
      "assignee_name": "Bob Smith",
      "status": "open",
      "due_date": "2026-06-08",
      "created_at": "2026-05-28T10:00:00Z"
    }
  ]
}
```

## Normalized Output Example

```json
{
  "snapshot": {
    "completed_count": 1,
    "in_progress_count": 1,
    "blocked_count": 0,
    "upcoming_count": 2
  },
  "items": [
    {
      "id": "opp2",
      "type": "opportunity",
      "name": "BigCorp Won",
      "bucket": "completed",
      "owner": "Carol Davis",
      "stage_or_status": "closed_won",
      "days_in_stage": 0,
      "due_date": null,
      "reason": null
    },
    {
      "id": "opp1",
      "type": "opportunity",
      "name": "Acme Deal",
      "bucket": "in_progress",
      "owner": "Bob Smith",
      "stage_or_status": "negotiation",
      "days_in_stage": 5,
      "due_date": "2026-06-15",
      "reason": null
    },
    {
      "id": "task1",
      "type": "task",
      "name": "Follow up with Acme",
      "bucket": "upcoming",
      "owner": "Bob Smith",
      "stage_or_status": "open",
      "days_in_stage": 5,
      "due_date": "2026-06-08",
      "reason": null
    },
    {
      "id": "c1",
      "type": "contact",
      "name": "Alice Chen",
      "bucket": "upcoming",
      "owner": null,
      "stage_or_status": "new",
      "days_in_stage": 3,
      "due_date": null,
      "reason": "New contact"
    }
  ]
}
```
