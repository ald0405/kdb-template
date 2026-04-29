Monthly wiki health check. Finds orphan pages, stale active pages, missing wikilinks, and contradictions. Outputs a prioritised punch list and offers to fix each category.

## Arguments
$ARGUMENTS

- No argument → full lint across all four checks
- `orphans` → orphan check only
- `stale` → stale pages only
- `links` → missing wikilinks only
- `fix` → auto-fix safe issues (missing wikilinks, index gaps) after showing what will change
- `dry` → run all checks, output results, make no changes

---

## Steps

### 1. Load wiki catalogue
Read:
- `wiki/index.md` — complete list of tracked pages and their paths
- `wiki/CLAUDE.md` — conventions (tag taxonomy, frontmatter schema, link format)

### 2. Collect all wiki files
Glob all `.md` files under `wiki/` (excluding `CLAUDE.md`, `log.md`, `index.md`, `surface.yaml`).
Also glob `knowledge/people/*.md` and `competitors/**/*.md` — these are indexed and can be orphaned too.

Build a registry: `{ slug → file_path, frontmatter, body_text }` for each file found.

### 3. Run the four checks

#### Check A — Orphan pages
An orphan is a wiki page with **no inbound wikilinks** from any other wiki page, initiative page, people profile, or `wiki/index.md`.

For each page in the registry:
- Search all other wiki files for `[[{slug}]]` or `[[{slug}|`
- If zero matches found: flag as orphan

Orphans are not always wrong — new pages take time to accumulate links. Flag, don't delete.

#### Check B — Stale active pages
A page is stale when:
- `status: active` (or `in-progress`) in frontmatter AND
- `last_updated` is more than 60 days before today

Read `last_updated` from each page's frontmatter. Flag any that qualify.

Also flag: pages with `status: active` but no `last_updated` field at all (missing metadata).

#### Check C — Missing wikilinks
For each page, scan the body text for mentions of entity names that have a corresponding wiki page (by title or slug) but are written as plain text rather than `[[wikilink]]`.

To find candidates:
- For each known slug in the registry, check if the slug (or its title equivalent) appears in body text without `[[` surrounding it
- Limit to high-signal mentions: initiative names, person names, metric names

Flag: `{source_file}` mentions `{entity}` as plain text — should be `[[{slug}]]`.

This check is fuzzy — only flag high-confidence matches (exact slug or title match). Do not flag partial name matches.

#### Check D — Contradictions
Look for the same fact asserted differently in two pages. Focus on high-risk fields:

- **Dates:** does the `decided_on` date in a decision file match how it's cited in initiative/metric context logs?
- **Status:** does an initiative page say `status: in-progress` while the index says something different?
- **People/roles:** does a person's role in their profile match how they're described in initiative pages?
- **Metric values:** do context log entries on metric pages contradict values cited in brief pages for the same week?

Read relevant page pairs. Flag specific contradictions with both sources cited.

### 4. Build the punch list

Output in this format:

```
WIKI LINT — YYYY-MM-DD
Total pages checked: {N}

━━━ A. ORPHAN PAGES ({count}) ━━━
Pages with no inbound wikilinks. May be newly created or genuinely isolated.

  HIGH — [[{slug}]] ({path}) — type: {type}, created: {last_updated}
  LOW  — [[{slug}]] ({path}) — type: {type}, created: {last_updated}

Suggested fix: add to relevant initiative page, decision, or person profile. Or archive if superseded.

━━━ B. STALE ACTIVE PAGES ({count}) ━━━
status: active but last_updated > 60 days ago.

  [[{slug}]] — last updated {date} ({N} days ago). Status: {status}.
  [[{slug}]] — last updated {date} ({N} days ago). No last_updated field.

Suggested fix: review each. Either update the context log with current state, or change status to archived/deferred.

━━━ C. MISSING WIKILINKS ({count}) ━━━
Entity mentioned as plain text where a wiki page exists.

  {source_file}:L{line} — "{plain text}" should be [[{slug}]]
  {source_file}:L{line} — "{plain text}" should be [[{slug}]]

Suggested fix: replace with wikilinks. Safe to auto-fix (run `/wiki-lint fix` or approve below).

━━━ D. CONTRADICTIONS ({count}) ━━━
Same fact stated differently in two places.

  {file_a} says: "{claim}"
  {file_b} says: "{claim}"
  → Resolve: which is authoritative?

━━━ SUMMARY ━━━
{N} orphans | {N} stale | {N} missing links | {N} contradictions
Total issues: {N}

Run `/wiki-lint fix` to auto-fix missing wikilinks (C). Other issues require manual review.
```

If no issues found in a category, output: `✓ {Category name} — clean.`

### 5. Offer fixes
After the punch list, ask:

**For missing wikilinks (Check C):** "Auto-fix {N} missing wikilinks? I'll replace plain-text mentions with `[[slug]]` in place. (Y / N / show me each)"

**For stale pages (Check B):** List each stale page and ask per-page: "[[{slug}]] — last touched {date}. Update it now, change status to `archived`, or skip?"

**For orphans (Check A):** Do not auto-fix. List and ask: "Which orphans should I link from existing pages?"

**For contradictions (Check D):** Present each pair and ask: "Which source is authoritative — {file_a} or {file_b}?"

### 6. Apply approved fixes
For each approved fix:
- Edit the file in place
- Collect all changed files

### 7. Log the lint run
Prepend to `wiki/log.md`:
```
## [YYYY-MM-DD] lint | Monthly wiki lint
{N} issues found: {N} orphans, {N} stale, {N} missing links, {N} contradictions. Fixed: {N} (missing links auto-fixed). Manual review needed: {N}.
```

Update `wiki/index.md` → bump `Last updated`.

### 8. Output final summary
```
LINT COMPLETE — YYYY-MM-DD

Issues found: {N}
Auto-fixed:   {N} (missing wikilinks)
Pending:      {N} (orphans + stale + contradictions — manual review)

wiki/log.md updated.
```

---

## Rules
- Never delete wiki pages — flag as orphan and let the PM decide.
- Never change frontmatter `status` without explicit confirmation per page.
- Auto-fix is limited to safe structural changes: adding `[[wikilinks]]` where plain text exists.
- `dry` runs all checks and shows the full punch list but writes nothing — not even the log entry.
- If the wiki is clean (zero issues), say so: one line. No padding.
