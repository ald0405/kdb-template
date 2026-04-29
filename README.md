# KDB — the AI assist

> Named after Kevin De Bruyne, the assist king. Sets up your PMs to score.

A config-driven AI assistant for product managers, built on [Claude Code](https://docs.claude.com/en/docs/claude-code) and the [Model Context Protocol](https://modelcontextprotocol.io). KDB drafts comms, queries Looker and Amplitude, writes PRDs, runs competitor scans, and maintains a synthesis wiki — all from one Markdown config file. Fork it, point it at your stack, and use it as a daily PM copilot.

---

## What this is

KDB is a template repository, not a product. It's a worked example of how to wire a general-purpose coding agent into a real product manager's workflow: every URL, channel, dashboard, person, and slash command lives in plain Markdown, so a non-technical PM can change behaviour by editing one file. The agent reads the config, loads only the knowledge it needs, calls the right MCP server, drafts an output, and waits for explicit approval before writing anywhere.

---

## What this repo demonstrates

A focused showcase of applied AI engineering for non-engineers:

- **Multi-MCP orchestration** across 7 tools — Notion, Figma, Slack, Amplitude, Atlassian/Jira, Looker (via local toolbox), and LangChain docs.
- **Custom slash commands as composable agent workflows** — 14 named workflows in `.claude/commands/`, each loading its own context bundle.
- **Config-as-knowledge pattern** — `kdb.config.md` + `knowledge/` + `wiki/surface.yaml` make agent behaviour data-driven, not code-driven.
- **LLM-maintained knowledge graph in Markdown** — Obsidian-compatible wiki with frontmatter schema, tag taxonomy, append-only context logs, and a monthly lint command.
- **Approval-gated write actions** — drafts plus explicit yes before any post to Slack, Jira, or Notion.
- **Browser automation pipeline** — Playwright competitor pricing scrapers triggered directly by agent slash commands.
- **Permissioned tool access** — explicit MCP allow-list in `.claude/settings.json` so nothing fires that wasn't pre-approved.
- **Live docs over training knowledge** — LangChain MCP wired in so AI/RAG questions never go stale.

---

## Architecture at a glance

```mermaid
flowchart LR
    User([PM])
    Persona[CLAUDE.md<br/>persona + rules]
    Config[kdb.config.md<br/>URLs · channels · dashboards · people]
    Knowledge[knowledge/<br/>PRDs · glossary · OKRs · team]
    Commands[.claude/commands/<br/>slash workflows]
    Wiki[wiki/<br/>LLM-maintained synthesis]
    MCP{{MCP servers}}

    User --> Persona
    Persona --> Config
    Persona --> Knowledge
    Persona --> Commands
    Commands --> MCP
    MCP --> Notion & Slack & Looker & Amplitude & Jira & Figma & LangChainDocs[LangChain docs]
    Commands --> Wiki
    Wiki --> Persona
```

`CLAUDE.md` always reads `kdb.config.md` first. Slash commands compose context from `knowledge/`, call MCP tools, and write synthesis back into `wiki/`.

---

## Slash command catalogue

| Command | What it does |
|---------|--------------|
| `/data` | Pull metrics, query Looker or Amplitude, write data-driven briefs |
| `/comms` | Draft Slack messages, product updates, launch comms |
| `/product` | PRD work, Jira tickets, roadmap decisions, discovery |
| `/morning` | Daily pulse check at the start of the day |
| `/weekly-pulse` | Combined weekly routine — stakeholder scan + wiki brief in one pass |
| `/wiki-brief` | Weekly wiki loop — pull metrics, annotate, file brief, update index + log |
| `/stakeholder-scan` | Pull latest Slack + Notion activity, update stakeholder profiles |
| `/prep` | Pre-meeting prep brief for a specific person |
| `/reflect` | Weekly reflection — structured questions, files to `wiki/reflections/` |
| `/wiki-ingest {url}` | Ingest a Notion page or pasted notes into the wiki |
| `/decision {title}` | Capture a decision into `wiki/decisions/` |
| `/wiki-lint` | Monthly wiki health check — orphans, stale pages, missing wikilinks |
| `/competitor-pricing` | Full multi-category competitor pricing audit (Playwright) |
| `/competitor-pricing-weight-loss` | Category-specific competitor deep-dive |

---

## Tech & integrations

Claude Code · Model Context Protocol (MCP) · Playwright · Obsidian · Looker SDK toolbox · GitHub · Markdown-as-state.

MCP servers wired in:

- **Notion** — read/write PRDs, meeting notes, roadmaps, templates
- **Figma** — read design files and extract specs (read-only)
- **Slack** — draft and post channel updates
- **Amplitude** — query dashboards, funnels, experiments
- **Atlassian/Jira** — create, update, search issues
- **Looker** — query dashboards via the local toolbox MCP
- **LangChain docs** — live documentation for AI feature questions

---

## Quickstart

**Prerequisites:** Node.js 18+, Claude Code CLI, and a Claude Pro or Team subscription.

1. **Clone** this repo and drop your local Looker toolbox binary into `looker-toolbox/` (the directory's contents are gitignored — only the placeholder is tracked). Skip if you don't use Looker.
2. **Fill in `.mcp.json`** with your Looker URL, client ID, and client secret. This file is gitignored so credentials never leave your machine.
3. **Authenticate remote MCPs** via `claude mcp add --transport http <name> <url>` for Notion, Figma, Slack, Amplitude, Atlassian, and the LangChain docs. Wire up only the tools you actually use.
4. **Edit [`kdb.config.md`](./kdb.config.md)** — replace every `YOUR_…` placeholder with your real URLs, channel names, and Jira project key.
5. **Fill in `knowledge/`** — PRD template, product glossary, OKRs, Slack channel rules, dashboard index, team roster, per-person stakeholder profiles.
6. **Fill in `wiki/surface.yaml`** — add your active initiatives and the metrics you track weekly. This is what drives `/wiki-brief`.
7. **Run** `claude` from the repo root. Try `/morning` or `/weekly-pulse` to see it work end-to-end.

---

## Repository layout

```
.claude/commands/   slash command workflows
knowledge/          team-specific reference (PRDs, glossary, OKRs, team, people)
wiki/               LLM-maintained synthesis layer (metrics, decisions, briefs)
competitors/        per-competitor pricing and positioning profiles
scripts/            Playwright scrapers for competitor pricing
looker-toolbox/     drop your local Looker MCP binary here (gitignored)
reports/            generated competitor reports (gitignored)
kdb.config.md       single source of truth for URLs, channels, dashboards
CLAUDE.md           agent persona, rules, MCP tool list
.mcp.json           local MCP server wiring (gitignored)
```

---

## Inspiration

The `wiki/` layer is a direct implementation of Andrej Karpathy's [LLM Wiki pattern](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) (April 2025). The core idea: instead of re-querying raw sources at every turn (traditional RAG), an LLM incrementally builds and maintains a persistent, interlinked Markdown wiki — synthesising, cross-referencing, and annotating as it goes. Queries are answered from the synthesised wiki, not raw chunks.

KDB wires that pattern into a PM's daily workflow: `wiki/surface.yaml` defines what to track, the slash commands (`/wiki-brief`, `/wiki-ingest`, `/wiki-lint`) run the ingest/query/lint cycle, and Obsidian provides the graph view over the compounding knowledge base.

---

## Design choices

**Markdown as state.** Every config, knowledge file, and wiki entry is plain Markdown. The agent and a human can both read, write, diff, and review it through git. There is no database to migrate, no JSON schema to version.

**Approval gating, not autonomy.** Anything that writes to Slack, Jira, or Notion is drafted and previewed first. The PM has to say yes. This trades a little speed for predictable, auditable outputs.

**Wiki separate from Notion.** Notion owns living, human-edited documents (PRDs in flight, meeting notes, roadmaps). The wiki owns synthesis — extracted decisions, rationale, cross-references, temporal patterns — so retrieval is local, grep-able, and graph-able in Obsidian. Every derived page links back to its Notion source.

**Config-driven, not code-driven.** A PM with no shell experience can change which channel a brief posts to, switch Jira projects, or add a dashboard by editing one Markdown file.

**Live docs over training knowledge.** The LangChain MCP is wired in specifically so AI/RAG questions are answered against current documentation, not the model's frozen training set.

---

## Status

Template — fork and fill in the placeholders. `knowledge/`, `wiki/surface.yaml`, the competitor list, and `kdb.config.md` ship as scaffolding with worked examples. The slash commands, agent rules, wiki conventions, and MCP allow-list are production-shaped and ready to use.

The worked examples in `knowledge/` (PRD template, glossary, OKRs structure) are intentionally generic so you can see the expected format before replacing with your team's content.

---

## License

[MIT](./LICENSE) © 2025 Asif Laldin

Built by [@ald0405](https://github.com/ald0405).
