# Stage 2 — Just Listed (example run)

The single highest-frequency stage. Every new listing needs this in the first 24 hours — that is the highest-engagement window on every social platform.

## The prompt

```
I'm a real estate agent. New listing just hit MLS. Build the full
Just Listed package.

LISTING DATA:
- Address: [ADDRESS]
- City/Town: [CITY] [STATE]
- Price: $[PRICE]
- Beds/Baths: [B] / [B]
- Square footage: [SQFT]
- Lot: [LOT] acres
- Year built: [YEAR]
- 3 standout features:
  - [FEATURE 1]
  - [FEATURE 2]
  - [FEATURE 3]
- Cover photo URL (from MLS): [URL]
- Listing URL on my website: [URL]
- Open house date (if any): [DATE / TIME]

REQUIRED COMPLIANCE (load fair-housing-overlay):
- NO school references
- NO demographic targeting
- NO scarcity language ("won't last", "act now")
- ALWAYS positive about the neighborhood
- NO "safe" or "quiet" — use objective alternatives

OUTPUT — four pieces:

1. INSTAGRAM CAPTION (≤2200 chars, 5 hashtags, no link)
2. FACEBOOK POST (≤700 chars, conversational, ends with a question)
3. TIKTOK CAPTION (≤300 chars, 5 hashtags, punchy)
4. CANVA DESIGN BRIEF — for each of 3 templates:
   - Template name (from foundation skill)
   - Each placeholder text replaced (HEADLINE, SUBHEAD, etc.)
   - Photo selection guidance (which MLS photo to feature)
   - 1-line "skip this" guidance if a template doesn't fit

Hashtag rules: 5 max — 1 city, 1 state, 2 listing-attribute, 1 brand.
NEVER use #DreamHome, #YourNextHome, #MustSee — banned clichés.
```

## A real run — $385K Greenville 4-bed

**Inputs:**
- Address: 1234 Honeysuckle Lane
- Greenville, NC
- $385,000 · 4 bed · 3 bath · 2,150 sqft · 0.6 acre · built 2018
- Standouts: screened porch, level fenced backyard, primary on main
- Cover photo: MLS image 03 (exterior front)

**Output — Instagram caption:**

> Brand-new listing in Greenville. Four-bedroom, three-bath plan with a screened porch off the back that opens to a level, fenced backyard. Primary suite is on the main floor — single-story living is right there if you want it, and the upstairs gives you three more bedrooms plus a flex room. Built in 2018 so the systems are recent and the finishes are still current. The lot is 0.6 acres, which is generous for this part of Greenville. Twelve minutes to ECU Health Medical Center if that's your commute. Comment HONEYSUCKLE for the full photo set and listing link.
>
> #Greenville #NorthCarolina #FourBedroom #ScreenedPorch #PinderTeam

**Output — Facebook post:**

> New listing day in Greenville. 1234 Honeysuckle Lane — four bedrooms, three baths, 2,150 sqft, 0.6 acres, built 2018. Primary on the main level, screened porch off the back, fenced yard. We're 12 minutes from ECU Health.
>
> What's the one feature you'd want most in a 4-bedroom Greenville home — primary on the main, screened porch, or the fenced yard?

**Output — TikTok caption:**

> Just listed in Greenville. 4 bed 3 bath, primary on the main, screened porch off the back. Drop your favorite Greenville neighborhood below. #Greenville #NorthCarolina #JustListed #ScreenedPorch #PinderTeam

**Output — Canva brief (JL-Square-Photo-Forward, 1080×1080):**

- HEADLINE: `Just Listed in Greenville`
- SUBHEAD: `$385,000 · 4 Bed · 3 Bath · 0.6 acre`
- BUTTON: `See Photos`
- Photo: MLS image 03 (exterior front, golden-hour). Crop so the front door is centered. The button overlays bottom-right.
- Footer (auto from Brand Kit): `Al Pinder, NC #334543 · eXp Realty LLC` + EHO logo small bottom-left.

**Output — Canva brief (JL-Story-Photo-Forward, 1080×1920):**

- HEADLINE: `JUST LISTED`
- SUBHEAD-LINE-1: `Greenville, NC`
- SUBHEAD-LINE-2: `4 Bed · 3 Bath · 2,150 sqft`
- SWIPE-UP-CTA: `Tap for the full photo set`
- Photo: MLS image 03 cropped vertical, full-frame.
- Footer: same.

**Output — Canva brief (JL-Flyer-Letter, 8.5×11):**

- HEADLINE: `Just Listed — 1234 Honeysuckle Lane`
- HERO PHOTO: MLS image 03
- 3 BULLETS (objective only, no cliché):
  - Primary suite on the main level
  - Screened porch off the back
  - Fenced 0.6-acre lot
- PRICE BLOCK: `$385,000`
- AGENT BLOCK: headshot + name + license + phone
- FOOTER: License + brokerage + EHO logo

## Before / After — the compliance lift

**Before this skill (the way most agents write the caption from scratch):**

> Stunning move-in ready home in a great Greenville school district! This must-see 4 bedroom won't last long — perfect for a young family looking for a safe, quiet neighborhood. Hidden gem on a quiet street!

What just happened:
- "Great Greenville school district" — FHA school reference, blocked.
- "Must-see" + "won't last" — MLS data-quality clichés, downranks on Realtor.com and Zillow.
- "Perfect for a young family" — demographic targeting, FHA violation.
- "Safe, quiet neighborhood" — "safe" has no compliant replacement (safety perception varies by demographic — blocked), "quiet" gets rewritten to "low-traffic street" only if accurate.
- "Hidden gem" — banned cliché.

The skill's caption gets the same engagement (probably better, because Realtor.com and Zillow don't downrank it) without any of those violations. You ship faster and you don't have to defend the listing description in front of the broker.

## Time saved

- From-scratch: 45-60 minutes to write 3 captions + design 3 Canva pieces.
- With the skill: 8 minutes start to ship.

That gap is most of why Victoria's stack works. You earn 50 minutes back per listing.
