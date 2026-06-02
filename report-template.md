# Report Template

Formats normalized GHL data into the weekly ops report.

## Report Structure

```
*Weekly ops report - week of Mon DD to Sun DD*

*Snapshot*
• Completed: N
• In progress: N
• Blocked: N
• Due next 7 days: N

*Completed this week*
• [name] ([type]) - [owner]
• ...max 8 items...

*In progress*
• [name] ([type]) - [owner], [stage/status]
• ...max 8 items...

*Blocked / at risk*
• [name], blocked [N days], [reason]
• ...max 8 items...

*Upcoming this week*
• [name], due [date] - [owner]
• ...max 8 items...
```

## Formatting Rules

**Names:**
- Opportunities: use `name` field
- Tasks: use `title` field
- Contacts: use `name` field with "(new contact)" label

**Owners:**
- Show first name only if available (e.g., "Bob" not "Bob Smith")
- If no owner, show "—" or omit

**Dates:**
- Format as "Mon DD" (e.g., "Jun 08", "Jun 15")
- No year
- Upcoming items show due date; blocked items show days (e.g., "overdue 3 days")

**Types:**
- Opportunities: show current stage (e.g., "negotiation")
- Tasks: show status (e.g., "open")
- Contacts: show "new contact"

**Truncation:**
- Max 8 items per section
- If more items, add line: "…and N more"
- Calculate "N more" as total - 8

**Length:**
- Target one page (email ~40 lines)
- If report exceeds 500 words, truncate sections proportionally

## Snapshot Calculation

Count items by bucket:
- completed: items where bucket = "completed"
- in_progress: items where bucket = "in_progress"
- blocked: items where bucket = "blocked"
- upcoming: items where bucket = "upcoming"

## Example Output

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

*In progress*
• Acme Integration (discovery) - Bob, 5 days
• Q3 OKR Planning (in progress) - Sam, 3 days
• Mobile App v2 (in development) - Jordan, 12 days

*Blocked / at risk*
• Office Lease (waiting) - Wei, blocked 11 days

*Upcoming this week*
• Acme Proposal due Jun 08 - Bob
• Task review due Jun 09 - Carol
• BigCorp call due Jun 10 - Sam
• Q3 Kickoff due Jun 12 - Alice
```

## Implementation Notes

- Always show 4 snapshot metrics, even if zero
- Each section title is bolded with asterisks: `*Title*`
- Bullet point prefix: `• ` (bullet + space)
- No markdown emphasis (em-dash, bold, italic) except section titles
- Dates and numbers are plain text
