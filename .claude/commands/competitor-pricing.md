Capture and compare competitor pricing pages across your tracked categories.

## Arguments
$ARGUMENTS

If arguments are provided, treat them as space-separated homepage URLs to capture ad-hoc.
Otherwise run with no arguments to use the configured competitor list.

---

## Setup check

First, read `kdb.config.md` and `scripts/competitor-pricing/competitors.json`.

If `competitor_analysis.industry` is still `YOUR_INDUSTRY`, or the competitors list only contains example entries — **run guided setup before proceeding**:

### Guided setup (first-time only)

Ask the user these questions one at a time. Confirm each answer before moving on.

1. **Industry**: "What industry or market are you in? (e.g. 'online healthcare', 'B2B SaaS', 'consumer insurance', 'retail banking')"
2. **Your product**: "What's your product/company name and homepage URL?"
3. **Categories**: "What product categories or pricing lines do you want to track? List them. (e.g. 'annual plan, monthly plan, enterprise' or 'weight-loss, skincare, vitamins')"
4. **Key attributes**: "What matters most when comparing prices in your category? (e.g. 'monthly price, what's included, contract length, free trial')"
5. **Competitors**: "Who are your main competitors? Give me names and URLs — one per line."

After collecting all answers:
- Update `scripts/competitor-pricing/competitors.json` with the full competitor list
- Update `scripts/competitor-pricing/competitors-category.json` with your product (marked `"isSelf": true`) and competitors
- Update the `competitor_analysis` section of `kdb.config.md` with industry, categories, and key_attributes
- Show the user a summary of what you wrote and ask for confirmation before continuing to the capture step

---

## Execution steps

### Step 1 — Ensure dependencies are installed

```
cd ./scripts/competitor-pricing && npm install && node_modules/.bin/playwright install chromium
```

### Step 2 — Run the capture script

```
cd ./scripts/competitor-pricing && node capture.mjs
```

If arguments were provided:
```
cd ./scripts/competitor-pricing && node capture.mjs <url1> <url2> ...
```

### Step 3 — Read today's manifest

Read: `./reports/competitor/runs/YYYY-MM-DD/manifest.json` (today's date).

For each result where `error` is null:
- Read the screenshot at `screenshotPath` (view as image — you are multimodal)
- Read the text at `textPath`

For results where `error` is set, note the failure and move on.

### Step 4 — Find the previous run

List directories inside `./reports/competitor/runs/`. The previous run is the most recent directory that is NOT today's date. If none exists, this is the baseline run — note it and skip comparison.

If a previous run exists, read its manifest and content files for each competitor.

### Step 5 — Extract pricing intelligence

Read `kdb.config.md` to get:
- `competitor_analysis.categories` — the categories you track
- `competitor_analysis.key_attributes` — what to extract per competitor

For each competitor + category page captured, extract:
- Product/plan/treatment names and tiers
- Prices (with currency, billing period — monthly, annual, per-unit, per-course)
- What's included at each tier or price point
- Subscription vs. one-off vs. pay-per-use model
- Entry price / "from £X" positioning
- Any promotions, free trials, or guarantees

Adapt extraction to your category. The attributes from `kdb.config.md` define what matters.

### Step 6 — Compare with previous run

For each competitor with both a current and previous capture:
- Flag price changes, new offerings added, offerings removed
- Flag packaging or positioning changes
- Flag strategic moves (new category entered, free tier launched, etc.)

### Step 7 — Write the report

Save to: `./reports/competitor/YYYY-MM-DD.md`

```markdown
# Competitor Pricing Report — YYYY-MM-DD

## Summary
[2–3 sentences: key moves, who changed what, market signals]

## Change Alerts
[Only if changes vs. previous run exist]
- **Competitor — Category**: what changed

## By Category

### [Category Name]
| Competitor | Offerings | Entry Price | Included | Model | vs. Last Run |
|------------|-----------|-------------|----------|-------|--------------|

[One table per tracked category]

## Competitor Profiles

### [Competitor Name]
**Categories offered**: list
**Pricing**: [detail by category]
**Model**: [subscription/one-off/etc]
**vs. Previous**: [No change / describe changes]

## Not Offered (gaps)
| Competitor | Missing Categories |
|------------|--------------------|

## Failed Captures
[Any pages that errored]
```

Output the full report in the conversation.
