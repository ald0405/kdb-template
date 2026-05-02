# Competitor Pricing Scripts

Playwright-based scripts that capture competitor pricing pages and produce structured markdown reports. Works for any industry — configured via `kdb.config.md` and the JSON files below.

## First-time setup

You don't need to edit anything manually. Run `/competitor-pricing` or `/competitor-pricing-deep-dive` inside KDB and it will walk you through a guided setup: industry, categories, competitors, and key attributes to track.

If you prefer to configure manually:

```bash
cd scripts/competitor-pricing
npm install
node_modules/.bin/playwright install chromium
```

Then edit:
- `competitors.json` — full competitor list (all categories)
- `competitors-category.json` — focused list for the deep-dive command (include yourself with `"isSelf": true`)

Each entry needs: `name`, `slug`, `url`, and optional category-specific `categoryUrl`.

## Running

Use the slash commands inside KDB:
- `/competitor-pricing` — full multi-category run
- `/competitor-pricing-deep-dive` — deep-dive on your primary category, your product as baseline

## Output

Reports go to: `reports/competitor/runs/YYYY-MM-DD/` (gitignored — run locally).

Slash commands also update `competitors/competitive-matrix.md` and push a summary to Notion (`competitor_research_db` in `kdb.config.md`).

## Tips

- Run `/competitor-pricing-deep-dive` first — faster, gives a category-specific baseline.
- Include yourself in `competitors-category.json` with `"isSelf": true` so your prices anchor the comparison tables.
- Prices change frequently. Re-run at least monthly or before any pricing decision.
