---
name: canva-real-estate-lifecycle
description: One consolidated Canva skill that walks every active listing through its full lifecycle — Coming Soon → Just Listed → Open House → Price Reduced → Just Sold → Closing Day → Quarterly Postcard. Includes a one-time Brand Kit setup. Every output is Fair-Housing-clean and pairs caption + Canva design brief in a single pass. Listing presentations and CMA covers are intentionally NOT here — those move to Gamma.
author: Al Pinder, Victoria Pinder
version: 2.0.0
---

# Canva — Real Estate Lifecycle (One Skill, Every Stage)

Every listing has a lifecycle. Coming Soon, Just Listed, Open House, Price Reduced, Just Sold, Closing Day, and the Quarterly Postcard that keeps you in the farm. Most agents redesign the post from scratch every stage — by post three of the week the brand voice has drifted and the compliance footer is missing.

This is one skill for the whole lifecycle. You run a one-time foundation setup, then every stage produces the IG caption + FB post + TikTok caption AND the Canva design brief in a single pass. Same Brand Kit, same fonts, same compliance footer every time.

## What is NOT in this skill (read this first)

Two Canva stages got dropped on purpose:

- **Listing presentations** — you use [Gamma](https://gamma.app) for these. Walk through `listing-presentation-builder` for the brief, then drop it into Gamma. Slide decks read better in Gamma than Canva and you get a shareable URL the seller can open from their phone.
- **CMA covers** — same reason. Run `cma-narrative` for the write-up, drop it into Gamma. The kitchen-table read is cleaner.

If a skill output you ran somewhere else mentions "Canva listing presentation" or "Canva CMA cover" — that's older guidance, ignore it. Gamma owns those.

## When to use this skill

You took a new listing today. Or your listing is reducing price. Or a deal just closed. Or it's quarterly-postcard mail-drop week.

You want the caption(s) AND the Canva brief in one shot — so you open Canva, find the template, replace the placeholder text, and ship.

## The lifecycle (7 stages)

| Stage | When | Use which mode |
|---|---|---|
| Coming Soon | Listing hits MLS in 5-7 days | `--stage coming-soon` |
| Just Listed | New MLS listing, day 0-2 | `--stage just-listed` |
| Open House | OH scheduled, 5-7 days out | `--stage open-house` |
| Price Reduced | Active listing reduces price | `--stage price-reduced` |
| Just Sold | Deal closed, day 0 | `--stage just-sold` |
| Closing Day | Closing-table moment | `--stage closing-day` |
| Quarterly Postcard | Geographic farm, every 90 days | `--stage quarterly-postcard` |

## Prerequisite — one-time Brand Kit setup

Before you run any stage, set up your Canva Brand Kit once. This is what every stage assumes is in place.

### The 10 assets your Canva Brand Kit needs

1. Logo primary — 1080×1080 transparent PNG
2. Logo horizontal — for footers
3. Logo mono dark — single-color for over-photo overlays
4. Logo mono light — single-color for over-dark overlays
5. Headshots — Al solo, Victoria solo, Al+Victoria paired
6. Color palette — 5 hex codes (primary, secondary, accent, dark, light)
7. Font pair — display (headers) + body (everything else)
8. License + brokerage line — exact text (e.g., `Al Pinder, NC #334543 · eXp Realty LLC`)
9. NAP block — name + address + phone + email (uniform across every design)
10. EHO disclaimer — Equal Housing Opportunity logo + tagline (required on every printed asset)

### Folder convention inside your Canva account

```
Your Canva account/
├── 01-Just-Listed/
├── 02-Open-House/
├── 03-Just-Sold/
├── 04-Coming-Soon/
├── 07-Quarterly-Postcards/
├── 08-Closing-Day/
├── 09-Price-Reduced/
└── 99-Brand-Kit/
```

Notice 05 and 06 are missing on purpose — those would have been Listing Presentation and CMA Cover, both moved to Gamma.

### One-time Brand Kit prompt

```
I'm a real estate agent. Generate my Canva Brand Kit spec.

INPUTS:
- Agent name(s): [NAME(S)]
- Brokerage: [BROKERAGE]
- License number(s): [LICENSE NUMBERS]
- Phone, email, website: [...]
- City/State served: [CITY], [STATE]
- Brand vibe (3 words): [e.g., "warm, specific, calm"]
- Existing brand colors: [HEX or "I don't have any yet"]

REQUIRED COMPLIANCE:
- License + brokerage line on every design (NAR + state rules)
- EHO logo placement on every printed asset (FHA)
- No school refs in any sample copy

OUTPUT:
1. Color palette — 5 hex codes with use-case per code
2. Font pair — display + body, Google Fonts equivalent if Canva-Pro
   font isn't free
3. Logo asset list — 4 variants with dimensions
4. NAP footer block — copy-paste ready
5. Compliance footer block — copy-paste ready
6. Template-naming convention for the lifecycle
   (e.g., "JL-Square-Photo-Forward 1080x1080")
```

You run this once. You save the output to a doc. You build the Brand Kit + folder structure in Canva. From here on, every stage prompt just references the templates by name.

## Stage prompts

Each stage takes the same shape: load fair-housing-overlay, plug in the listing data + permission flags, run the prompt. Captions + Canva brief come out together.

### Stage 1 — Coming Soon

Pre-listing teaser package. Builds buzz without violating MLS pre-launch rules (you cannot claim "available now" until it hits the feed). See `examples/coming-soon.md` for the full prompt + a real run.

Highest-risk patterns: "available now", "schedule a showing", specific street address. The skill blocks all three.

### Stage 2 — Just Listed

Single highest-frequency Canva post — every new listing needs one in the first 24 hours. Outputs IG caption + FB post + TikTok caption + Canva briefs for three templates (`JL-Square-Photo-Forward`, `JL-Story-Photo-Forward`, `JL-Flyer-Letter`). See `examples/just-listed.md`.

### Stage 3 — Open House

The highest-Fair-Housing-risk stage in the repo. OH flyers and yard-rider signs are where most agents accidentally cross a line. The skill outputs 7 assets: printed flyer, yard-sign rider, IG announce post, IG day-of story, FB post, TikTok script outline, door-knocking 30-second script, opt-in email to your owned list. See `examples/open-house.md`.

### Stage 4 — Price Reduced

Frames the reduction positively (market alignment, seller flexibility) — never as desperation. Also covers "Back on Market" after a fall-through. Banned: "must sell", "owner motivated", "won't last". See `examples/price-reduced.md`.

### Stage 5 — Just Sold

Permission-aware. Defaults to no first name + no sale price + no client photo unless you set the permission flag to `yes`. Outputs IG + FB + TikTok + 2 Canva briefs + the 7-14-day testimonial-request DM. See `examples/just-sold.md` (paired with `--stage just-sold`).

### Stage 6 — Closing Day

The closing-table moment. Three quick assets: IG post + branded photo overlay + printed 5×7 thank-you card. Permission-aware by default. See `examples/closing-day.md`.

### Stage 7 — Quarterly Postcard

Geographic farming on a 90-day cycle. Four seasonal variants per year (Q1 year-end recap → Q2 spring snapshot → Q3 season transition → Q4 year-to-date). USPS EDDM-spec'd at 6.5×9. The Q3 mailing is the highest-FH-risk because most agents accidentally reference "back to school" — the skill enforces "season transition" framing only. See `examples/quarterly-postcard.md`.

## How the stages fit together

A normal listing runs through 4-5 stages:

```
Coming Soon (week -1) → Just Listed (day 0)
        → Open House (week 1-2) → Price Reduced (only if needed)
        → Just Sold (closing) → Closing Day (closing-table)
```

Plus the Quarterly Postcard running on its own 90-day track for whichever neighborhood you're farming.

Same Brand Kit through every stage. Same compliance footer. Same fonts. Caption tone shifts (excited → informative → celebratory → grateful) but the brand visual stays locked.

## Compliance — the patterns this skill always blocks

The skill loads `fair-housing-overlay` for every stage. On top of the overlay's baseline rules, each stage adds its own hard-block list:

- **Coming Soon:** "available now", "schedule a showing", full street address in the public teaser
- **Just Listed:** "must-see", "won't last", "great schools", any demographic targeting
- **Open House:** "family-friendly", "great schools nearby", "safe quiet neighborhood", "tight-knit community"
- **Price Reduced:** "desperate", "must sell", "owner motivated", explanations of why a prior deal fell through
- **Just Sold:** sale price without explicit permission, client first name without permission, closing-table photo without permission
- **Closing Day:** same permission gates as Just Sold, plus never frames closing as a "win" against another buyer/seller
- **Quarterly Postcard:** "back to school" (Q3 trap), school district references, ranking neighborhoods against each other

If you try to override one of these in your prompt, the skill explains why (cites the specific FHA / NAR Code / MLS rule) and offers a compliant alternative. You always have the final say — but you'll know what the system caught.

## Compliance notes — the footer that survives every stage

License + brokerage line and the EHO logo are non-optional on every design output. They live in the Brand Kit so once you set them up, every stage inherits them automatically. If a stage output references a template that doesn't have those baked in, you set up the Brand Kit wrong — run the foundation prompt again.

## Companion content

YouTube Season 2: [Canva Real Estate Lifecycle](https://youtube.com/@theprosperityagents) — a single episode per lifecycle stage, all linked back to this consolidated skill.

## Why this skill replaced 10 separate skills

In the early version of this repo every Canva stage was its own folder. You ended up loading 10 skills + duplicating the foundation explanation 10 times + jumping between folders to find a stage you needed. Consolidated, you load one skill and pick the stage. Less ceremony, same compliance, same Canva briefs.

Two stages (listing presentation, CMA cover) moved to Gamma intentionally — see the "What is NOT in this skill" section at the top.

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
