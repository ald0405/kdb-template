Combined weekly routine: stakeholder scan → wiki brief → unified debrief. Run once a week to keep people profiles and metrics in sync.

## Arguments
$ARGUMENTS

- Week string (e.g. `W15`, `2026-W15`) → target that week for the brief.
- `preview` → dry run on the wiki brief; print brief without writing files (stakeholder profiles still update).

---

## Phase 1 — Load shared context

Read all of these before starting:
- `kdb.config.md` — extract `peers`, `leadership`, `monitored_channels_for_people`, `monitored_notion_pages_for_people`
- `wiki/CLAUDE.md` — wiki conventions
- `wiki/surface.yaml` — your initiatives, metrics, source queries
- `wiki/index.md` — catalogue of existing pages
- Most recent file under `wiki/briefs/` (highest ISO week) — used for WoW comparisons

---

## Phase 2 — Stakeholder scan

### 2a. Pull Slack activity (last 7 days)

For each person in `peers` + `leadership` where `slack_id` is set (not `YOUR_SLACK_ID`):
- Search each `monitored_channels_for_people` channel for messages from that person
- Capture: topics raised, questions asked, concerns flagged, decisions pushed on

If Slack MCP not connected: skip, note "Slack not connected — Notion-only scan".

### 2b. Pull Notion activity

For each URL in `monitored_notion_pages_for_people` that is not `YOUR_NOTION_PAGE_URL`:
- Fetch page + comments
- Filter for activity attributed to people in config
- Note: what they commented on, questioned, or changed

### 2c. Extract patterns per person

For each person with activity:
- **Recurring topics** — subjects that keep coming up
- **Question patterns** — what they probe on, what gaps they flag
- **Decision signals** — what factors seem to drive their responses
- **Tone** — data-driven, narrative, blunt, etc.

### 2d. Update knowledge/people/ files

For each person:
- Read `knowledge/people/{slug}.md`
- Overwrite "Recent signals" with 1–2 bullets from last 7 days
- Append new question patterns to "Questions they typically ask" (no duplicates)
- Update "What they care about" only if a genuinely new durable theme emerged
- Update `Last updated` line with today's date and source channels/pages

If no file exists yet: create it using the structure from existing stubs.

---

## Phase 3 — Wiki brief

### 3a. Determine target week

Default: prior completed ISO week. Override with week string from `$ARGUMENTS`.
Brief filename: `wiki/briefs/YYYY-Wxx.md`.

### 3b. Pull current metrics

For each metric in `business_health` + each active metric in `initiatives[*].metrics` (respect `phase_active_from`):
- Query via Looker toolbox (Looker metrics) or Amplitude MCP (Amplitude metrics)
- Capture value for the prior full week
- Note: if a metric isn't yet measurable, record `not yet measurable — {initiative} in {phase}` — do not query

If a dashboard query fails: record `FAILED` — never invent numbers.

### 3c. Diff vs last week

Extract last week's values from the previous brief. Compute WoW delta + % change. Flag:
- Any metric with >5% WoW change
- Any metric on a 3+ week trend in the same direction (read earlier briefs if needed)

### 3d. Annotate

For each flagged movement:
- Check `wiki/metrics/{slug}.md` Context log — if cause known, reference it; if not, mark "unexplained"
- Check `wiki/initiatives/*.md` for phase changes or status updates since last brief

**Cross-reference with stakeholder signals** (Phase 2 output):
- If a stakeholder raised a question about a metric that moved, note it under that metric
- If a stakeholder commented on an initiative page, pull that signal into initiative progress

### 3e. File the brief

If `preview` in `$ARGUMENTS`: print and stop — do not write.

Otherwise write `wiki/briefs/YYYY-Wxx.md`:

```markdown
---
title: Weekly Brief — YYYY-Wxx
type: brief
domain: cross-cutting
status: filed
tags: [weekly-brief]
last_updated: YYYY-MM-DD
week_start: YYYY-MM-DD
week_end: YYYY-MM-DD
---

# Weekly Brief — YYYY-Wxx

## Headline
{One sentence. The most important thing this week.}

## Business health
| Metric | This week | Last week | WoW | Trend | Notes |
|---|---|---|---|---|---|
| [[your-metric]] | … | … | … | … | … |

## Initiative progress
### [[your-initiative]] — {phase}
- {Status, blocker, decision}
- Stakeholder signals: {any relevant person activity from Phase 2}

## Stakeholder signals
{2–4 bullets. One per person where something notable emerged. Only include signals that are new or directionally significant.}

## Flags for the PM
- {Decisions needed, conversations to have, follow-ups}

## Sources pulled
- {Dashboard name} ({source}) — {date}
- Slack channels: {list} — {date range}
- Notion pages: {list} — {date}
```

### 3f. Update metric pages

Append to `wiki/metrics/{slug}.md` Context log:
```
- [YYYY-MM-DD] {value} ({WoW delta}). {1-line annotation}. See [[YYYY-Wxx]].
```

Create page if missing (use structure from `wiki/CLAUDE.md`). Add to `wiki/index.md` under `## Metrics`.

### 3g. Update wiki/index.md

Add brief under `## Weekly Briefs`:
```
- [[YYYY-Wxx]] — {headline}
```
Add new metric pages under `## Metrics`. Bump `Last updated` and `Total pages`.

### 3h. Prepend to wiki/log.md

```
## [YYYY-MM-DD] pulse | Weekly Pulse YYYY-Wxx
{Headline + notable metric movement + key stakeholder signal. Pages touched: {list}.}
```

---

## Phase 4 — Combined debrief output

```
*WEEKLY PULSE — YYYY-Wxx*

*HEADLINE*
{One sentence. The most important thing across metrics + people.}

*WHAT MOVED*
{2–4 bullets. Metric, direction, magnitude, cause if known.}

*INITIATIVES*
{One line per active initiative. Phase + on-track / blocked.}

*PEOPLE*
{2–4 bullets. One per person with a notable signal. Skip anyone with nothing new.}

*FLAGS*
{What needs the PM this week. Be direct. Cross-reference metrics and people where relevant.}

*FILES UPDATED*
knowledge/people/{list of updated profiles}
wiki/briefs/YYYY-Wxx.md
wiki/metrics/{list}
wiki/index.md, wiki/log.md
```

Keep under 35 lines. No padding.

---

## Rules

- Read `kdb.config.md` first — always.
- Never cache metric values. Always query live.
- Never post to Slack without a separate explicit instruction.
- If a brief contradicts a prior decision in `wiki/decisions/`, surface it in Flags.
- If a stakeholder signal contradicts the current initiative direction, surface it in Flags.
- Preserve prior Context log entries on metric pages. Append only.
- Draft before writing to Notion or Jira — not triggered by this routine.
