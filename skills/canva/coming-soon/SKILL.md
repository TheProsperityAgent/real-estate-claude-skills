---
name: canva-coming-soon
description: Pre-listing teaser package — IG/FB/TikTok captions + Canva briefs that build anticipation without violating MLS coming-soon rules (you can't market it as available before MLS).
author: Al Pinder, Victoria Pinder
version: 1.0.0
---

# Canva Coming Soon

Pre-listing marketing is a minefield. Most state MLS rules require the listing to be IN the MLS before any "available now" / "showings open" claims. This skill produces teaser content that builds excitement without triggering the rule violation.

## When to use this skill

You have a listing about to hit MLS in 5-7 days. The seller wants pre-launch buzz. You need teasers that:
- Build anticipation without claiming availability
- Stay inside MLS rules + state license-board rules
- Don't violate Fair Housing
- Drive interested buyers to the launch (not direct showings yet)

## Prerequisite

Run [`canva-foundation`](../foundation/) first. Assumes folder `04-Coming-Soon/` with templates `CS-Square-Mystery` (1080×1080) and `CS-Story-Reveal-Date` (1080×1920).

## Prompt

```
I'm a real estate agent. Listing hits MLS in [N] days. Build teaser package.

LISTING DATA:
- Address: [ADDRESS — DO NOT INCLUDE IN PUBLIC TEASER per MLS rules]
- City/area: [CITY] [STATE]
- Approximate size + style: [SQFT range, beds, style — vague enough to be
  pre-MLS-compliant]
- 1 standout teaser feature: [ONE intriguing detail — e.g., "screened porch
  with sunset views" or "workshop with epoxy floor"]
- Cover photo URL: [URL]
- MLS go-live date: [DATE]

SELLER PERMISSION:
- OK to tease publicly? [yes / no]
- OK to share neighborhood? [yes — at neighborhood level only / no]

REQUIRED COMPLIANCE (load fair-housing-overlay) — STRICT MLS PRE-LAUNCH MODE:
- NEVER use "available now" / "available" / "showings open" / "schedule
  a showing" — these claim availability before MLS go-live
- NEVER use scarcity ("won't last", "act now")
- NEVER reference schools (FH)
- NEVER target by demographic (FH)
- ALWAYS positive about the neighborhood
- DEFAULT framing: "Coming Soon — [NEIGHBORHOOD] — [GO-LIVE DATE]"
- Include "Pre-Launch Disclaimer" line on Canva flyer:
  "Listing not yet active in MLS. For information when it goes live,
   subscribe to our list at [URL]."

OUTPUT — five pieces:

1. INSTAGRAM SQUARE TEASER CAPTION (≤2200 chars)
   - Single-feature reveal (e.g., "We're listing a [STYLE] in [NEIGHBORHOOD]
     next [DAY]. Here's the one feature we couldn't believe...")
   - 5 hashtags including #ComingSoon

2. FACEBOOK TEASER POST (≤700 chars)
   - Same single-feature angle, conversational
   - "Subscribe to our list" CTA (compliant) instead of "schedule a showing"

3. TIKTOK 10-SEC SCRIPT
   - Hook: "We can't show this listing yet, but..."
   - 5-second feature reveal
   - 3-second CTA: "Comment COMING SOON for the launch alert"

4. CANVA BRIEFS:
   a) CS-Square-Mystery
      - Visual: silhouette of property OR detail crop (door, window, view)
      - Text: "Coming [DATE]" + neighborhood
      - NO address, NO price, NO beds/baths
   b) CS-Story-Reveal-Date
      - Vertical: countdown sticker + "Subscribe for launch" CTA
      - Same restrictions

5. EMAIL TO YOUR OPTED-IN LIST (NOT cold)
   - Subject: ≤50 chars
   - Body: 100 words, includes "save the date" framing + opt-out

Write all five.
```

## What good output looks like

Buzz that builds list signups + engagement without crossing MLS rules. Drives interest to launch day instead of unsanctioned pre-MLS showings.

## Variants

**Off-market network-only (broker-network teaser):**
Different rules — you CAN claim availability to the broker network as long as the listing is in your office's MLS-pending state. Use [`canva/just-listed`](../just-listed/) instead.

**Coming-back-on-market (after a fall-through):**
Use [`canva/price-reduced`](../price-reduced/) "Back on Market" variant — the framing is different from a true coming-soon.

**Luxury / private network:**
Add: `Tone shifts to private-network framing. Replace public teaser with broker-network email + select-list announcement. No public TikTok variant.`

## Companion content

YouTube Episode 15: [Coming Soon Teaser](https://youtube.com/@theprosperityagents)

## Compliance notes

Loads [`fair-housing-overlay`](../../fair-housing-overlay/). Coming-Soon-specific rules — these are the MOST violated:

- **MLS rules (state-specific):** in NC, FL, NY, CA — listings cannot claim "available" status until they're in the MLS. The skill enforces "Coming Soon" framing only.
- **Public marketing must include the pre-launch disclaimer** on any printed/flyer asset
- **No specific address in the teaser** — neighborhood-level only
- **Subscribe-for-launch CTA is REQUIRED** — gives interested buyers a compliant path

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
