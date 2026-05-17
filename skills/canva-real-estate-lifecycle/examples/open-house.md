# Stage 3 — Open House (example run)

The highest-Fair-Housing-risk stage in the lifecycle. Open house flyers and yard-rider signs are where most agents accidentally cross a line — "family-friendly", "great schools nearby", "safe quiet neighborhood" all show up routinely. This skill blocks those by default and produces 8 assets in one pass.

## The 8 assets the stage produces

1. Printed flyer (8.5×11, OH-Flyer-Letter)
2. Yard-sign rider (24×6, OH-Sign-Rider-Strip)
3. Instagram announce post (1080×1080, OH-IG-Square-Announce)
4. Instagram day-of story (1080×1920, OH-IG-Story-Day-Of)
5. Facebook landscape post (1200×630, OH-FB-Landscape)
6. TikTok 30-second script outline + caption
7. Door-knocking 30-second script
8. Email to your owned (opted-in) list — never cold

## The prompt

```
I'm a real estate agent. Build the full Open House package.

LISTING DATA:
- Address: [ADDRESS]
- City: [CITY] [STATE]
- Price: $[PRICE]
- Beds/Baths/SqFt: [B] / [B] / [SQFT]
- 3 standout features: [F1, F2, F3]
- Cover photo URL: [URL]

OPEN HOUSE DETAILS:
- Date: [DATE]
- Time: [START - END]
- My phone for RSVP: [PHONE]
- Refreshments? [yes/no]
- Lender on-site? [yes/no — name and license if yes]

REQUIRED COMPLIANCE — STRICT MODE (load fair-housing-overlay):
- NEVER reference schools by name, district, ranking, or "great schools nearby"
- NEVER use "family-friendly", "great for families", "perfect for [demographic]"
- NEVER use "safe", "quiet" (use objective: "low-traffic street")
- NEVER use "tight-knit community", "exclusive", "private", "up-and-coming"
- NEVER use scarcity ("won't last", "rare opportunity", "act now")
- ALWAYS positive about every nearby town/neighborhood
- License + brokerage line on every printed asset
- EHO logo on flyer + sign rider

OUTPUT — all 8 assets per the spec above.
```

## A real run — $385K Sunday 1-3 PM

**Inputs:**
- 1234 Honeysuckle Lane, Greenville NC
- $385,000 · 4 bed · 3 bath · 2,150 sqft
- Standouts: screened porch, primary on main, fenced backyard
- OH: Sunday, June 1, 1:00-3:00 PM
- RSVP phone: 252-XXX-XXXX
- Refreshments: yes (coffee + cookies)
- Lender on-site: no

**Output — Printed flyer (OH-Flyer-Letter brief):**

- HEADLINE: `OPEN HOUSE — Sunday, June 1`
- SUBHEAD: `1234 Honeysuckle Lane · Greenville, NC`
- TIME: `1:00 PM – 3:00 PM`
- 3 BULLETS (objective only):
  - Primary suite on the main level
  - Screened porch off the back
  - Fenced 0.6-acre lot
- HERO PHOTO: MLS image 03 (exterior front, golden-hour)
- RSVP: `Stop by — coffee and cookies on. RSVP: 252-XXX-XXXX`
- FOOTER: License + brokerage + EHO logo

**Output — Yard-sign rider brief (OH-Sign-Rider-Strip, 24×6):**

- 5-word headline: `OPEN HOUSE THIS SUNDAY 1-3`
- Date + time only. No price, no features (the rider is glanceable from a moving car).

**Output — Instagram announce post:**

Caption (130 words):
> We're hosting an open house in Greenville this Sunday. 1234 Honeysuckle Lane — four bedrooms, three baths, primary suite on the main, screened porch off the back. The lot is fenced and a hair over half an acre. Built 2018, so the systems are still in their warranty window.
>
> Sunday, June 1, 1:00 to 3:00 PM. Coffee and cookies on. No pressure, no pitch — come walk through and ask whatever you want to know.
>
> Bring a friend if you want a second opinion. I'll be there the whole time.
>
> #Greenville #NorthCarolina #OpenHouse #ScreenedPorch #PinderTeam

Canva brief: template `OH-IG-Square-Announce`, hero photo MLS image 03, headline `OPEN HOUSE — SUNDAY 1-3`, subhead with address.

**Output — Instagram day-of story (sent Sunday morning):**

Brief: template `OH-IG-Story-Day-Of`. Hero photo full-bleed vertical. Top overlay: `OPEN NOW`. Bottom overlay: `Honeysuckle Lane · Greenville · until 3 PM`. Add a countdown sticker pinned to 3:00 PM.

**Output — Facebook landscape post:**

200 words conversational, ends with `Anyone in Greenville want a casual Sunday afternoon walk-through?`

**Output — TikTok 30-second script + caption:**

Script:
- 0-3 sec: Drone or walk-up exterior shot. Voiceover: "Open house Sunday in Greenville."
- 3-15 sec: Pan through the main level — primary suite door, screened porch, kitchen.
- 15-25 sec: Quick exterior — fenced backyard, lot view.
- 25-30 sec: Text overlay `Sunday, June 1, 1-3 PM · 1234 Honeysuckle Lane`. Voiceover: "Stop by, no pressure."

Caption (≤300 chars): `Open house Sunday in Greenville. 4 bed 3 bath, primary on main, screened porch off the back. 1-3 PM. Walk in, ask anything. #Greenville #NorthCarolina #OpenHouse #ScreenedPorch #PinderTeam`

**Output — Door-knocking 30-second script:**

> "Hi, I'm Al with the Pinder Team at eXp. We're hosting an open house this Sunday from 1 to 3 at 1234 Honeysuckle Lane — it's the house with the screened porch off the back. I wanted to drop a flyer off in case any of your friends or family are house-hunting in Greenville. Coffee and cookies will be on. No pressure, just neighborly. Have a good one."

10-second opener (neighborhood-context), 10-second pitch (invite, no pressure), 10-second close (flyer hand-off + walk).

**Output — Email to your opted-in list:**

Subject (≤50 chars): `Open house Sunday in Greenville · 1-3 PM`
Body (100 words): announce + features + RSVP + opt-out line at the bottom (TCPA-adjacent).

## Before / After — what this skill BLOCKED

Most agents writing an OH flyer from scratch produce something like this:

> Open House Sunday! Family-friendly home in great Greenville school district. Safe, quiet neighborhood. Tight-knit community. Won't last — must see! Stop by 1-3 PM.

What just happened — line by line:
- "Family-friendly" → demographic targeting, FHA violation.
- "Great Greenville school district" → FHA school reference.
- "Safe, quiet neighborhood" → "safe" forbidden (no compliant replacement), "quiet" gets rewritten only if there is objective data ("low-traffic street, 25 mph posted").
- "Tight-knit community" → coded demographic implication, FHA violation.
- "Won't last — must see" → MLS scarcity, blocked.

The skill output above ships all 8 assets with zero of those phrases. Same engagement, broker-defensible.

## Time saved

From-scratch: 90 minutes for the full 8-asset OH package + repeated FH-compliance review.
With the skill: 12 minutes input → review → Canva → print + schedule.
