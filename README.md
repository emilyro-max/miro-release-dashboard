# miro-release-dashboard
Here's the complete `README.md` — ready to paste straight into your repo:

---

```markdown
# Miro Release Dashboard

An internal tool for the Customer Education team that automatically scrapes
#product_news and #product_review, parses feature milestones, flags delays,
and generates an interactive HTML dashboard. Hosted on VibeLab.

**Live dashboard:** https://miro-release-dashboard.vibelab.miro.tools
**Tracking spreadsheet:** https://docs.google.com/spreadsheets/d/1HbvZWkDBr5DuIqhPzuTnMdP81Xa9huLldALBS9eq1-A/edit
**Full spec:** https://docs.google.com/document/d/1IcKLhiEEw638Js-0tatz8P5vi3LLopeLZ0hUo-Ck-Gk/edit

---

## How it works

1. Claude scrapes #product_news and #product_review via Slack search
2. Threads are fetched for any message with replies
3. Features are parsed, milestones extracted, and delays detected
4. An interactive HTML dashboard is generated and saved to `index.html`
5. The Google Sheet is updated with the same data in tabular form
6. VibeLab serves `index.html` automatically from this repo

The full parsing rules, design spec, colour tokens, and delay detection
logic all live in [SPEC.md](./SPEC.md).

---

## Triggering a refresh

Open the [Claude Project](https://claude.ai) and say:

> Refresh the Miro release dashboard

Claude will scrape both channels, rebuild the dashboard, update the Google
Sheet, and output the new `index.html`. Copy the generated HTML and commit
it to this repo — VibeLab will pick it up automatically.

Typical refresh time: ~3 minutes.

**Recommended cadence:** Weekly, every Monday morning. Set a Slack reminder:
```
/remind me every Monday at 9am to refresh the Miro release dashboard
```

---

## Repo structure

```
miro-release-dashboard/
├── index.html                  ← Live dashboard (auto-generated, do not edit manually)
├── SPEC.md                     ← Full parsing, delay detection, and rendering spec
├── project-instructions.md     ← Text to paste into the Claude Project Instructions
└── README.md                   ← This file
```

---

## Editing the spec

All parsing logic and dashboard design rules live in `SPEC.md`. To change
how features are parsed, how delays are detected, which priority products
are tagged, or how the dashboard looks — edit `SPEC.md` and the next
refresh will pick up the changes automatically.

The spec is also mirrored in this Google Doc for easy commenting:
https://docs.google.com/document/d/1IcKLhiEEw638Js-0tatz8P5vi3LLopeLZ0hUo-Ck-Gk/edit

If you edit the Google Doc, keep SPEC.md in sync.

---

## Claude Project setup

The Claude Project that powers this dashboard is configured with a short
instruction that points to this repo and the spec. The full text is in
[project-instructions.md](./project-instructions.md).

To set up the project from scratch:
1. Go to claude.ai → Projects → Create project
2. Name it `Miro Release Dashboard`
3. Paste the contents of `project-instructions.md` into the project instructions
4. Make sure the Slack and Google Drive connectors are enabled

---

## VibeLab deployment

This dashboard runs as a static site on VibeLab — no build step required.
VibeLab serves `index.html` directly from the `main` branch of this repo.

**To deploy an update:**
1. Get the refreshed `index.html` from Claude
2. Replace the existing `index.html` in this repo
3. Commit and push to `main`
4. VibeLab picks it up automatically

**Access:** Miro VPN required (Cloudflare WARP). All Mironeers with GitHub
access can view the dashboard.

**VPN setup:** Miro Self-Serve → Cloudflare WARP → Auth GitHub → Toggle on

---

## Data sources

| Source | What it provides |
|---|---|
| #product_news (C8BJSFHAT) | GA, Public Beta, Mironeer Release announcements |
| #product_review (CGNJHCD97) | Kick-off, Solution Review, Pre-release Review milestones |

Scrape window: last 90 days. Thread replies are fetched for all messages
with reply_count > 0, as they frequently contain the most current milestone
dates and delay acknowledgements.

---

## Tracked fields per feature

| Field | Notes |
|---|---|
| Feature Name | |
| Channel | #product_news or #product_review |
| Priority Product | Engage / AI Workflows / Roadmaps / Prototypes |
| Current Milestone | Kick-off → Solution Review → Pre-release → Mironeer → Private Beta → Public Beta → GA |
| Next Milestone | |
| Delay Status | No delay / Date slipped / Milestone regressed / No recent updates |
| Last Updated | Date of most recent Slack message |
| Message History | All Slack messages referencing the feature, with permalinks |

---

## Delay detection

| Flag | Meaning |
|---|---|
| ⏰ Date slipped | A previously announced date moved later |
| 🔴 Milestone regressed | Feature moved backward in milestone sequence |
| 💤 No recent updates | No Slack message in the last 30 days |

---

## Questions or changes?

Reach out in Slack or open a PR against `SPEC.md` with your proposed change.
```

---
