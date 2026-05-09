---
name: canva-price-reduced
description: Price-reduction announcement — IG/FB posts + Canva briefs. Frames the reduction positively (as market alignment or seller flexibility, never as desperation). Also handles "Coming Back on Market" after a fall-through.
author: Al Pinder, Victoria Pinder
version: 1.0.0
---

# Canva Price Reduced

Price reductions are inevitable on some listings. The wrong post says "DESPERATE — TAKE OFFER" and turns buyers off. The right post reframes as a fresh market position. This skill writes the right one.

## When to use this skill

You're reducing the list price on an active listing. OR a deal fell through and you're going back-on-market. You need announcement content that:
- Frames positively (market alignment, seller flexibility, fresh opportunity)
- Doesn't violate FH (no demographic implication of why it didn't sell)
- Doesn't violate MLS scarcity rules ("won't last" still banned)
- Doesn't make the seller look desperate

## Prerequisite

Run [`canva-foundation`](../foundation/) first. Assumes folder `09-Price-Reduced/` with templates:
- `PR-Square-Photo-Forward` (1080×1080)
- `PR-Story-Reduction` (1080×1920)
- `PR-Back-On-Market` (1080×1080 — different framing for fall-throughs)

## Prompt

```
I'm a real estate agent. Build the price-reduction announcement.

LISTING DATA:
- Address: [ADDRESS]
- City: [CITY] [STATE]
- Original list price: $[ORIG]
- New list price: $[NEW]
- Days on market: [N]
- Reason for adjustment: [market alignment / seller flexibility /
  feedback-driven / pre-holiday / off-market period over /
  back-on-market after fall-through]
- Original cover photo URL: [URL]

REQUIRED COMPLIANCE (load fair-housing-overlay) — STRICT REDUCTION MODE:
- NEVER use "desperate", "must sell", "owner motivated" (NAR Code +
  fiduciary issue — discloses seller motivation publicly)
- NEVER use scarcity ("won't last", "act now", "rare opportunity")
- NEVER use "fixer-upper", "needs work" unless seller agreed (NAR Code)
- NEVER imply the home didn't sell because of the neighborhood / school /
  demographic
- ALWAYS positive — never knocks competing listings or buyer pool
- Frame reduction as MARKET ALIGNMENT or SELLER FLEXIBILITY only

OUTPUT — three pieces:

1. INSTAGRAM SQUARE CAPTION (≤2200 chars)
   - HEADLINE FRAMING: "Fresh Market Position" or "Updated Pricing"
     (NEVER "PRICE REDUCED")
   - Body: 3-5 sentences highlighting WHAT BUYERS GET at the new price
     (square footage, lot, features). NOT what changed about the seller.
   - 5 hashtags: 1 city, 1 #NewPrice or #FreshOpportunity, 2 listing-
     attribute, 1 brand
   - NO "won't last" / "act now" / "must see"

2. FACEBOOK POST (≤700 chars)
   - Conversational, ends with question to drive engagement
   - Same compliant framing
   - Link to listing OK

3. CANVA BRIEFS:
   a) PR-Square-Photo-Forward
      - HERO PHOTO: same as original listing
      - HEADLINE: "Fresh Pricing — [NEIGHBORHOOD]"
      - SUBHEAD: new price + key stats (beds/baths/sqft)
      - CTA: "See full details" + listing URL
      - License + brokerage + EHO logo footer
   b) PR-Story-Reduction
      - Vertical, same content, story-friendly aspect
      - "Swipe up" CTA → full listing

If reason=back-on-market-after-fall-through:
   Use PR-BACK-ON-MARKET template instead.
   Frame: "Fresh opportunity — [NEIGHBORHOOD] back on market"
   NEVER explains why prior deal fell through (NAR Code violation if it
   discloses prior buyer's circumstances)

Write all three.
```

## What good output looks like

A reduction announcement that buyer-leads engage with (because it's positive + specific) and seller approves (because it doesn't reveal motivation or imply desperation). Avoids the most common reductio-FH violations.

## Variants

**Multi-step reduction (2nd or 3rd reduction):**
Add: `Tone shifts further from "fresh" framing. Replace headline with "Best Value in [NEIGHBORHOOD] — [N] beds at $[NEW]". Stats-forward only. Avoid celebratory framing.`

**Coming back on market after a fall-through (no price change):**
Use template `PR-Back-On-Market` only. Drop price-change copy. Headline: "Back On Market — [NEIGHBORHOOD]". Body explains buyer's circumstances changed (compliant framing) without naming or details.

**Off-market period over (intentional pull + relist):**
Add: `Frame as "intentional refresh". Body explains seller pulled to make improvements/wait for season. Highlight what's different now.`

## Companion content

YouTube Episode 20: [Price Reduced + Coming Back on Market](https://youtube.com/@theprosperityagents)

## Compliance notes

Loads [`fair-housing-overlay`](../../fair-housing-overlay/). Reduction-specific rules — these violate frequently:

- **NAR Code Article 11:** never publicly disclose seller's motivation. "Owner motivated" / "must sell" / "desperate" are violations.
- **MLS scarcity rules:** "won't last", "act now", "rare opportunity" still banned even on reductions
- **FH:** never imply the home didn't sell because of the neighborhood, school, or demographic
- **Back-on-market specifically:** never discloses prior buyer's circumstances (financing fell through, inspection issues, etc.) — describe as "circumstances changed" only

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
