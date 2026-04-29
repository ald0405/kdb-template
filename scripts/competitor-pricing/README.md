# Competitor Pricing Scripts

Playwright-based scripts that capture competitor pricing pages and produce structured markdown reports.

## One-time setup

```bash
cd scripts/competitor-pricing
npm install
node_modules/.bin/playwright install chromium
```

## Config files

Edit these before your first run:

- `competitors.json` — full competitor list (multi-category)
- `competitors-weight-loss.json` — weight-loss category focus (include yourself for comparison)

Each entry needs: `name`, `slug`, `url`, and optional `pricingUrl`.

## Running

Use the slash commands inside KDB:
- `/competitor-pricing` — full multi-category run
- `/competitor-pricing-weight-loss` — weight loss deep-dive

## Output

Reports go to: `reports/competitor/runs/YYYY-MM-DD/` (gitignored — run locally).

The slash commands also update `competitors/competitive-matrix.md` and push a summary to the Notion `competitor_research_db` URL in `kdb.config.md`.

## Tips

- Run `/competitor-pricing-weight-loss` first — it's faster and gives you a category-specific baseline.
- Add yourself to `competitors-weight-loss.json` under `"isSelf": true` so your own prices appear in the comparison table.
- Prices change frequently. Re-run at least monthly or before any pricing decision.
