Capture and compare competitor pricing pages.

## Arguments
$ARGUMENTS

If arguments are provided, treat them as space-separated homepage URLs to capture ad-hoc.
Pass them to the script: `node ./scripts/competitor-pricing/capture.mjs <url1> <url2> ...`
Otherwise run with no arguments to use the configured competitor list.

## Competitors Analyzed

Competitors are defined in `./scripts/competitor-pricing/competitors.json`.

Edit that file to configure your own competitor list. Each entry needs: `name`, `slug`, `url`, and optional category-specific URLs.

**Categories tracked**: configure in `competitors.json` — e.g. weight-loss, erectile-dysfunction, hair-loss, testosterone

To add or update competitors, edit the JSON config file directly. Each entry has: name, slug, url, and category URLs.

---

## Execution steps

### Step 1 — Ensure dependencies are installed

Run the following to install npm dependencies and the Playwright browser (idempotent — safe to run every time):
```
cd ./scripts/competitor-pricing && npm install && node_modules/.bin/playwright install chromium
```

### Step 2 — Run the capture script

Run:
```
cd ./scripts/competitor-pricing && node capture.mjs
```

If arguments were provided, pass them to the script:
```
cd ./scripts/competitor-pricing && node capture.mjs <url1> <url2> ...
```

### Step 3 — Read today's manifest

Read the file: `./reports/competitor/runs/YYYY-MM-DD/manifest.json` (substitute today's actual date).

For each result where `error` is null:
- Read the screenshot at the `screenshotPath` field (view it as an image — you are multimodal)
- Read the text content at the `textPath` field

For results where `error` is set, note the failure and move on.

### Step 4 — Find the previous run

List the directories inside `./reports/competitor/runs/`. Sort them. The previous run is the most recent directory that is NOT today's date. If no previous run exists, skip the comparison step and note this is the baseline run.

If a previous run exists:
- Read `./reports/competitor/runs/PREVIOUS-DATE/manifest.json`
- For each competitor that succeeded in both runs, read their previous `content.txt`

### Step 5 — Extract pricing intelligence

Categories tracked: **weight-loss**, **erectile-dysfunction**, **hair-loss**, **testosterone**.

For each successfully captured competitor + category page, analyse the screenshot and text to extract:
- Treatment/product names (e.g. Mounjaro, Wegovy, Sildenafil, Finasteride)
- Prices (with £ amounts, billing period — monthly, per dose, per course)
- What's included (consultation, medication, delivery, coaching)
- Subscription vs. one-off vs. pay-per-treatment model
- Any "from £X/month" entry points
- Free trial, trial period, or money-back guarantees

### Step 6 — Compare with previous run

For each competitor + category with both a current and previous capture:
- Compare extracted pricing and treatment offerings
- Identify: price changes, new treatments added, treatments removed, packaging changes, messaging shifts
- Flag strategic moves (e.g. a competitor adding a new category, dropping prices, launching a free tier)

### Step 7 — Write the report

Save a markdown report to: `./reports/competitor/YYYY-MM-DD.md`

Report structure:

```
# Competitor Pricing Report — YYYY-MM-DD

## Summary
[2-3 sentences: key moves, who changed what, market signals]

## Change Alerts
[Only populate if changes vs. previous run exist]
- **Competitor — Category**: what changed

## By Category

### [Category Name]
| Competitor | Treatments | Entry Price | Included | Model | vs. Last Run |
|------------|------------|-------------|----------|-------|--------------|
| [Competitor] | ... | £X/mo | ... | subscription | — |

[Repeat a table per tracked category — only include competitors that offer it]

## Competitor Profiles

### [Competitor Name]
**Categories offered**: list only what they offer

#### [Category Name]
**Page**: [URL]
- Treatments: [list]
- Pricing: [details]
- Model: [subscription/one-off/etc]
- vs. Previous: [No change / describe what changed]

#### [next category]
...

---
[repeat for each competitor]

## Not Offered (gaps)
| Competitor | Missing Categories |
|------------|--------------------|
| [Competitor] | [categories not offered] |
| ... | ... |

## Failed Captures
[Any pages that errored, with reason]
```

Then output the full report to the user in the conversation.
