# Stage 1 — Coming Soon (example run)

Pre-listing marketing is a minefield. Most state MLS rules require the listing to be IN the MLS before any "available now" / "showings open" claims. This stage produces teaser content that builds excitement without triggering the rule violation.

## What the skill blocks

- "Available now" / "available" / "showings open" / "schedule a showing" — these claim availability before MLS go-live.
- Specific street address in the public teaser — neighborhood-level only.
- Scarcity ("won't last", "act now").
- Demographic targeting and school refs (inherited from `fair-housing-overlay`).

## The prompt

```
I'm a real estate agent. Listing hits MLS in [N] days. Build teaser package.

LISTING DATA:
- Address: [ADDRESS — DO NOT INCLUDE IN PUBLIC TEASER per MLS rules]
- City/area: [CITY] [STATE]
- Approximate size + style: [SQFT range, beds, style — pre-MLS-compliant vague]
- 1 standout teaser feature: [ONE intriguing detail]
- Cover photo URL: [URL]
- MLS go-live date: [DATE]

SELLER PERMISSION:
- OK to tease publicly? [yes / no]
- OK to share neighborhood? [yes — at neighborhood level only / no]

REQUIRED COMPLIANCE (load fair-housing-overlay) — STRICT MLS PRE-LAUNCH MODE:
- NEVER use "available now" / "schedule a showing" — claims availability
- NEVER use scarcity, NEVER use schools, NEVER target by demographic
- DEFAULT framing: "Coming Soon — [NEIGHBORHOOD] — [GO-LIVE DATE]"
- Include "Pre-Launch Disclaimer" line on Canva flyer:
  "Listing not yet active in MLS. Subscribe for the launch alert at [URL]."

OUTPUT — five pieces per the spec in SKILL.md.
```

## A real run — Greenville teaser, MLS go-live in 5 days

**Inputs:**
- Neighborhood: Brook Valley
- City: Greenville NC
- Size: ~2,100 sqft, 4 bed, traditional
- Teaser feature: screened porch with sunset views
- Cover photo: porch detail crop
- MLS go-live: Sunday, June 1
- Seller permission to tease publicly: yes
- OK to share neighborhood: yes (neighborhood-level only)

**Output — Instagram square teaser caption:**

> We are listing a four-bedroom in Brook Valley next Sunday. The one feature we couldn't get over: the screened porch faces west, the sky over the lot turns gold at 7 PM, and the porch ceiling has a fan we are not going to pretend isn't life-changing for July in Greenville. More photos drop Sunday June 1.
>
> Want the first look? Comment SUNSET and we will send you the launch alert when it goes live in MLS.
>
> #ComingSoon #Greenville #NorthCarolina #ScreenedPorch #PinderTeam

Notice: no street address, no price, no "available now", no demographic targeting. The CTA drives to a list signup, not a showing request.

**Output — Facebook teaser post:**

> Coming soon to Brook Valley in Greenville. Four-bedroom traditional with a screened porch that faces west — golden-hour on that porch is something. MLS go-live is Sunday June 1.
>
> If you want the launch alert before it hits the feed, comment SUNSET and I'll get you on the list.

**Output — TikTok 10-second script:**

- 0-3 sec: Porch detail crop, no full house visible. Voiceover: "We can't show this listing yet."
- 3-8 sec: Slow pan of the porch ceiling fan + the view past it. Voiceover: "But the sunset off this porch."
- 8-10 sec: Text overlay `Coming June 1 · Brook Valley · Greenville`. Voiceover: "Comment SUNSET for the launch alert."

Caption (≤300 chars): `Coming soon to Brook Valley. Screened porch faces west — golden-hour situation. MLS go-live June 1. Comment SUNSET for the launch alert. #ComingSoon #Greenville #NorthCarolina #ScreenedPorch #PinderTeam`

**Output — Canva brief (CS-Square-Mystery, 1080×1080):**

- HEADLINE: `Coming Soon · Brook Valley`
- SUBHEAD: `Listing Sunday, June 1`
- CTA: `Comment SUNSET on Instagram for the launch alert`
- Hero image: porch detail crop (NOT a full house shot — pre-MLS rule)
- Pre-launch disclaimer (small footer): `Listing not yet active in MLS. Subscribe for the launch alert at [LANDING PAGE URL].`
- Brand Kit footer: license + brokerage + EHO logo

**Output — Canva brief (CS-Story-Reveal-Date, 1080×1920):**

Vertical layout, countdown sticker pinned to June 1, swipe-up CTA to the launch landing page.

**Output — Email to your opted-in list (NOT cold):**

Subject: `Coming June 1 to Brook Valley — first look`
Body (100 words):
> Quick heads up — we are listing a 4-bedroom traditional in Brook Valley next Sunday. The screened porch off the back faces west and the sunset over the lot is the thing we keep coming back to. MLS go-live is Sunday, June 1. If you want the first-look photo set the morning it goes live, reply to this email and we'll send it to you before it hits the feed. — Al & Victoria
>
> [opt-out footer: You're receiving this because you subscribed at pinder-team.com. Reply STOP to opt out.]

## Why the address is missing on purpose

NC, FL, NY, CA all have variants of the same rule: listings cannot claim "available" status — and printed/digital marketing should not lead the public to an unsanctioned showing — until they are active in the MLS. Most agents accidentally violate this by putting the full address on a Coming Soon flyer. The skill strips it by default. The CTA drives interested buyers to a list signup so when the MLS goes live, they get the alert through a compliant channel.

## Time saved

From-scratch: 60 minutes to write 3 captions + 2 Canva briefs + email + verify MLS rule compliance.
With the skill: 7 minutes start to ship.
