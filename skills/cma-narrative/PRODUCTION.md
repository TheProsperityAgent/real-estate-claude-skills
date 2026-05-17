# PRODUCTION — cma-narrative

The "kitchen-table narrative" skill. Writes the 4-section CMA write-up that the seller reads at the counter without your narration. Pairs with Gamma (NOT Canva) for the visual layout.

## Where this fits

A listing appointment is on the calendar. You pulled comps. You have the subject property. The narrative is what closes the listing.

```
Pull comps (3-6) →
Run cma-narrative →
  Section 1: Comp Summary (~150 words prose)
  Section 2: Price Positioning (~150 words)
  Section 3: Market Context (~150 words)
  Section 4: Recommended List-Price Range (low/recommended/high)
→ Drop into Gamma →
Shareable URL to send the seller after the meeting
```

Replaces 60-90 minutes of CMA prep with 60 seconds of generation + 5 minutes of review.

## Why Gamma, not Canva

Earlier versions of this repo paired this skill with `canva/cma-cover`. Dropped on 2026-05-17.

Gamma renders responsively (phone, tablet, laptop), produces a shareable URL the seller can open from their car after the meeting, and presents narrative content better than Canva. Canva slide decks shared as PDF lose the kitchen-table read on mobile.

See `canva-real-estate-lifecycle/README-on-canva-vs-gamma.md` for the full split rule.

## The 4 sections — what each one does

**Section 1 — Comp Summary (~150 words, prose):**

Walks the seller through the strongest 3 comps. NOT a comp grid table — prose. Each comp framed by what it shares with subject + how it differs.

**Section 2 — Price Positioning (~150 words):**

Where this listing slots versus comps. Objective factors only: condition, sqft delta, lot size delta, year-built delta. Avoid subjective ("location is better").

**Section 3 — Market Context (~150 words):**

Town-level data — median sold last 90 days, DOM trend, active inventory. Sourced (MLS, RPR, etc.). NEUTRALLY FRAMED — never "buyer's market" / "seller's market" judgments.

**Section 4 — Recommended Range:**

Three numbers — low / recommended / high — with 2-3 sentence rationale each. The "recommended" aligns to seller's motivation:
- More aggressive if motivation is "no rush."
- More conservative if motivation is "relocating with a deadline."

## Why no buyer's-market / seller's-market framing

Locked rule across the repo: never describe a market segment as a loser. The skill rejects "buyer's market" or "seller's market" framing because:

1. Listing agents read CMAs. If yours frames the market as "buyer's market," the listing agent uses that against your seller.
2. "Seller's market" framing makes your pricing recommendation look like overconfidence.
3. Neutral numbers (median sold, DOM trend, active count) speak for themselves.

The skill outputs the data, never the labels.

## Variants

| Variant | Use case |
|---|---|
| Default | Residential resale, seller motivated to list |
| Investor | Rental property — section 3 becomes rental-comp, section 4 cap-rate-based |
| Pre-listing valuation | Seller exploring, not committed — 3 scenarios, no single push |
| Commercial / land | NOT this skill — methodology differs significantly |

## The price-range slot — how it works

Recommended slot depends on seller motivation:

```
motivation = "downsizing / no rush"
  → recommended = high end of low/recommended/high range (aggressive)
motivation = "relocating, deadline"
  → recommended = low end (conservative, accept the trade for speed)
motivation = "financial"
  → recommended = middle (balance)
```

The skill needs the motivation to slot correctly. Pass it explicitly:
```
seller_motivation: relocating with a deadline (must close within 60 days)
```

## How this connects to the larger stack

After the CMA narrative ships:
- `listing-presentation-builder` (Episode 9) uses Section 3 (Comp Summary) as Section 3 of the full listing presentation — skill chaining baked in.
- `cma-narrative` output → Gamma → shareable URL → seller's email + your follow-up.
- Once the seller signs the listing agreement, the listing flows into `canva-real-estate-lifecycle` (Coming Soon → Just Listed → ...).

## See also

- `examples/before-after.md` — a Greenville 4-bed CMA walk
- `examples/failure-modes.md` — the breaks
- `cron.example` — on-dispatch, no cron

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
