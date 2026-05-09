---
name: canva-listing-presentation
description: Takes the listing-presentation-builder brief and produces Canva slide briefs — cover, market-context, comp visualizations, pricing strategy, marketing plan, timeline, and next-steps.
author: Al Pinder, Victoria Pinder
version: 1.0.0
---

# Canva Listing Presentation

The visual half of every listing presentation. Pair with [`listing-presentation-builder`](../../listing-presentation-builder/) for the text. Together they replace 90-180 minutes of presentation prep.

## When to use this skill

You ran `listing-presentation-builder` and have an 8-section brief. You need slide visuals that match. This skill produces the Canva brief for each slide so you can build the full deck in 15 minutes instead of 2 hours.

## Prerequisite

Run [`canva-foundation`](../foundation/) first. Assumes folder `05-Listing-Presentation/` with these 8 templates:
- `LP-01-Cover` (16×9 hero)
- `LP-02-Market-Context` (data slide)
- `LP-03-Comps-Summary` (3-photo grid + table)
- `LP-04-Price-Positioning` (scatter / range chart)
- `LP-05-Marketing-Plan` (6-channel grid)
- `LP-06-Timeline` (week-by-week)
- `LP-07-Why-Us` (3-column differentiator)
- `LP-08-Next-Steps` (signature page)

## Prompt

```
I'm a real estate agent. I have the listing-presentation brief. Build
the Canva slide briefs for the deck.

INPUT:
[Paste the output from listing-presentation-builder skill]

PROPERTY PHOTOS:
- Subject hero photo URL: [URL]
- Subject interior photos (3-5): [URLs]
- Comp photos (3-6): [URLs]

REQUIRED COMPLIANCE (load fair-housing-overlay):
- License number + brokerage line auto-pulled from canva-foundation Brand Kit
- Equal Housing Opportunity logo on COVER slide
- NO school references
- NO demographic positioning in any slide

OUTPUT — 8 Canva slide briefs:

LP-01-Cover:
- HERO PHOTO: subject property exterior
- HEADLINE: "Listing Strategy for [STREET ADDRESS]"
- SUBHEAD: "Prepared for [SELLER NAME] · [DATE]"
- AGENT BLOCK: headshot + name + license + brokerage
- EHO LOGO: bottom right corner

LP-02-Market-Context:
- HEADLINE: "[CITY] Market Today"
- 4 STAT BOXES: median sold (90 days), median DOM, active inventory, sold in past 30
- 1-PARAGRAPH NARRATIVE pulled from brief section 2
- FOOTER: data source line ("Source: MLS, [DATE]")

LP-03-Comps-Summary:
- HEADLINE: "How We Compare"
- 3-PHOTO GRID of strongest 3 comps with mini-stats below each
- 1-PARAGRAPH NARRATIVE pulled from brief section 3
- TABLE (subject vs comps): beds/baths/sqft/price/date

LP-04-Price-Positioning:
- HEADLINE: "Where We Slot"
- VISUAL: range chart with comp prices + recommended-price marker
- 3-LINE rationale from brief section 4

LP-05-Marketing-Plan:
- HEADLINE: "How We'll Sell It"
- 6-PANEL GRID: MLS launch · Pro photos + video · Just Listed social ·
  Open House · Direct mail · Broker network email
- Each panel: 1-line action + Canva template name reference

LP-06-Timeline:
- HEADLINE: "Week by Week"
- WEEK 1 → WEEK 2 → WEEK 3 → WEEK 4 columns with milestones each

LP-07-Why-Us:
- HEADLINE: "Why Choose Us"
- 3 COLUMNS: each shows "What we do" → "What that means for you"
- Specific differentiators ONLY (no knocking competitors)

LP-08-Next-Steps:
- HEADLINE: "Ready to Launch?"
- SIGNATURE BLOCK
- DATE + AGENT NAME
- LICENSE + BROKERAGE FOOTER

Write all 8 slide briefs.
```

## What good output looks like

Each slide brief is a 5-line spec the seller can hand to a designer (or paste into Canva themselves). Photos are pre-selected. Text is pre-written. The deck builds in 15 minutes of clicks.

## Variants

**Investor presentation:**
Add: `Replace LP-04 (price positioning) with cap-rate analysis. LP-05 (marketing plan) replaces "open house" with "investor network email + 1031-exchange outreach."`

**Pre-listing CMA-only meeting (seller exploring):**
Use only LP-01, LP-02, LP-03, LP-04. Skip the rest until seller signs.

**Luxury ($1M+):**
Add: `Tone shifts to specific-detail-rich. LP-05 (marketing plan) emphasizes private channels. LP-07 (Why Us) highlights specific past sales in the same price band (with anonymized addresses).`

## Companion content

YouTube Episode 16: [Listing Presentation — Comps to Canva](https://youtube.com/@theprosperityagents)

## Compliance notes

Loads [`fair-housing-overlay`](../../fair-housing-overlay/). Listing-presentation-specific rules:
- LP-02 (Market Context) is highest-FH-risk — never describes neighborhood by demographic
- LP-07 (Why Us) is highest-NAR-Code-risk — never knocks competing agents/brokerages
- COVER slide MUST include license number + EHO logo (state real estate license-board requirement)

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
