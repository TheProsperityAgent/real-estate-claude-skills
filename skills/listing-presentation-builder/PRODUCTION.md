# PRODUCTION — listing-presentation-builder

The "Thursday 6 PM listing appointment, it's Tuesday morning" skill. Produces the 8-section listing-presentation brief. Drop into Gamma (NOT Canva) for the final deliverable.

## Where this fits in the stack

```
Tuesday morning — pull comps, pull market data
Tuesday morning — run listing-presentation-builder (this skill)
  └── 8 sections, 200-300 words each
Tuesday morning — drop sections into Gamma → shareable URL
Thursday 6 PM — present to seller at the kitchen table
Friday — seller shares the Gamma link with their spouse/family
```

Replaces 90-180 minutes of presentation prep with 10 min input + 5 min review.

## Why Gamma, not Canva

Per the locked rule (see `canva-real-estate-lifecycle/README-on-canva-vs-gamma.md`):

- Canva is for visual-heavy, algorithm-targeted social content (Just Listed, Open House, Just Sold).
- Gamma is for text-heavy, narrative-driven seller decks the seller reads on their own.

Listing presentations live in Gamma. The seller reads it on their phone after you leave. The shareable URL renders responsively. Canva PDFs lose the kitchen-table read.

## The 8 sections — what each one does

| # | Section | Length | Purpose |
|---|---|---|---|
| 1 | Cover / Agent Intro | 200 words | License #, brokerage, EHO logo, headshots |
| 2 | Market Context | 250 words | Town-level data, NEUTRALLY framed |
| 3 | Comp Summary | 300 words | Delegates to `cma-narrative` if available |
| 4 | Price Positioning | 250 words | Objective factors only |
| 5 | Marketing Plan | 300 words | References `canva-real-estate-lifecycle` stages + 6-channel grid |
| 6 | Pricing Recommendation | 250 words | Low / recommended / high with 2-3 sentence rationale each |
| 7 | Timeline | 200 words | Week-by-week (week 1: prep; week 2: launch; etc.) |
| 8 | Why Us | 300 words | "What we do → What that means for you" — NEVER knocks competitors |

## The highest-NAR-risk section — Section 8

Section 8 (Why Us) is the highest-NAR-Code-of-Ethics-risk section. Most agents accidentally violate Article 12 by writing "unlike other agents" or "what most agents miss."

The skill enforces:
> "Here's what we do that's specific" — NEVER "here's what others don't do."

Frame your differentiators as POSITIVE statements about your service, never as negative comparisons against competing agents.

## The cover-slide compliance lock

Cover slide MUST include:
1. License number (e.g., `NC #334543`)
2. Brokerage name (e.g., `eXp Realty LLC`)
3. Equal Housing Opportunity logo

This is pulled from your Canva Brand Kit (foundation step from `canva-real-estate-lifecycle`). Pull once, save Gamma template, never rebuild.

If your cover slide is missing any of the three, the listing presentation is non-compliant in some states. The skill enforces all three.

## Skill chaining — Section 3 + cma-narrative

Section 3 of the listing presentation delegates to the `cma-narrative` skill. If you have that skill available, run it for Section 3 separately:

```
Run cma-narrative → 4-section CMA narrative
  └── Use Section 1 (Comp Summary) as Section 3 of listing presentation
```

This avoids duplicating effort + keeps Section 3 consistent with any standalone CMA you've shared.

## Variants

| Variant | Use case |
|---|---|
| Default | Standard residential listing presentation |
| Pre-listing CMA only | Seller exploring — drop sections 5, 6, 7 |
| Competitive (interviewing 3 agents) | Section 8 gets longer + more specific |
| Luxury ($1M+) | Specific-detail-rich, longer sentences, restrained hype |

## How this connects to Gamma

After running this skill:

1. Open Gamma. Create a new deck from your saved listing-presentation template.
2. Paste Section 1 (Cover) text into Gamma's cover slide.
3. Paste Sections 2-8 into their respective Gamma slides.
4. Gamma auto-formats with your brand fonts/colors (uploaded once in foundation step).
5. Generate shareable URL.
6. Send URL to seller as part of pre-meeting prep (so they can read in advance) OR after the meeting (so they can re-read with spouse).

## See also

- `examples/before-after.md` — a full kitchen-table listing presentation walk
- `examples/failure-modes.md` — the breaks
- `cron.example` — on-dispatch, no cron

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
