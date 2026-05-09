---
name: canva-cma-cover
description: Companion to cma-narrative. Generates Canva briefs for the visual half of a CMA — branded cover, comp visualization layouts, price-summary slide.
author: Al Pinder, Victoria Pinder
version: 1.0.0
---

# Canva CMA Cover

The visual half of every CMA. Pair with [`cma-narrative`](../../cma-narrative/) for the text. Together they replace 60-90 minutes of CMA prep.

## When to use this skill

You ran `cma-narrative` and have a 4-section write-up. You need the slide visuals that pair with it — cover, comp grid, price-range graphic, next-steps. This skill produces all four briefs.

## Prerequisite

Run [`canva-foundation`](../foundation/) first. Assumes folder `06-CMA-Covers/` with templates:
- `CMA-01-Cover` (subject property hero)
- `CMA-02-Comps-Grid` (3-6 comp visualization)
- `CMA-03-Price-Range` (low / recommended / high graphic)
- `CMA-04-Next-Steps` (sign-now or revisit-in-30 paths)

## Prompt

```
I'm a real estate agent. I have the cma-narrative output. Build the
Canva CMA visual briefs.

INPUT:
[Paste cma-narrative output]

PHOTOS:
- Subject property exterior URL: [URL]
- Comp photos (3-6): [URLs in same order as comp narrative]

SELLER CONTEXT:
- Name (with permission): [NAME]
- Date of CMA: [DATE]
- Seller's stated motivation: [from cma-narrative input]

REQUIRED COMPLIANCE (load fair-housing-overlay):
- License + brokerage line auto from Brand Kit
- EHO logo on cover
- NO school refs anywhere

OUTPUT — 4 slide briefs:

CMA-01-Cover:
- HERO PHOTO: subject property exterior
- HEADLINE: "Market Analysis for [STREET ADDRESS]"
- SUBHEAD: "Prepared for [SELLER NAME] · [DATE]"
- AGENT BLOCK: headshot + name + license + brokerage + EHO logo

CMA-02-Comps-Grid:
- HEADLINE: "Recent Sales Like Yours"
- 3-6 COMP CARDS in grid: each card = photo + address (last 4 digits) +
  beds/baths/sqft + sold price + sold date + DOM
- BOTTOM: 1-line summary stat (e.g., "Median: $X · Average DOM: Y days")

CMA-03-Price-Range:
- HEADLINE: "Where We Recommend Pricing"
- VISUAL: horizontal bar with three markers (LOW / RECOMMENDED / HIGH)
- BELOW each marker: dollar value + 1-line rationale (from cma-narrative
  section 4)
- FOOTER: data-source line

CMA-04-Next-Steps:
- HEADLINE: "Two Paths Forward"
- LEFT COLUMN: "Sign today" → 4 next-step bullets
- RIGHT COLUMN: "Revisit in 30 days" → 4 next-step bullets (with revised
  market check at the 30-day mark)
- AGENT SIGNATURE: name + phone + email + booking link

Write all 4 briefs.
```

## What good output looks like

Visual deck the seller takes home + reads at the kitchen table without your narration. Comp grid is photo-forward. Price range is unambiguous. Next-steps are concrete.

## Variants

**Investor CMA:**
Add: `Replace CMA-03 (price range) with cap-rate-range visualization. CMA-04 (next steps) replaces "sign today" with "make offer / pass / re-evaluate."`

**Quick-pulse CMA (homeowner curious about value):**
Add: `Drop CMA-04. Output only CMA-01, CMA-02, CMA-03. Educational tone — don't push a listing decision.`

**Luxury ($1M+):**
Add: `CMA-02 (comp grid) limits to 3 comps with larger photos + more detail. CMA-03 (price range) widens — luxury comp variance is higher.`

## Companion content

YouTube Episode 17: [CMA Cover That Sells the Meeting](https://youtube.com/@theprosperityagents)

## Compliance notes

Loads [`fair-housing-overlay`](../../fair-housing-overlay/). CMA-cover-specific rules:
- Comp addresses partially redacted (last 4 digits only) for buyer-privacy in some states
- License + EHO logo on cover (state license-board requirement for any property valuation)
- Never displays comp seller names or buyer names
- Never describes comp neighborhood by demographic

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
