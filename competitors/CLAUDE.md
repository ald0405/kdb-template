# Competitors

Per-competitor pricing profiles and competitive intelligence.

---

## Doc index

| File | Description |
|------|-------------|
| `competitive-matrix.md` | Living cross-competitor comparison. Updated after each `/competitor-pricing` run. |
| `{slug}/{slug}.md` | Entity summary page — 3-5 bullet executive summary with YAML frontmatter. |
| `{slug}/pricing.md` | Latest multi-category pricing detail. |
| `{slug}/pricing-weight-loss.md` | Latest weight loss drug-by-dose pricing (from `/competitor-pricing-weight-loss`). |

**Quick read:** Start with `competitive-matrix.md`. Go to a specific competitor's entity page for a fast summary. Use `pricing.md` for detail.

**After any new run:** The `/competitor-pricing` commands update these files automatically. Also push to Notion (competitor_research_db in kdb.config.md).

---

## Competitor index

<!-- Add a row for each competitor you add to this folder. -->

| Folder | Categories tracked | Relevance |
|--------|--------------------|-----------|
| `example-competitor/` | category-1, category-2 | Direct / Adjacent |

---

## How to use
- **Before a pricing run:** Check if `competitive-matrix.md` has a recent enough baseline.
- **For strategic decisions:** Read competitor entity pages for positioning, then `competitive-matrix.md` for where you sit on price.
- **For a specific competitor:** Read their entity page first, then `pricing.md` for detail.
