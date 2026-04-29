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
