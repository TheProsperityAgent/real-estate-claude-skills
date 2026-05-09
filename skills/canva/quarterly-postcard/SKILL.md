---
name: canva-quarterly-postcard
description: Geographic-farming postcard package — front + back Canva briefs, 4 seasonal variants per year, with FH-clean copy. Built for door-knocking + USPS Every Door Direct Mail.
author: Al Pinder, Victoria Pinder
version: 1.0.0
---

# Canva Quarterly Postcard

Geographic farming is the slow compounding strategy. One mailing per quarter to 200-500 addresses for 3-5 years builds you the dominant agent in a neighborhood. This skill produces all 4 seasonal variants in one pass.

## When to use this skill

You've picked a target neighborhood. You've subscribed to USPS EDDM (Every Door Direct Mail). You want a quarterly postcard that's:
- FH-compliant (no schools, no demographics, no scarcity)
- USPS-EDDM-spec'd (6.5×9, postage area free)
- Variant by season (so it doesn't feel like a template)
- Always positive about the neighborhood

## Prerequisite

Run [`canva-foundation`](../foundation/) first. Assumes folder `07-Quarterly-Postcards/` with templates:
- `QP-Q1-Year-End-Recap` (front + back)
- `QP-Q2-Spring-Snapshot` (front + back)
- `QP-Q3-Season-Transition` (front + back)
- `QP-Q4-Year-To-Date` (front + back)

## Prompt

```
I'm a real estate agent. Build the quarterly postcard package for a
target neighborhood.

NEIGHBORHOOD DATA:
- Neighborhood name: [NAME]
- City: [CITY] [STATE]
- Approximate address count: [N]
- Last quarter's market data:
  - Sold count: [X]
  - Median sold price: $[Y]
  - Median DOM: [Z] days
  - Active inventory: [N]
  - Source: [MLS, RPR, etc.]
- Standout neighborhood objective amenity: [park / commute time / something
  measurable that ALL residents experience the same — NEVER schools, NEVER
  "great community"]

REQUIRED COMPLIANCE (load fair-housing-overlay) — STRICT EDDM MODE:
- NEVER reference schools by name, district, ranking, or "good schools"
- NEVER use "family-friendly", "great for families", demographic targeting
- NEVER use "safe", "quiet" (replace with "low-traffic street" if accurate)
- NEVER use scarcity ("won't last", "rare opportunity")
- ALWAYS positive — never knocks competing agents / brokerages / builders
- License + brokerage line on every postcard
- EHO logo on every postcard (FHA + state requirement for printed RE marketing)

OUTPUT — 4 quarterly briefs (front + back each = 8 designs total):

QP-Q1-YEAR-END-RECAP (Jan/Feb mail-drop):
FRONT:
- HEADLINE: "[NEIGHBORHOOD] in [PRIOR YEAR]"
- 3 STAT TILES: "[X] homes sold" + "Median $[Y]" + "[Z] day average DOM"
- AGENT BLOCK: headshot + name + license + phone
BACK:
- 1-PARAGRAPH NARRATIVE: positive recap of last year's activity
- 1 RECENTLY-SOLD TEASE (anonymized): "We helped a buyer close on
  [TYPE] for $[PRICE]"
- CTA: "Curious what your home would sell for? Reply with [SHORT URL]"
- EHO logo + license footer

QP-Q2-SPRING-SNAPSHOT (Apr/May mail-drop):
FRONT:
- HEADLINE: "Spring 2026 in [NEIGHBORHOOD]"
- 3 STAT TILES: same format, Q1 numbers
- AGENT BLOCK
BACK:
- 1-PARAGRAPH NARRATIVE: positive spring-market snapshot
- 1 BUYER TEASE: "I'm working with [N] active buyers looking in
  [NEIGHBORHOOD]"
- CTA: same compliant pattern
- EHO logo + footer

QP-Q3-SEASON-TRANSITION (Aug/Sep mail-drop):
FRONT:
- HEADLINE: "[NEIGHBORHOOD] Mid-Year"
- 3 STAT TILES
- AGENT BLOCK
BACK:
- 1-PARAGRAPH: positive frame on season transition (NEVER mentions
  "back to school" — that's a demographic/age implication)
- 1 NEIGHBORHOOD OBJECTIVE FACT (the amenity from input)
- CTA
- Footer

QP-Q4-YEAR-TO-DATE (Nov/Dec mail-drop):
FRONT:
- HEADLINE: "[NEIGHBORHOOD] 2026 So Far"
- 3 STAT TILES (year-to-date)
- AGENT BLOCK
BACK:
- 1-PARAGRAPH: forward-looking, positive
- HOLIDAY FRAMING: "Thinking about a move next year? Let's start the
  conversation now."
- CTA
- Footer

Write all 4 quarterly briefs.
```

## What good output looks like

Four distinct postcards across the year that don't feel like templates. Each one has a real reason to exist (recap / spring / mid-year / year-to-date). Each one is FH-clean. Each one drives interested homeowners to a compliant CTA.

## Variants

**High-density urban farm (apartment buildings, condos):**
Add: `Replace "your home" CTA with "your unit." Stats focus on per-square-foot rather than total price (more useful for condo/co-op markets).`

**Rural / acreage farm:**
Add: `Stats include lot-size median + acreage range. Standout amenity should be objective rural feature (commute to nearest grocery, distance to county seat).`

**Just-moved-in welcome variant:**
Different timing — sent within 30 days of a property closing in the neighborhood. Replace "what's your home worth" CTA with "welcome to the neighborhood" framing.

## Companion content

YouTube Episode 18: [Quarterly Postcard for Geographic Farming](https://youtube.com/@theprosperityagents)

## Compliance notes

Loads [`fair-housing-overlay`](../../fair-housing-overlay/). Postcard-specific rules — these are the MOST scrutinized in printed RE marketing:

- **Q3 (back-to-school season) is the HIGHEST-FH-risk** — most agents accidentally violate by referencing school timing. The skill enforces "season transition" framing only.
- **EHO logo + license number on every printed postcard** is non-negotiable (FHA + state license-board requirement)
- **Neighborhood-positive framing only** — never "this neighborhood is better than X"
- **TCPA-adjacent: postcards are direct-mail (not regulated by TCPA), but the CTA should drive to opt-in channels (form/email), not phone-blast

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
