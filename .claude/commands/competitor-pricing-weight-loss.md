Deep competitor analysis of weight loss treatment pricing — by drug, by dose, across all competitors. Includes your product as the reference baseline.

## Arguments
$ARGUMENTS

If arguments are provided, treat them as additional competitor homepage URLs to include in this run.

## Competitors Analyzed

Competitors are defined in `./scripts/competitor-pricing/competitors-weight-loss.json`.

Edit that file to configure your own list. Include your product as the first entry (set `"slug": "your-product"`) so it appears as the reference baseline in all tables.

**Drugs tracked**: configure per competitor in the JSON — e.g. Wegovy, Mounjaro, Saxenda, Orlistat, Xenical, Alli

Your product is included as the reference baseline — all pricing and programme comparisons position you vs. the market.

To add competitors or update drug pages, edit `competitors-weight-loss.json` directly.

---

## Execution steps

### Step 1 — Install dependencies

```
cd ./scripts/competitor-pricing && npm install && TMPDIR=./scripts/competitor-pricing/tmp node_modules/.bin/playwright install chromium
```

### Step 2 — Run the capture script

```
cd ./scripts/competitor-pricing && TMPDIR=./scripts/competitor-pricing/tmp node capture-weight-loss.mjs
```

### Step 3 — Read the manifest

Read: `./reports/competitor/runs/YYYY-MM-DD/weight-loss/manifest.json` (today's date).

For each competitor, for each successfully captured page:
- Read the screenshot (you are multimodal — view it as an image to spot pricing UI, toggle states, hidden tiers)
- Read the content.txt

Prioritise drug-specific pages over the main page when both exist.

### Step 4 — Find the previous run

List directories in `./reports/competitor/runs/`. Find the most recent dated folder (not today) that contains a `weight-loss/` subdirectory. If found, read the previous manifest and content files for comparison. If this is the first run, note it as baseline.

### Step 5 — Extract pricing intelligence per competitor

For each competitor, extract a structured pricing breakdown:

**Per drug, per dose:**
- Drug name (Wegovy / Mounjaro / Orlistat / Xenical / Saxenda / Nevolat / Alli / Orlos)
- Active ingredient
- Dose (mg)
- Price per month (£)
- Price per pen/dose/unit where shown
- Whether price includes consultation
- Any promotional pricing (crossed-out prices, discount codes, limited offers)

**Programme wrapper:**
- What's included beyond medication: coaching, app, dietitian, community, blood tests, delivery
- Consultation model: online assessment, video call, async, GP referral
- Model: subscription / pay-per-order / subscription with cancel-anytime
- Delivery: included / charged / speed
- Money-back / guarantee
- Ongoing clinical support: what kind, how frequently

**Positioning signals:**
- Core claim / headline message
- Target audience signals (men, women, general)
- Trust signals: CQC, GPhC, NHS partnership, Trustpilot score
- Any active promotions or urgency messaging

### Step 6 — Build the cross-competitor price tables

Build one table per drug for every drug offered by at least two competitors. Column = competitor. Row = dose. Cell = monthly price. Add a "Your Product vs. cheapest" and "Your Product vs. market avg" column.

**[Drug Name] ([Active Ingredient])**
| Dose | Your Product | Competitor 1 | Competitor 2 | Competitor 3 | Cheapest | Your Product vs. Cheapest |
|------|-------------|-------------|-------------|-------------|----------|--------------------------|
| Xmg | £X | £X | £X | £X | £X (who) | +£X / -£X |

Repeat the same table structure for each drug tracked in `competitors-weight-loss.json`.

Where pricing is gated (assessment required) or not published, mark as `—` with a footnote.

### Step 7 — Programme comparison table

| Feature | Your Product | Competitor 1 | Competitor 2 | Competitor 3 |
|---------|-------------|-------------|-------------|-------------|
| Health coaching | ✓/✗ | | | |
| 1:1 dietitian | | | | |
| App | | | | |
| Blood tests included | | | | |
| Community | | | | |
| Delivery included | | | | |
| Consultation model | async/video/etc | | | |
| Subscription required | Y/N | | | |
| Cancel anytime | | | | |
| Money-back guarantee | | | | |
| Trust/regulatory badge | | | | |
| Trustpilot score | | | | |

### Step 8 — Compare with previous run

If a previous run exists:
- For each competitor + drug + dose: flag any price change (↑ or ↓ with £ amount)
- Flag any new drugs added or removed
- Flag programme/packaging changes
- Flag new promotions
- Flag messaging or positioning shifts

If no previous run: note this as the baseline.

### Step 9 — Write the report

Save to: `./reports/competitor/weight-loss-YYYY-MM-DD.md`

Report structure:

```markdown
# Weight Loss Competitor Pricing — YYYY-MM-DD

**Your position**: [one-line summary of where your product sits on price vs. the market]

## Executive Summary
[3–5 bullet points: the most important findings. What do you need to know today?]

## Change Alerts
[Only if previous run exists and changes detected]
- **Competitor — Drug — Dose**: was £X, now £X (↑/↓ £X)

---

## Price Comparison by Drug

### Wegovy (Semaglutide)
[Cross-competitor table as designed in Step 6]
**Key insight**: [1–2 sentences on price positioning]

### Mounjaro (Tirzepatide)
[Same]

### Orlistat-class (Orlistat / Xenical / Alli / Orlos)
[Same]

### Saxenda / Nevolat (Liraglutide)
[Only if applicable]

---

## Programme Comparison
[Table from Step 7]
**Key insight**: [What does your product include/lack vs. competitors?]

---

## Competitor Profiles

### [Competitor name]
**Drugs offered**: list
**Pricing**:
[Drug by drug, dose by dose with £ amounts]
**Programme**:
- Included: ...
- Model: ...
- Delivery: ...
- Guarantee: ...
**Positioning**: [their core message]
**Promotions active**: [any discount codes, limited offers spotted]
**vs. Your Product**: [direct comparison — where they're cheaper, where your product wins on programme]
**vs. Previous run**: [No change / changes]

---
[repeat for all competitors except your product]

---

## Strategic Observations
[3–5 observations about the market that go beyond price — positioning gaps, underserved segments, programme differentiation opportunities]

## Actions for your team
[2–3 specific, actionable recommendations based on the pricing intelligence]

## Failed Captures / Data Gaps
[Any pages that errored or where pricing was not available]
```

Then output the full report to the user in the conversation.
