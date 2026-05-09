---
name: cma-narrative
description: Generates the full narrative section of a Comparative Market Analysis from a list of comps + the subject property. Output is a 4-section CMA write-up — comp summary, price-positioning rationale, market context, and recommended list-price range.
author: Al Pinder, Victoria Pinder
version: 1.0.0
---

# CMA Narrative

Writes the WRITE-UP half of a CMA in 60 seconds. Pair with [`canva/cma-cover`](../canva/cma-cover/) for the visual half. Together they replace 60-90 minutes of CMA prep.

## When to use this skill

You have a listing appointment in 48 hours. You've pulled 3-6 comps. You have the subject property data. You need to convert that into a defensible narrative — not just a comp grid, but the STORY of why this seller should price where you recommend.

This skill writes that story.

## Prompt

```
I'm a real estate agent. Write the narrative section of a CMA.

SUBJECT PROPERTY:
- Address: [ADDRESS]
- City: [CITY] [STATE]
- Beds/Baths/SqFt: [B] / [B] / [SQFT]
- Lot: [LOT] acres
- Year built: [YEAR]
- Condition: [excellent / good / fair / needs work]
- Standout features: [F1, F2, F3]
- Seller's stated motivation: [downsizing / relocating / financial / no rush]
- Seller's price expectation (if known): $[AMOUNT]

COMPS (provide 3-6):
[For each comp, paste: address, sold price, sold date, beds/baths/sqft,
days on market, condition, distance from subject in miles]

REQUIRED COMPLIANCE (load fair-housing-overlay):
- NO school references in market context
- NO demographic-based positioning
- NO scarcity language ("won't last")
- ALWAYS positive about competing properties + agents

OUTPUT — 4 sections, ~150 words each:

1. COMP SUMMARY — narrative paragraph that walks the reader through the
   strongest 3 comps. Frame each by what it shares with subject + how it
   differs. No comp-grid table; this is prose.

2. PRICE POSITIONING — where this listing slots vs comps. Use objective
   factors: condition, sqft delta, lot size delta, year-built delta. Avoid
   subjective ("location is better"). Recommend a position relative to comps
   (e.g., "5% below median comp due to deferred maintenance").

3. MARKET CONTEXT — town-level data: median sold last 90 days, days-on-
   market trend, active inventory count. Source the data (MLS, Realtors
   Property Resource, etc.). Frame neutrally — never "buyer's market" /
   "seller's market" judgments.

4. RECOMMENDED LIST-PRICE RANGE — three numbers (low, recommended, high)
   with a 2-3 sentence rationale for each. The "recommended" should align
   with seller's motivation: more aggressive if motivation is "no rush,"
   more conservative if "relocating with a deadline."

Write all four sections.
```

## What good output looks like

A defensible narrative the seller can read at the kitchen table and follow without asking questions. Each section flows into the next. The recommended range is grounded in the comps and the seller's situation, not pulled from thin air.

## Variants

**Investor-targeted CMA (rental property):**
Add: `Replace section 3 (market context) with rental-comp section: monthly rent range for similar properties, current rental yield, 1-year forward projection. Replace section 4 with cap-rate-based price recommendation.`

**Pre-listing valuation (seller exploring, not committed):**
Add: `Tone shifts to educational rather than persuasive. Present 3 scenarios (price-aggressive / price-comp / price-conservative) with expected days-on-market for each. Don't push a single recommendation.`

**Commercial / land:**
Different methodology. Use [`canva/cma-cover`](../canva/cma-cover/) for layout but write the narrative manually for these — comp logic differs significantly from residential.

## Companion content

YouTube Episode 6: [CMA Narratives in 60 Seconds](https://youtube.com/@theprosperityagents)

## Compliance notes

Loads [`fair-housing-overlay`](../fair-housing-overlay/). CMA-specific rules:
- Market context never uses demographic shorthand ("changing neighborhood", "up-and-coming")
- Comps are described by objective property facts only (no "this comp is in a more desirable area")
- Never knocks competitors' listings or pricing decisions
- Always positive framing of every comp's location

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
