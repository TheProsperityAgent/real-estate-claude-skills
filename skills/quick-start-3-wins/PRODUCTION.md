# PRODUCTION — quick-start-3-wins

The "I just installed Claude, what do I do today" skill. This file is the production-grade walk-through for shipping the 3 wins on a real listing today.

## Where this skill fits

It's the first skill you load. Pair with `fair-housing-overlay` from minute one — every output is FH-clean from the first character because the overlay loads alongside.

This skill is on-dispatch only — there is no cron. You run it manually:
- New listing came in this morning. Trigger prompt 1.
- Buyer made an offer. Trigger prompt 2.
- It's 8 AM and you want 3 social posts. Trigger prompt 3.

When you outgrow on-dispatch and want the social posts to fan out automatically every morning, graduate to `build-your-cron-daily-mls-to-metricool`.

## The actual workflow on a real listing

Victoria's morning, real listing in Greenville:

```
8:45 AM — New listing came in. 1234 Honeysuckle Lane, $385K, 4 bed.
8:46 AM — Open Claude Code. Load fair-housing-overlay + quick-start-3-wins.
8:47 AM — Paste Prompt 1 (listing description). Fill in property data.
8:48 AM — Claude returns 145-word description. No "great schools", no
          "must-see", no "won't last". Copy → paste into MLS field.
8:49 AM — Same chat — paste Prompt 3 (3 social posts).
          Claude returns IG (≤2200 chars), FB (≤700), TikTok (≤300) — all
          with the listing's real numbers.
8:55 AM — Open Metricool. Paste IG → schedule for 9 AM.
          Paste FB → schedule for 11 AM. Paste TikTok → 1 PM.
9:00 AM — Done. 15 minutes from "new listing landed" to "scheduled across
          3 platforms, MLS field filled."
```

The from-scratch version of that morning is 45-60 minutes. The skill earns its install cost on the first listing.

## What you need before you load this skill

1. Claude Code installed — `claude.ai/download`.
2. `fair-housing-overlay` skill cloned alongside.
3. Your brokerage's license number + brokerage line (e.g., "Al Pinder, NC #334543 · eXp Realty LLC").
4. Your MLS export source for the daily social-posts prompt.

## How this connects to the rest of the stack

You start here. Then:

- For a 145-word description on a single listing → keep using `listing-description` (skill 3).
- For a counter-offer reply → graduate to `offer-counter` (skill 4).
- For a full 30-day lead nurture cadence on a new lead → graduate to `lead-nurture-cadence` (skill 5).
- For a 4-section CMA narrative → graduate to `cma-narrative` (skill 6).
- For BoldTrail integration so Claude actually posts notes to contacts → graduate to `claude-boldtrail-bridge` (skill 7).
- For 5-platform social posts (adds X + LinkedIn beyond the 3 in this skill) → graduate to `mls-to-social` (skill 8).
- For a full listing presentation → graduate to `listing-presentation-builder` (skill 9).
- For Canva-driven design briefs across the listing lifecycle → graduate to `canva-real-estate-lifecycle` (Season 2).
- For automating the daily 3-platform social into a cron → graduate to `build-your-cron-daily-mls-to-metricool` (Season 3).

## A real before / after

See `examples/before-after.md` in this folder.

## When something doesn't work

See `examples/failure-modes.md`. The big ones:
- Not loading `fair-housing-overlay` alongside — the overlay is what catches "great schools" and "must-see" before they ship.
- Word count drift — Prompt 1's 145-word cap matches the optimal MLS field length. Don't override.
- Pasting MLS data with mixed-case ALL-CAPS headers — Claude reads it fine but the output sometimes mirrors the case. Send clean.

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
