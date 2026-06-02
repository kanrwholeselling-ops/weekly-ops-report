# Email Sender

Sends the formatted report to the user's email via Gmail MCP.

## Configuration

Set in routine prompt:

```
EMAIL_TO: inmkgt@gmail.com
```

## Email Format

**Subject:** `Weekly ops report - week of Mon DD to Sun DD`

Example: `Weekly ops report - week of Mon Jun 02 to Sun Jun 08`

**From:** Claude.ai (via Gmail MCP)

**Body:** Plain text report from report-template.md

**Example:**

```
*Weekly ops report - week of Mon Jun 02 to Sun Jun 08*

*Snapshot*
• Completed: 2
• In progress: 3
• Blocked: 1
• Due next 7 days: 4

*Completed this week*
• BigCorp Renewal (closed_won) - Carol
• Stripe Webhook Fix (done) - Jordan

[... rest of report ...]
```

## Error Handling

- **Missing Gmail MCP connector**: Fail with error message directing user to Settings > Connectors
- **Invalid email address**: Fail with error
- **Mail delivery failure**: Log error, fail gracefully

## Implementation

Use the Gmail MCP to send:
- To: EMAIL_TO from config
- Subject: formatted with week dates
- Body: plain text report
- Headers: minimal (no HTML unless requested)
