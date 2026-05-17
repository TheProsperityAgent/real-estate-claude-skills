# Stage 4 — Price Reduced (example run)

Reductions are inevitable on some listings. The wrong post reads "DESPERATE — TAKE OFFER" and turns buyers off. The right post reframes as a fresh market position. This stage writes the right one — and also handles "Back on Market" after a fall-through.

## What this stage blocks

- "Desperate", "must sell", "owner motivated" — NAR Code violation, public disclosure of seller motivation.
- "Won't last", "act now", "rare opportunity" — MLS scarcity rules.
- "Fixer-upper", "needs work" — only allowed if the seller explicitly agreed.
- Any explanation of why a prior deal fell through — discloses prior buyer's circumstances (NAR Code).
- Any implication that the home did not sell because of the neighborhood, school, or demographic.

## The prompt

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
- NEVER use "desperate", "must sell", "owner motivated"
- NEVER use scarcity, NEVER use "fixer-upper" unless seller agreed
- NEVER imply the home didn't sell because of neighborhood / school / demographic
- ALWAYS positive — never knocks competing listings or buyer pool
- Frame as MARKET ALIGNMENT or SELLER FLEXIBILITY only

OUTPUT — three pieces per the spec in SKILL.md.
```

## A real run — $385K → $375K, reason "market alignment"

**Inputs:**
- 1234 Honeysuckle Lane, Greenville NC
- Original list: $385,000
- New list: $375,000
- DOM: 42
- Reason: market alignment
- Cover photo: MLS image 03

**Output — Instagram square caption:**

> Updated pricing in Greenville. 1234 Honeysuckle Lane is now $375,000 — four bedrooms, three baths, 2,150 square feet on a fenced 0.6-acre lot. Primary suite on the main level and a screened porch off the back. Built in 2018 with recent systems.
>
> Same house, fresh number. If you have been on the fence, this is the right week to walk through it.
>
> #Greenville #NorthCarolina #NewPrice #ScreenedPorch #PinderTeam

Notice: "Updated pricing" instead of "PRICE REDUCED". "Fresh number" instead of "must sell". Body focuses on WHAT BUYERS GET at the new price, not what changed about the seller.

**Output — Facebook post:**

> Fresh pricing on the Greenville listing. 1234 Honeysuckle Lane — four bedrooms, primary on the main, screened porch, fenced 0.6 acres. Now at $375,000.
>
> Anybody have a buyer in the 4-bedroom $370-380K band in Greenville? This is in range now.

**Output — Canva brief (PR-Square-Photo-Forward, 1080×1080):**

- HEADLINE: `Fresh Pricing — Greenville`
- SUBHEAD: `$375,000 · 4 Bed · 3 Bath · 2,150 sqft`
- HERO PHOTO: original MLS image 03
- CTA: `See full details` + listing URL
- FOOTER: License + brokerage + EHO logo

**Output — Canva brief (PR-Story-Reduction, 1080×1920):**

- Same content, vertical layout
- SWIPE-UP-CTA: `Tap for full listing`

## Variant — Back on Market after fall-through

If `reason=back-on-market-after-fall-through`, the skill switches to template `PR-Back-On-Market` and the output looks different:

**IG caption:**
> Back on the market in Greenville. 1234 Honeysuckle Lane is available again — 4 bed 3 bath, primary on main, screened porch, 0.6 acres. Circumstances changed on the prior contract. Same house, fresh opportunity.

Notice: "circumstances changed" — does not name the buyer, does not say "financing fell through" or "inspection issues" (those disclose prior buyer's circumstances, NAR Code violation). The framing is forward, not backward.

## Variant — second or third reduction

The skill tone shifts further from "fresh" framing on a 2nd/3rd reduction. Headline becomes `Best Value in Greenville — 4 beds at $365K`. Stats-forward only. Drops celebratory framing.

## Before / After — what the skill BLOCKED

**Before (the way most agents post a reduction):**

> PRICE REDUCED! Owner motivated — must sell this week! Was $385K, NOW $375K — won't last at this price! Buyers, this is your chance — DM me NOW!

What just happened:
- "Owner motivated" / "must sell" → discloses seller motivation publicly. NAR Code Article 11 violation. Fiduciary issue.
- "Won't last" / "your chance" / "DM me NOW" → MLS scarcity rules + TCPA-adjacent pressure tactic.
- "PRICE REDUCED" caps headline → buyer-side reads it as desperation, kills negotiation leverage.

The skill output above gets the same buyer-side engagement (because it shows a fresh number on a real house) without leaking the seller's motivation to the entire market.

## Time saved

From-scratch: 30 minutes for IG + FB + Canva brief + FH compliance check.
With the skill: 5 minutes.

The bigger win is the seller-defensibility. When the seller asks "what does the price-reduction post say about me?" you can show them the output and demonstrate the framing protected their negotiation position.
