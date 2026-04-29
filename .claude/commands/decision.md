Capture a decision into the wiki. Prompts for structured context if not supplied inline, then files to wiki/decisions/, updates the index, and logs.

## Arguments
$ARGUMENTS

- Title string → use as the decision title, then prompt for context
- Full context pasted inline → extract and file without prompting
- `dry` → preview only, write nothing

---

## Steps

### 1. Load context
Read in parallel:
- `wiki/index.md` — check `## Decisions` for any existing related decisions (avoid duplicates; surface related ones)
- `wiki/surface.yaml` — to map initiative and person references

### 2. Extract or prompt for the four elements

If `$ARGUMENTS` contains enough context (multi-sentence, contains reasoning), extract directly.

If only a title is supplied, ask the four questions **one at a time**, waiting for each answer:

**Q1 — What was decided?**
"One clear sentence. What is the decision — not the background, just what was resolved."

**Q2 — Why?**
"What forced this decision? Name the specific constraints, trade-offs, or facts that made the alternatives worse."

**Q3 — Who decided (and who needs to know)?**
"Who owns this decision? Who signed off? Who downstream is affected and doesn't know yet?"

**Q4 — Implications?**
"What does this gate, unblock, or change? Any open questions that follow from this decision?"

After all four answers, also ask:
- "Suggest a slug (e.g. `feature-scope-reduction`) or accept auto-generated: `{YYYY-MM-DD}-{kebab-title}`"
- "Related wiki pages? (hit enter to skip)" — offer wikilink candidates based on mentions in the answers

### 3. Check for duplicates/related
Scan `wiki/decisions/` for any decision with similar title or overlapping scope. If found:
- Flag it: "Related decision exists: [[YYYY-MM-DD-slug]]. File separately or add to that page?"
- Wait for answer before proceeding.

### 4. Preview
Before writing, output:

```
DECISION PREVIEW

File: wiki/decisions/YYYY-MM-DD-{slug}.md

WHAT: {what}
WHY:  {why}
WHO:  {who}
IMPLICATIONS:
  - {implication 1}
  - {implication 2}
RELATED: {wikilinks}

INDEX ENTRY:
  - [[YYYY-MM-DD-{slug}]] — {one-line summary}

LOG ENTRY:
  ## [YYYY-MM-DD] decision | {title}
  {1-line summary}

File it? (Y / N / edit slug)
```

### 5. Write the decision page
```markdown
---
title: {title}
type: decision
domain: {your-domain|cross-cutting}
status: decided
decided_on: YYYY-MM-DD
decided_by: {name}
tags: [{relevant tags}]
last_updated: YYYY-MM-DD
---

# {title}

## What was decided
{clear statement — one paragraph max}

## Why
{constraints, trade-offs, context — bullet points if multiple reasons}

## Who
{name(s), role, and whether they own vs. approved vs. were consulted}

## Implications
{bullet list: what this gates, what it unblocks, what open questions follow, what people need to know}

## Related
{wikilinks — every initiative, person, or prior decision mentioned}
```

### 6. Update wiki/index.md
Add under `## Decisions` (most recent first):
```
- [[YYYY-MM-DD-{slug}]] — {one-line summary}. 
```
Bump `Last updated`.

### 7. Prepend to wiki/log.md
```
## [YYYY-MM-DD] decision | {title}
{1-line: what was decided + key implication + pages touched}
```

### 8. Output confirmation
```
Filed: wiki/decisions/YYYY-MM-DD-{slug}.md
Index and log updated.

{Any people who need to be told — flagged if WHO answer mentioned downstream recipients.}
```

---

## Rules
- One decision per file. If the content spans multiple decisions, file them separately and wikilink them.
- Never reopen a filed decision — create a new decision that supersedes it and link back.
- `dry` mode outputs the full preview, writes nothing.
- If the slug conflicts with an existing file, append `-2` and flag it.
- Domain: default to `cross-cutting` when uncertain. Ask if it matters.
