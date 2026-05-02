Deep competitor analysis for a specific category — by product, by tier/variant, across all competitors. Your product is included as the reference baseline.

## Arguments
$ARGUMENTS

If a category name is provided (e.g. `/competitor-pricing-deep-dive annual-plans`), use that as the focus category.
Otherwise use `competitor_analysis.deep_dive_category` from `kdb.config.md`.
Additional URLs in arguments are included as ad-hoc competitors in this run.

---

## Setup check

First, read `kdb.config.md` and `scripts/competitor-pricing/competitors-category.json`.

If `competitor_analysis.industry` is still `YOUR_INDUSTRY`, or the competitors list is empty or example-only — **run guided setup before proceeding**:

### Guided setup (first-time only)

Ask the user these questions one at a time. Confirm each answer before moving on.

1. **Industry**: "What industry or market are you in?"
2. **Your product**: "What's your product/company name, URL, and pricing page URL?"
3. **Focus category**: "Which category do you want to deep-dive on? (e.g. 'annual subscription', 'weight-loss programme', 'business insurance')"
4. **Pricing unit**: "What's the main pricing unit in this category? (e.g. 'per month', 'per dose', 'per policy', 'per seat')"
5. **Product variants**: "What are the main product variants, tiers, or SKUs in this category — yours and your competitors'? (e.g. plan names, product names, coverage levels, dosages)"
6. **Key attributes**: "Beyond price, what else matters when comparing? (e.g. 'what's included', 'minimum commitment', 'support model', 'delivery')"
7. **Competitors**: "Who are your main competitors in this category? Names and URLs."

After collecting answers:
- Update `scripts/competitor-pricing/competitors-category.json` with your product (marked `"isSelf": true`) and all competitors
- Update `kdb.config.md`: set `deep_dive_category`, `pricing_unit`, `product_variants`, and `key_attributes` under `competitor_analysis`
- Show a summary of what you wrote and confirm before proceeding

---

## Execution steps

### Step 1 — Install dependencies

```
cd ./scripts/competitor-pricing && npm install && TMPDIR=./scripts/competitor-pricing/tmp node_modules/.bin/playwright install chromium
```

### Step 2 — Run the capture script

```
cd ./scripts/competitor-pricing && TMPDIR=./scripts/competitor-pricing/tmp node capture-category.mjs
```

### Step 3 — Read the manifest

Read: `./reports/competitor/runs/YYYY-MM-DD/category/manifest.json` (today's date).

For each competitor, for each successfully captured page:
- Read the screenshot (view as image — spot pricing UI, toggle states, hidden tiers, promotional banners)
- Read the content.txt

Prioritise category-specific pages over homepage when both exist.

### Step 4 — Find the previous run

List directories in `./reports/competitor/runs/`. Find the most recent dated folder (not today) with a `category/` subdirectory. If found, read previous manifest and content for comparison. If first run, note as baseline.

### Step 5 — Extract pricing intelligence per competitor

Read `kdb.config.md` for:
- `competitor_analysis.pricing_unit` — the core unit (per month, per dose, per seat, etc.)
- `competitor_analysis.product_variants` — the tiers/SKUs to extract across competitors
- `competitor_analysis.key_attributes` — programme/package attributes to compare

For each competitor, extract:

**Per variant/tier/SKU:**
- Name and description
- Price (in local currency, per the configured `pricing_unit`)
- Whether price includes consultation/onboarding/setup
- Any promotional pricing (crossed-out prices, discount codes, limited offers)

**Package wrapper:**
- What's included beyond the core product
- Onboarding or support model
- Commitment: subscription / one-off / rolling
- Any guarantee or free trial

**Positioning signals:**
- Core claim / headline message
- Target audience signals
- Trust signals and credentials
- Active promotions or urgency messaging

### Step 6 — Build cross-competitor comparison tables

For each variant/tier/SKU offered by at least two competitors, build a table. Your product is always the first column — the reference baseline.

**[Variant/Tier/SKU Name]**
| | Your Product | Competitor A | Competitor B | Competitor C | Cheapest | Your position |
|---|---|---|---|---|---|---|
| Price ([pricing_unit]) | | | | | | |
| Included | | | | | | |
| Model | | | | | | |

Where pricing is gated or not published, mark as `—`.

### Step 7 — Programme/package comparison table

Use `competitor_analysis.key_attributes` from `kdb.config.md` as rows.

| Attribute | Your Product | Competitor A | Competitor B | Competitor C |
|-----------|-------------|-------------|-------------|-------------|
| [key_attribute_1] | | | | |
| [key_attribute_2] | | | | |
| [key_attribute_3] | | | | |

### Step 8 — Compare with previous run

If a previous run exists:
- For each competitor + variant: flag any price change (↑ or ↓ with amount and %)
- Flag new variants added or removed
- Flag programme/packaging changes
- Flag new promotions or messaging shifts

If no previous run: note as baseline.

### Step 9 — Write the report

Save to: `./reports/competitor/[category]-YYYY-MM-DD.md`

```markdown
# [Category] Competitor Deep-Dive — YYYY-MM-DD

**Your position**: [one-line summary of where your product sits vs. the market]

## Executive Summary
[3–5 bullets: the most important findings]

## Change Alerts
[Only if previous run exists and changes detected]
- **Competitor — Variant**: was £X, now £X (↑/↓ £X)

---

## Price Comparison by Variant

### [Variant/Tier Name]
[Cross-competitor table from Step 6]
**Key insight**: [1–2 sentences on positioning]

[Repeat per variant]

---

## Package Comparison
[Table from Step 7]
**Key insight**: [Where does your product win or lose on attributes vs. price?]

---

## Competitor Profiles

### [Competitor name]
**Variants offered**: list
**Pricing**: [variant by variant with amounts]
**Package**: [included, model, commitment]
**Positioning**: [their core message]
**Active promotions**: [any spotted]
**vs. Your product**: [direct comparison]
**vs. Previous run**: [No change / changes]

---

## Strategic Observations
[3–5 observations beyond price — positioning gaps, underserved segments, differentiation opportunities]

## Recommended actions
[2–3 specific, actionable recommendations]

## Failed Captures / Data Gaps
[Any pages that errored or where pricing was unavailable]
```

Output the full report in the conversation.
