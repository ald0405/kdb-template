# KDB: the AI assist

[![License: MIT](https://img.shields.io/badge/License-MIT-green?style=flat-square)](./LICENSE)
[![Built with Claude Code](https://img.shields.io/badge/Built%20with-Claude%20Code-5C4EE5?style=flat-square&logo=anthropic&logoColor=white)](https://claude.ai/code)
[![MCP](https://img.shields.io/badge/MCP-6%20servers-blue?style=flat-square)](https://modelcontextprotocol.io)
[![Inspired by LLM Wiki](https://img.shields.io/badge/Inspired%20by-Karpathy%20LLM%20Wiki-f5c518?style=flat-square&logoColor=black)](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)

> Named after Kevin De Bruyne, the assist king. Sets up your PMs to score.

Three layers, three jobs. KDB is the layer everyone skips: the one that makes the other two work for *you*.

![Model. Harness. Saddle. Three layers, three jobs.](assets/slide-model-harness-saddle.png)

---

## What this is

A config-driven PM copilot built on [Claude Code](https://docs.claude.com/en/docs/claude-code) and [MCP](https://modelcontextprotocol.io). Fork it, replace the placeholders with your stack, and every URL, channel, dashboard, person, and slash command lives in plain Markdown. The agent reads the config, loads only what it needs, and drafts before posting anywhere.

![KDB: one fill of the saddle. Four internal layers: wiki, knowledge base, config, MCP tools.](assets/slide-kdb-system.png)

---

## What it demonstrates

- **Multi-MCP orchestration** across 7 integrations: Notion, Figma, Slack, Amplitude, Atlassian/Jira, Looker (local toolbox), and LangChain docs
- **Composable slash commands**: 14 named workflows in `.claude/commands/`, each loading its own context bundle
- **Config-as-knowledge**: `kdb.config.md` + `knowledge/` + `wiki/surface.yaml` make agent behaviour data-driven, not code-driven
- **LLM-maintained wiki**: Obsidian-compatible, frontmatter schema, append-only context logs, monthly lint
- **Approval-gated writes**: draft first, explicit yes before any post to Slack, Jira, or Notion
- **Browser automation**: Playwright competitor pricing scrapers triggered by slash commands
- **Permissioned tool access**: explicit MCP allow-list in `.claude/settings.json`
- **Live docs**: LangChain MCP so AI/RAG questions never hit a stale training set

---

## Architecture

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

`CLAUDE.md` reads `kdb.config.md` first. Slash commands compose context from `knowledge/`, call MCP tools, and write synthesis back into `wiki/`.

---

## Slash commands

| Command | What it does |
|---------|--------------|
| `/data` | Pull metrics, query Looker or Amplitude, write data-driven briefs |
| `/comms` | Draft Slack messages, product updates, launch comms |
| `/product` | PRD work, Jira tickets, roadmap decisions, discovery |
| `/morning` | Daily pulse check |
| `/weekly-pulse` | Stakeholder scan + wiki brief in one pass |
| `/wiki-brief` | Pull metrics, annotate, file brief, update index + log |
| `/stakeholder-scan` | Pull latest Slack + Notion activity, update stakeholder profiles |
| `/prep` | Pre-meeting brief for a specific person |
| `/reflect` | Weekly reflection, files to `wiki/reflections/` |
| `/wiki-ingest {url}` | Ingest a Notion page or pasted notes into the wiki |
| `/decision {title}` | Capture a decision into `wiki/decisions/` |
| `/wiki-lint` | Monthly health check: orphans, stale pages, missing wikilinks |
| `/competitor-pricing` | Multi-category competitor pricing audit (guided setup on first run) |
| `/competitor-pricing-deep-dive` | Category deep-dive by variant/tier, your product as baseline (guided setup on first run) |

---

## Integrations

| Tool | Role |
|------|------|
| Notion | Read/write PRDs, meeting notes, roadmaps |
| Figma | Read design files and extract specs (read-only) |
| Slack | Draft and post channel updates |
| Amplitude | Query dashboards, funnels, experiments |
| Atlassian/Jira | Create, update, search issues |
| LangChain docs | Live documentation for AI/RAG questions |
| Looker | Query dashboards via local toolbox binary |

---

## Quickstart

**Prerequisites:** Node.js 18+, Claude Code CLI, Claude Pro or Team.

1. **Clone** this repo. Drop your Looker toolbox binary into `looker-toolbox/` (gitignored). Skip if you don't use Looker.
2. **Fill in `.mcp.json`** with your Looker URL, client ID, and secret (gitignored).
3. **Authenticate remote MCPs** via `claude mcp add --transport http <name> <url>` for Notion, Figma, Slack, Amplitude, Atlassian, and LangChain docs.
4. **Edit [`kdb.config.md`](./kdb.config.md)**: replace every `YOUR_...` placeholder with your real URLs, channel names, and Jira project key.
5. **Fill in `knowledge/`**: PRD template, glossary, OKRs, Slack channels, dashboard index, team roster, stakeholder profiles.
6. **Fill in `wiki/surface.yaml`**: add your active initiatives and weekly metrics. This drives `/wiki-brief`.
7. **Run** `claude` from the repo root. Try `/morning` or `/weekly-pulse`.

---

## Repo layout

```
.claude/commands/   slash command workflows
knowledge/          team reference (PRDs, glossary, OKRs, team, people)
wiki/               LLM-maintained synthesis (metrics, decisions, briefs)
competitors/        competitor pricing and positioning profiles
scripts/            Playwright scrapers for competitor pricing
looker-toolbox/     local Looker MCP binary (gitignored)
reports/            generated competitor reports (gitignored)
kdb.config.md       single source of truth for URLs, channels, dashboards
CLAUDE.md           agent persona, rules, MCP tool list
.mcp.json           local MCP server wiring (gitignored)
```

---

## Inspiration

The `wiki/` layer implements Andrej Karpathy's [LLM Wiki pattern](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) (April 2026): instead of re-querying raw sources at every turn, an LLM incrementally builds a persistent, interlinked Markdown wiki, synthesising and cross-referencing as it goes.

KDB wires that into a PM's daily workflow: `wiki/surface.yaml` defines what to track, `/wiki-brief`, `/wiki-ingest`, and `/wiki-lint` run the ingest/query/lint cycle, and Obsidian provides the graph view.

![The wiki: where context accumulates into a graph.](assets/slide-wiki.png)

---

## Design choices

**Markdown as state.** Every config, knowledge file, and wiki entry is plain Markdown. No database to migrate, no JSON schema to version.

**Approval gating, not autonomy.** Anything that writes to Slack, Jira, or Notion is drafted first. The PM says yes. Predictable, auditable.

**Wiki as automated context engineering.** Without the wiki, every prompt starts cold. With it, context accumulates: decisions link to metrics, metrics link to briefs, briefs link to stakeholder moves. `/wiki-lint` runs monthly to catch orphans and stale entries. Notion owns living documents; the wiki owns synthesis, with every derived page linking back to its Notion source.

**Config-driven, not code-driven.** Change which channel a brief posts to, switch Jira projects, or add a dashboard by editing one Markdown file.

**Live docs over training knowledge.** LangChain MCP answers AI/RAG questions against current documentation, not a frozen training set.

---

## Status

Template: fork and fill in the placeholders. `knowledge/`, `wiki/surface.yaml`, the competitor list, and `kdb.config.md` ship as scaffolding with worked examples. Slash commands, agent rules, wiki conventions, and the MCP allow-list are production-shaped and ready to use.

---

## License

[MIT](./LICENSE) © 2026 Asif Laldin

Built by [@ald0405](https://github.com/ald0405).
