Here's the section to add to your spec doc. You can paste this in as a new **Step 6** at the bottom, just before the "Ambiguity rules" section:

---

## Step 6 — Update the Google Sheet

After rendering the HTML dashboard artifact, update the persistent tracking spreadsheet in Google Drive.

**Spreadsheet ID:** `[PASTE YOUR SHEET ID HERE ONCE CREATED]`

If the spreadsheet cannot be found, create a new one titled "Miro Release Tracker — Data" and update this spec with the new ID.

### How to update

Use the Google Drive tools to overwrite the sheet with fresh data on every refresh. The sheet has a single tab called **"Features"**. Clear all rows below the header on every run, then write the full current feature list from scratch. Do not append — always overwrite.

### Column structure

Write these columns in this exact order, with row 1 as a frozen header row:

| Column | Header | Notes |
|---|---|---|
| A | Feature Name | Plain text |
| B | Channel | #product_news or #product_review |
| C | Priority Product | Engage / AI Workflows / Roadmaps / Prototypes / blank |
| D | Current Milestone | Plain text |
| E | Current Milestone Date | Plain text, preserve approximate dates as-is |
| F | Next Milestone | Plain text |
| G | Next Milestone Date | Plain text, preserve approximate dates as-is |
| H | Delay Status | One of: No delay / Date slipped / Milestone regressed / No recent updates |
| I | Delay Note | The plain-text explanation of the delay, or blank |
| J | Last Updated | ISO date format: YYYY-MM-DD |
| K | Last Updated (Readable) | Human-readable, e.g. "Apr 23, 2026" |
| L | Slack Link | Permalink URL to the most recent message for this feature |
| M | Refreshed At | The current date and time this row was written, ISO format |

### Formatting rules

- Row 1 (header): bold, light grey background (#f3f3f3), frozen
- Rows with Delay Status of "Date slipped": light yellow background (#fff8e1) on the Delay Status cell (column H)
- Rows with Delay Status of "Milestone regressed": light red background (#fce4ec) on the Delay Status cell
- Rows with Delay Status of "No recent updates": light grey background (#f5f5f5) on the Delay Status cell
- All other rows: no fill
- Column A (Feature Name): bold text
- Freeze the top row so it stays visible when scrolling

### After updating the sheet

Share the Google Sheet link with the user alongside the dashboard summary message, e.g.:

> "Dashboard updated. [View the Google Sheet →](link)"

---

A couple of things to do after adding this to the doc:

1. **Create the Sheet first.** Either make it manually in Google Drive and paste the ID into the spec, or on your first refresh just tell Claude "create the tracking spreadsheet too" and it will make one and record the ID for future runs.

2. **Update the Project Instructions** to mention the sheet step. Add one line: "After building the HTML artifact, also update the persistent Google Sheet per Step 6 of the spec."
