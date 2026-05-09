---
name: listing-presentation-builder
description: Takes property data + your selected comps and produces a structured listing-presentation brief — section-by-section content ready to drop into Gamma, Canva, or PowerPoint. Pairs with canva/listing-presentation for the visual deck.
author: Al Pinder, Victoria Pinder
version: 1.0.0
---

# Listing Presentation Builder

The text half of every listing presentation. Pair with [`canva/listing-presentation`](../canva/listing-presentation/) for the slide visuals. Together they replace 90-180 minutes of presentation prep with 10 minutes of input + 5 minutes of review.

## When to use this skill

You have a listing appointment Thursday at 6 PM. It's Tuesday morning. You've pulled comps. You know what the seller wants to hear. You need a presentation that:
- Walks through the comps with narrative, not just a grid
- Positions your price recommendation defensibly
- Lays out the marketing plan referring to specific Canva templates
- Includes a "why us" section with specific differentiators
- Has a clear timeline + next steps

## Prompt

```
I'm a real estate agent prepping a listing presentation. Build the brief.

PROPERTY DATA:
- Address: [ADDRESS]
- Beds/Baths/SqFt: [B] / [B] / [SQFT]
- Lot: [LOT] acres
- Year built: [YEAR]
- Condition: [excellent / good / fair / needs work]
- 3 standout features: [F1, F2, F3]
- 2 challenges (be honest): [C1, C2]

SELLER CONTEXT:
- Stated motivation: [downsizing / relocating / financial / no rush]
- Timeline: [needs to close by DATE / no timeline]
- Price expectation: $[AMOUNT]
- Has interviewed other agents? [yes — competing with X / no]

COMPS (3-6):
[Per comp: address, sold price, sold date, beds/baths/sqft, condition,
distance from subject]

MARKET CONTEXT:
- Town median sold (last 90 days): $[X]
- Town median DOM: [Y] days
- Active inventory: [Z]
- Source: [MLS / RPR / your data file]

REQUIRED COMPLIANCE (load fair-housing-overlay):
- NO school references
- NO demographic positioning
- NO scarcity language
- ALWAYS positive about competing properties + agents
- License + brokerage line in cover/footer

OUTPUT — 8 sections, brief format (200-300 words each):

1. COVER / AGENT INTRO — branded, license number, Equal Housing Opportunity
   logo placement note
2. MARKET CONTEXT — town-level data, neutrally framed
3. COMP SUMMARY — narrative through the strongest 3 comps (delegate to
   cma-narrative skill if available)
4. PRICE POSITIONING — where this listing slots, why
5. MARKETING PLAN — references specific skills: just-listed, open-house,
   mls-to-social, canva templates, plus a 6-channel grid
6. PRICING RECOMMENDATION — three numbers (low / recommended / high) with
   2-3 sentence rationale each
7. TIMELINE — week-by-week (week 1: prep + photos; week 2: launch +
   open house; etc.)
8. WHY US — specific differentiators (NEVER knocks competing agents).
   Format: "What we do" → "What that means for you"

Write all 8 sections.
```

## What good output looks like

A 4-page brief the seller can read on the kitchen counter and understand without your narration. Specific numbers throughout. Marketing plan that references real Canva templates the seller can preview. Timeline that fits their stated motivation.

## Variants

**Pre-listing CMA only (seller exploring, not committed):**
Add: `Drop sections 5, 6, 7 (marketing, recommendation, timeline). Output sections 1, 2, 3, 4 only as a CMA write-up. Use cma-narrative skill output as section 3.`

**Seller is interviewing 3 agents (competitive):**
Add: `Section 8 (Why Us) gets longer + more specific. Reference 3-5 differentiators with proof points. Never knocks competitors by name. Frame as "here's what we do that's specific" not "here's what others don't do."`

**Luxury ($1M+):**
Add: `Tone shifts to specific-detail-rich, longer sentences, restrained hype. Section 5 (Marketing Plan) emphasizes private-sphere channels (broker network email, off-market tour, video walkthrough) over MLS-blast.`

## Companion content

YouTube Episode 9: [Listing Presentation — From Comps to Brief](https://youtube.com/@theprosperityagents)

## Compliance notes

Loads [`fair-housing-overlay`](../fair-housing-overlay/). Listing-presentation-specific rules:
- Section 2 (Market Context) is the highest-FH-risk — never describes neighborhood by demographic, never compares "this town vs that town"
- Section 3 (Comp Summary) describes comps by objective property facts only
- Section 8 (Why Us) is the highest-NAR-Code-of-Ethics-risk — never knocks competing agents
- Cover slide MUST include license number + brokerage + EHO logo (pulled from canva-foundation Brand Kit)

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
