# Miro Release Dashboard — Full Spec

*Used by the Claude Project "Miro Release Dashboard" to drive automated Slack scraping, feature parsing, delay detection, and HTML artifact generation.*

---

## Channels to Scrape

| Channel | ID |
|---|---|
| #product_news | C8BJSFHAT |
| #product_review | CGNJHCD97 |

Scrape window: last 90 days.

---

## Step 1 — Slack Search Queries

Run ALL of the following searches using `slack_search_public_and_private`. Replace DATE with the date 90 days ago (YYYY-MM-DD). Collect all results before moving to Step 2.

- `in:#product_news "public beta" after:DATE`
- `in:#product_news "general availability" after:DATE`
- `in:#product_news GA release after:DATE`
- `in:#product_news mironeer release after:DATE`
- `in:#product_news "private beta" after:DATE`
- `in:#product_news "solution review" after:DATE`
- `in:#product_news shipping launched milestone after:DATE`
- `in:#product_review "milestone update" after:DATE`
- `in:#product_review "solution review" after:DATE`
- `in:#product_review "pre-release review" after:DATE`
- `in:#product_review kickoff after:DATE`

---

## Step 2 — Fetch Threads

For every message with `reply_count > 0`, call `slack_read_thread` with the message's `channel_id` and `message_ts`.

Thread replies frequently contain the most current information: date changes, milestone updates, delay acknowledgements, and follow-up announcements.

Mark each entry clearly:
- `type: "parent"` — top-level message
- `type: "thread_reply"` — reply within a thread

Limit: fetch up to 50 replies per thread.

---

## Step 3 — Parse Features

Extract one row per feature/project. A single Slack message may reference multiple features — extract each separately.

### Fields to extract

| Field | Notes |
|---|---|
| feature_name | Name of the feature or project |
| current_milestone | Current milestone (see order below) |
| current_milestone_date | Date announced, achieved, or expected |
| next_milestone | Next milestone in pipeline |
| next_milestone_date | Date for next milestone |
| channel | #product_news or #product_review |
| last_updated | Timestamp of most recent message |
| priority_product | Engage / AI Workflows / Roadmaps / Prototypes / null |
| message_history | Array of all messages referencing this feature |

### Milestone order (sequential)

1. Kick-off
2. Solution Review
3. Pre-release Review
4. Mironeer Release
5. Private Beta
6. Public Beta
7. GA

### Priority product tagging rules

Only tag when there is a clear, explicit signal. When in doubt, leave as null — a missed tag is better than a wrong one.

**Engage**
Tag when message contains: "Miro Engage", "Engage add-on", "Engage addon", "Engage monetization", "Engage initiative"
Or mentions channels: #i_miro_engage, #engage_monetization, #engage_addon, #i_miro_engage_trials, #i_miro_engage_feedback
Do NOT tag for generic "engagement" language unrelated to the Engage product.

**AI Workflows**
Tag ONLY when about the monetized Sidekicks + Flows paid product.
Keywords: "AI Workflows" (explicit), "Sidekicks & Flows" as a paid product, "AI Workflows monetization"
Or mentions channel: #tmp_ai-settings_flows_and_sidekicks
Do NOT tag for general AI features, AI analytics, AI board generation experiments, or internal AI tooling.

**Roadmaps**
Tag when message contains: "Dynamic Roadmap", "Dynamic Roadmapping", "Roadmapping", "Roadmap" (as a Miro product feature — not a team's planning doc)
Or mentions channels: #feedback_dynamic_roadmapping, #i_roadmapping_ai, #i_roadmapping_insights
Do NOT tag when "roadmap" refers to a team's internal planning document.

**Prototypes**
Tag ONLY when about the Miro Prototypes paid add-on.
Keywords: "Prototypes add-on", "Prototyping Format", "Miro Prototypes" as a product, "prototyping addon"
Or mentions channels: #prototyping_addon, #tm_prototyping, #prototyping_feedback
Do NOT tag for general prototyping concepts or design research prototypes.

### Parsing rules

- Dates may be approximate ("end of Q2", "late May") — preserve as-is, do not normalise
- Feature names are often inconsistent across messages — deduplicate using judgment; when in doubt keep separate and note the possible duplicate
- Only include features whose most recent message falls within the 90-day scrape window

---

## Step 4 — Detect Delays

Compare each feature's message history chronologically.

**Type A — Date slipped**
A previously announced milestone date has shifted to a later date in a newer message.
Flag value: `"slipped"` + note old vs. new date in delayNote field.

**Type B — Milestone regressed**
A feature appears to have moved backward in the milestone sequence in a newer message.
Flag value: `"regressed"` + note old vs. new milestone in delayNote field.

**No recent updates**
If a feature's most recent message is older than 30 days.
Flag value: `"stale"`

No delay: `"none"`

Multiple flags can apply — combine with " + ".

---

## Step 5 — Render the Dashboard

Build a single self-contained HTML file (`index.html`). No external dependencies.

### Visual design

- Font: `-apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif`
- Page background: `#f5f5f0`
- Card/table background: `#ffffff`
- Primary accent: `#ffe043` (Miro yellow)
- Body text: `#1a1a1a`
- Muted text: `#6b6b6b`

### Milestone badge colours

| Milestone | Background | Text |
|---|---|---|
| Kick-off | #e8f0fe | #1a73e8 |
| Solution Review | #e8f0fe | #1a73e8 |
| Pre-release Review | #fff3e0 | #e65100 |
| Mironeer Release | #fff3e0 | #e65100 |
| Private Beta | #f3e8ff | #7b1fa2 |
| Public Beta | #e8f5e9 | #2e7d32 |
| GA | #1a1a1a | #ffe043 |

### Delay badge colours

| Status | Icon | Background | Text |
|---|---|---|---|
| No delay | ✅ | #e8f5e9 | #2e7d32 |
| Date slipped | ⏰ | #fff8e1 | #f57f17 |
| Milestone regressed | 🔴 | #fce4ec | #c62828 |
| No recent updates | 💤 | #f5f5f5 | #757575 |

### Priority product badge colours

| Product | Background | Text |
|---|---|---|
| Engage | #fff0f6 | #c2185b |
| AI Workflows | #e8f5e9 | #1b5e20 |
| Roadmaps | #e3f2fd | #0d47a1 |
| Prototypes | #fff8e1 | #e65100 |

### Channel badge colours

| Channel | Background | Text |
|---|---|---|
| #product_news | #e3f2fd | #0d47a1 |
| #product_review | #fce4ec | #880e4f |

### Layout structure

**Header**
Miro yellow (#ffe043) background. Left: "🟡 Miro Release Tracker" title (bold, 20px). Right: "↻ Refresh" button (dark background, yellow text).

**Subtitle bar**
Light yellow (#fff8c0). Shows: Last refreshed [date] · [N] features tracked · [N] delays flagged · [date range] scanned.

**Filter bar**
White background. Dropdowns for: All Milestones / All Channels / All Statuses / All Products. Text search input (feature name only). All filters client-side with AND logic. Showing X of Y count on the right.

**Table columns**

| Column | Width | Notes |
|---|---|---|
| Feature Name | 22% | Bold |
| Channel | 10% | Small badge |
| Priority Product | 11% | Coloured pill or dash |
| Current Milestone | 14% | Badge + date below in muted text |
| Next Milestone | 14% | Badge + date below in muted text |
| Delay Status | 13% | Badge |
| Last Updated | 11% | Relative (e.g. "3 days ago") |
| Expand | 5% | Arrow toggle |

**Default sort:** Last Updated descending. Column headers clickable to sort; clicking active column reverses direction. Active sort column visually marked.

**Expanded rows**
All messages referencing the feature, newest first. Each entry: date, channel badge, author, text (truncated at 300 chars), "View in Slack →" permalink opening in new tab. Thread replies indented with ↩ prefix and muted background. Delay explanation shown at bottom if applicable.

### Refresh button

```javascript
document.getElementById('refresh-btn').addEventListener('click', () => {
  sendPrompt("Please refresh the Miro release dashboard by re-scraping #product_news and #product_review and rebuilding the artifact.");
});
```

---

## Step 6 — Update the Google Sheet

After rendering the HTML dashboard, update the persistent tracking spreadsheet.

**Spreadsheet ID:** `1HbvZWkDBr5DuIqhPzuTnMdP81Xa9huLldALBS9eq1-A`

Use the Google Drive tools to overwrite the sheet with fresh data on every refresh. The sheet has a single tab called **"Features"**. Clear all rows below the header on every run, then write the full current feature list from scratch. Do not append — always overwrite.

### Column structure

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
| I | Delay Note | Plain-text explanation of the delay, or blank |
| J | Last Updated | ISO date format: YYYY-MM-DD |
| K | Last Updated (Readable) | Human-readable, e.g. "Apr 23, 2026" |
| L | Slack Link | Permalink to most recent message for this feature |
| M | Refreshed At | Current date and time this row was written, ISO format |

### Formatting rules

- Row 1 (header): bold, light grey background (#f3f3f3), frozen
- Rows with Delay Status "Date slipped": light yellow (#fff8e1) on column H cell
- Rows with Delay Status "Milestone regressed": light red (#fce4ec) on column H cell
- Rows with Delay Status "No recent updates": light grey (#f5f5f5) on column H cell
- Column A (Feature Name): bold text
- Freeze top row

### After updating the sheet

Share the Google Sheet link alongside the dashboard summary:

> "Dashboard updated. [View the Google Sheet →](https://docs.google.com/spreadsheets/d/1HbvZWkDBr5DuIqhPzuTnMdP81Xa9huLldALBS9eq1-A/edit)"

---

## Ambiguity rules

- Ambiguous milestone → include with "⚠️ Ambiguous" note rather than guessing
- Same feature referenced under different names → include both rows with "Possible duplicate of: [name]" note
- No milestone info at all → include the feature with null milestone fields

---

## After rendering, tell the user

- How many features were found
- How many have delays flagged (and which types)
- How many are flagged as no recent updates
- The date range of messages scanned
