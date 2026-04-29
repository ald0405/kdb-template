Ingest a Notion page (or pasted content) into the wiki. Creates or updates the right wiki page, updates any people profiles mentioned, and appends to the log. Always shows a preview before writing files.

## Arguments
$ARGUMENTS

- A Notion URL → fetch via MCP then ingest
- No argument → treat the message content as pasted notes/transcript
- `dry` → preview only, write nothing
- `force` → skip preview confirmation and write immediately

---

## Steps

### 1. Load wiki context
Read in parallel:
- `wiki/CLAUDE.md` — conventions and page structure
- `wiki/index.md` — catalogue of existing pages (check for existing pages before creating new ones)
- `wiki/surface.yaml` — initiatives and people (to map mentions)
- `kdb.config.md` — people slugs, Jira project, Notion URLs

### 2. Fetch or accept content
- If `$ARGUMENTS` is a Notion URL: fetch with `mcp__notion__notion-fetch`
- If `$ARGUMENTS` is empty or contains only flags: use the pasted content from the message
- If fetching fails, say so — never invent content

### 3. Classify the content
Determine the primary type. One type per ingest:

| Content type | Wiki target | Frontmatter type |
|---|---|---|
| Meeting notes / standup summary | `wiki/initiatives/{initiative}.md` context entry + people profiles | `ingest` in log |
| Notion PRD / initiative doc | `wiki/initiatives/{slug}.md` (create or update) | `initiative` |
| Reference / strategy doc | `wiki/reference/{slug}.md` (create) | `reference` |
| Decision note | `wiki/decisions/YYYY-MM-DD-{slug}.md` (create) | `decision` |
| Competitor page | `competitors/{slug}/{slug}.md` (update) | `competitor` |

If the content spans multiple types (e.g. a meeting that also contains a decision), handle each separately.

### 4. Extract structured content
Pull out:

**Always:**
- Title / subject
- Date (explicit or inferred)
- Key decisions made (what + why + who)
- Action items (owner + due date)
- People mentioned (match to slugs in `kdb.config.md`)
- Initiative references (match to slugs in `wiki/surface.yaml`)

**For meetings specifically:**
- Attendees
- Blockers or risks raised
- Signals about stakeholder positions / next steps

**For PRDs/initiative docs:**
- Phase + status
- Goals / success metrics
- Open questions or blockers
- Last-edited date (from Notion metadata if available)

### 5. Determine create vs update
Check `wiki/index.md`:
- If a page for this entity already exists → **update**: append a dated entry to its Context log, bump `last_updated`, refresh summary ONLY if the narrative shifted
- If no page exists → **create**: full page using the structure in `wiki/CLAUDE.md`

For meetings: there is usually no standalone wiki page — instead append context log entries on the relevant initiative pages and update people profiles.

### 6. Build the preview
Before writing anything, output:

```
INGEST PREVIEW — {title}
Type: {classified type}
Date: {date}

WIKI PAGES TO TOUCH
  {create|update} wiki/{path} — {one-line reason}
  {create|update} wiki/{path} — {one-line reason}

PEOPLE PROFILES TO UPDATE
  knowledge/people/{slug}.md — {signal in one line}
  knowledge/people/{slug}.md — {signal in one line}

DECISIONS TO FILE (if any)
  wiki/decisions/YYYY-MM-DD-{slug}.md — "{decision title}"

ACTION ITEMS EXTRACTED (if any)
  - {owner}: {action} by {date}

LOG ENTRY
  ## [YYYY-MM-DD] ingest | {title}
  {1–2 line summary of what happened, pages touched, key insight}

Write? (Y / N / edit)
```

If `force` flag is set, skip this step and write immediately.

### 7. Write files (on confirmation or `force`)

**Wiki page — create:**
Use frontmatter schema from `wiki/CLAUDE.md`. Full structure: frontmatter + Summary + Substance + Related + Context log.

**Wiki page — update:**
Append to the Context log section only. Format:
```
- [YYYY-MM-DD] {title}. {2–3 sentences: key decisions, signals, phase changes, blockers}. Source: {notion-url or "pasted notes"}.
```
Bump `last_updated` in frontmatter. Refresh summary paragraph only if the core narrative has shifted.

**People profiles — update:**
Append a dated entry to the `## Recent signals` section of `knowledge/people/{slug}.md`:
```
- **{Meeting name} ({date}):** {2–3 sentences: what they said/did, what it signals about their position/priorities, what the PM should know}
```
Never overwrite prior entries. Bump `last_updated`.

**Decision page — create (if extracted):**
Use the standard decision structure:
```markdown
---
title: {decision title}
type: decision
domain: {your-domain}
status: decided
decided_on: YYYY-MM-DD
decided_by: {name(s)}
tags: [{tags}]
last_updated: YYYY-MM-DD
---

# {title}

## What was decided
{clear statement}

## Why
{constraints, reasoning, context}

## Who
{name(s) + role context}

## Implications
{bullet list of downstream effects, dependencies, open questions}

## Related
{wikilinks}
```

### 8. Update wiki/index.md
- New pages: add one line under the correct category, alphabetically sorted
- Bump `Last updated` and `Total pages`

### 9. Prepend to wiki/log.md
```
## [YYYY-MM-DD] ingest | {title}
{1–2 lines: what happened, pages touched, key insight}
```

### 10. Output the summary
Short. What was ingested, what was created/updated, any action items or decisions to act on.

```
Ingested: {title}

FILED
  {list of files written}

ACTION ITEMS
  {list, or "none extracted"}

DECISIONS
  {filed slug, or "none — offer to file: {title}?"}
```

---

## Rules
- Never invent content. If the source is ambiguous, flag it explicitly.
- Never overwrite prior Context log entries. Append only.
- Meetings do not get their own wiki page — they touch initiative pages and people profiles.
- If the Notion fetch returns an image-heavy page with no extractable text, say so and ask the PM to paste the relevant content.
- `dry` mode generates the full preview but writes nothing and does not prompt for Y/N.
