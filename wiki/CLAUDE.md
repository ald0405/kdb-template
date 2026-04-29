# Wiki maintenance conventions

The wiki is the compounding layer — LLM-maintained synthesis that connects metrics, initiatives, decisions, and competitor moves. Raw data stays in Looker, Amplitude, Notion, Jira. The wiki holds annotations, patterns, and the "why behind the what" that nothing else captures.

Read this file before any wiki-aware command.

## Read order
1. `wiki/index.md` — the catalogue. Always first.
2. `wiki/surface.yaml` — your initiatives, metrics, and source queries.
3. Drill into specific pages. Never bulk-load.

## When to create vs. update
- **Create** a new page when a new entity appears (new metric, new initiative, new competitor, new decision worth logging).
- **Update** an existing page when the underlying source changes — append a dated entry to its Context log, bump `last_updated` in frontmatter, refresh the summary only if the narrative actually shifted.
- Never overwrite prior context log entries. Append only.

## Page structure
Every wiki page has these blocks in order:

1. **YAML frontmatter** (Obsidian indexes this)
2. **Summary** — one paragraph: what this thing is, why it matters
3. **Substance** — metric definition / initiative phases / competitor profile / decision rationale
4. **Related** — wikilinks to other wiki pages
5. **Context log** — append-only dated entries

## Frontmatter schema
```yaml
---
title: Example Metric
type: metric              # metric | initiative | competitor | decision | brief | person
domain: cross-cutting     # adjust to your domain taxonomy
status: active            # active | declining | stable | decided | deferred | archived
source: looker-NNN        # looker-NNN | amplitude-XXX | notion | jira | competitor-capture
tags: [your-tags]
last_updated: YYYY-MM-DD
---
```

## Tag taxonomy (applied consistently)
- `#type/…` — metric, initiative, competitor, decision, brief, person
- `#domain/…` — set domains that match your product areas
- `#status/…` — active, declining, stable, decided, deferred, archived
- `#source/…` — looker, amplitude, notion, jira, competitor-capture

## Wikilinks
- Use `[[kebab-case]]`. Filename stem IS the wikilink target.
- Link aggressively — every mention of a tracked entity gets a wikilink.
- Obsidian resolves across folders by filename: `[[slug]]` finds `wiki/metrics/slug.md` if it exists. Avoid duplicate filenames across the vault.
- Rename with care. If you rename a file, grep for inbound wikilinks and update them.

## Index discipline
Every new page gets one line under its category in `wiki/index.md`:
```
- [[slug]] — one-line summary. Source: XXX.
```
Sort alphabetically within each category. Bump `Last updated` and `Total pages`.

## Log discipline
Every action (ingest, brief, decision, lint) prepends an entry to `wiki/log.md`:
```
## [YYYY-MM-DD] {ingest|brief|decision|lint|scaffold} | {title}
{1–2 lines: what happened, pages touched, key insight}
```
Most recent at the top.

## Notion boundary
- Notion owns living, human-edited documents (PRDs in progress, meeting notes, roadmaps). The wiki never copies them.
- The wiki owns synthesis: extracted decisions, rationale, cross-references, temporal patterns. Every derived page links back to the Notion source in frontmatter or summary.
- Ingest from Notion happens ONCE when a source is finalised, not on every query.

## Ingest rules
- **Weekly metrics** (Looker, Amplitude) — refreshed every Monday by `/wiki-brief`. Numbers are never cached. The wiki stores the annotation, not the number.
- **Notion pages** — ingested on-demand via `/wiki-ingest {url}` when a PRD is finalised, a decision lands, or a roadmap updates. Can also accept pasted notes/transcripts without a URL.
- **Jira epics** — ingested on epic closure. Extract outcomes; file to `wiki/initiatives/{epic-slug}.md`.
- **Competitor runs** — `/competitor-pricing-*` writes the raw report AND updates `competitors/{name}/{name}.md` pricing history + `wiki/log.md`.

## Linting (monthly, via `/wiki-lint`)
- Orphan pages (no inbound wikilinks)
- Stale `last_updated` (>60 days) on pages marked `status: active`
- Missing cross-references (page mentions an entity that has a wiki page but doesn't link to it)
- Contradictions between pages (same fact, different claims)

## Obsidian
The repo root opens as an Obsidian vault. `.obsidian/` is gitignored. Graph view should show clusters by domain; if it doesn't, tags or wikilinks are missing.
