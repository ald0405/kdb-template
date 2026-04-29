Run the weekly wiki brief loop: pull metrics, diff vs last week, annotate, file to wiki, update index + log, output a short debrief.

## Steps

### 1. Load wiki context
- Read `wiki/CLAUDE.md` — conventions
- Read `wiki/surface.yaml` — your initiatives, metrics, source queries
- Read `wiki/index.md` — catalogue of existing pages
- Read the most recent file under `wiki/briefs/` (highest ISO week number) — this is the "previous brief" used for WoW comparisons. If none exists, this is Week 1 — establish baselines.

### 2. Determine the target week
- Default: the prior completed ISO week. If today is Mon 2026-04-20, target = W16 (2026-04-07 → 2026-04-13).
- If `$ARGUMENTS` includes a week string like `W15` or `2026-W15`, use that.
- Brief filename: `wiki/briefs/YYYY-Wxx.md`.

### 3. Pull current values
For each metric in `business_health`, plus each active metric in `initiatives[*].metrics` (respect `phase_active_from` — skip leading metrics for initiatives not yet in the activating phase):

- Query the named dashboard via the appropriate MCP (Looker toolbox for Looker; Amplitude MCP for Amplitude)
- Capture the current value for the prior full week
- Cite the dashboard URL in the brief's `## Sources pulled` section

If a metric is not yet measurable (initiative hasn't reached `phase_active_from`), note `not yet measurable — {initiative} in {phase}` rather than querying.

If a dashboard query fails, record `FAILED` against that source — never invent numbers.

### 4. Diff vs last week
From the previous brief, extract last week's value for each metric. Compute WoW delta and % change. Flag:
- Any metric with >5% WoW change
- Any metric continuing a 3+ week trend in the same direction (read earlier briefs if needed)

### 5. Annotate
For each flagged movement, check the corresponding `wiki/metrics/{slug}.md` Context log:
- If a known cause is already logged, reference it in the brief
- If not, flag as "unexplained" — an open question for the PM

Also check `wiki/initiatives/*.md` for any phase change or status update since the last brief.

### 6. File the brief
Write `wiki/briefs/YYYY-Wxx.md` using this structure:

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
| [[your-metric]] | value | value | delta | trend | notes |

## Initiative progress
### [[your-initiative]] — {phase}
- {Status, blocker, decision}
- Metrics in scope: {list; note if not yet measurable}

## Flags for the PM
- {Anything needing a decision, conversation, or follow-up}

## Sources pulled
- {Dashboard name} ({source}) — {date}
```

### 7. Update metric pages
For each tracked metric, append one dated entry to the Context log section of `wiki/metrics/{slug}.md`:

```
- [YYYY-MM-DD] {value} ({WoW delta}). {1-line annotation}. See [[YYYY-Wxx]].
```

If the metric page does not exist yet, create it using the structure defined in `wiki/CLAUDE.md` (frontmatter + summary + definition + Related + Context log). Add it to `wiki/index.md` under `## Metrics`.

### 8. Update wiki/index.md
Add the new brief under `## Weekly Briefs`:
```
- [[YYYY-Wxx]] — {headline from the brief}
```
Add any newly created metric pages under `## Metrics`. Bump `Last updated` and `Total pages`.

### 9. Prepend to wiki/log.md
At the top, right under the header block:

```
## [YYYY-MM-DD] brief | Weekly Brief YYYY-Wxx
{Headline + any notable trend/anomaly. Pages touched: {list}.}
```

### 10. Output the debrief
Short, direct. Do not post to Slack — that's a separate step.

```
*WEEKLY BRIEF — YYYY-Wxx*

*HEADLINE*
{One sentence.}

*WHAT MOVED*
{2–4 bullets. Metric, direction, magnitude, likely cause if known.}

*INITIATIVES*
{One line per active initiative. Phase + on-track / blocked.}

*FLAGS*
{What needs the PM this week. Be direct.}

*FILES UPDATED*
wiki/briefs/YYYY-Wxx.md
wiki/metrics/{list}
wiki/index.md, wiki/log.md
```

Keep the debrief under 30 lines. No padding.

## Rules
- Never cache metric values. Always query live.
- If a dashboard query fails, flag it in `## Sources pulled` — never invent numbers.
- Preserve prior Context log entries on metric pages. Append only.
- If the brief contradicts a prior decision logged in `wiki/decisions/`, surface it explicitly in `## Flags for the PM`.
- Preview-only mode: if `$ARGUMENTS` contains `preview`, generate the brief but do not write files — output it for review first.

## Arguments
$ARGUMENTS

- Week string (e.g. `W15`, `2026-W15`) → target that week instead of the prior completed week.
- `preview` → dry run; print the brief, write nothing.
