Scan recent Slack and Notion activity for key people and update their stakeholder intelligence files.

## What this does

Reads monitored channels and Notion pages, filters by the people defined in config, extracts patterns, and updates per-person files in `knowledge/people/`. This is how the knowledge base stays current — run it weekly or after any significant leadership interaction.

## Steps

### Step 1 — Read config

Read `kdb.config.md`. Extract:
- `peers` list (name, slack_id, role, group)
- `leadership` list (name, slack_id, role, group)
- `monitored_channels_for_people` (list of Slack channels)
- `monitored_notion_pages_for_people` (list of Notion page URLs)

If any slack_ids are still set to `YOUR_SLACK_ID`, note those people as Slack-untrackable and skip Slack for them (still scan Notion).

### Step 2 — Pull Slack activity (last 7 days)

For each person where slack_id is set:
- Search each monitored channel for messages from that person in the last 7 days
- Capture: what topics they raised, questions they asked, concerns they flagged, decisions they pushed on

If Slack MCP is not connected: skip this step, note "Slack not connected — Notion-only scan" in the output.

### Step 3 — Pull Notion activity

For each URL in `monitored_notion_pages_for_people`:
- Fetch the page and its comments
- Filter comments and recent edits attributed to people in your config list
- Note: what did they comment on, what did they question, what changes did they make

If Notion page URLs are still set to `YOUR_NOTION_PAGE_URL`: skip and note.

### Step 4 — Extract patterns per person

For each person with activity found:

Identify:
- **Recurring topics**: what subjects keep coming up in their messages?
- **Question patterns**: what do they probe on? What gaps do they flag?
- **Decision signals**: what factors seem to drive how they respond?
- **Tone and framing**: how do they communicate — data-driven, narrative, blunt?

### Step 5 — Update knowledge files

For each person, read their current file at `knowledge/people/{slug}.md`:

- **Overwrite** the "Recent signals" section with 1-2 bullet points from the last 7 days
- **Append** any new question patterns to "Questions they typically ask" that are not already captured
- **Update** "What they care about" if a genuinely new theme emerged (don't over-update — themes should be durable, not reactive)
- **Bump the frontmatter `last_updated` field** to today's date (YYYY-MM-DD format, inside the `---` YAML block — not just the inline header line)
- **Add `[[wikilinks]]`** to any tracked person mentioned by name in new signal entries. Use `[[slug|display-name]]` format. Slug mapping is in `knowledge/people/CLAUDE.md`.

If no file exists for someone yet: create it using the standard template (copy structure from existing stubs). New files must include:
- Full YAML frontmatter (`title`, `type: person`, `group`, `role`, `tags`, `last_updated`)
- Wikilinks on first mention of any tracked person

### Step 6 — Update wiki log

Append an entry to `wiki/log.md` (prepend — most recent at top):
```
## [YYYY-MM-DD] stakeholder-scan | Weekly signal refresh
Scanned: {list of people updated}. Key signals: {1-2 notable patterns worth wiki-level attention}. Skipped: {anyone with missing IDs}.
```

### Step 7 — Report

Output a brief summary:
- Who was scanned and from which sources
- What changed in each person's profile (new patterns, updated signals)
- Anyone skipped (missing Slack ID, Notion URLs not set)
- Any notable new patterns worth flagging (e.g. leadership asking about a topic that wasn't on your radar)

---

## File locations

- Config: `kdb.config.md`
- People profiles: `knowledge/people/{slug}.md`

## Slug mapping

Update this table to match your team as configured in `kdb.config.md`:

| Name | Role | Slug |
|------|------|------|
| LEADER_NAME | Leadership Role | leader-slug |
| TEAM_MEMBER | Tech Lead | tech-lead-slug |
| TEAM_MEMBER | Designer | designer-slug |

<!-- Keep this table in sync with kdb.config.md People section. -->
