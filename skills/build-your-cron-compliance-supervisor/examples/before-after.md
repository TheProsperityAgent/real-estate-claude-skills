# Before / After — compliance supervisor

## The day Victoria almost shipped "great schools"

Late one Tuesday, around 7 PM, the overnight Gemini Batch API drafted tomorrow's content. One of the drafted listing descriptions for a 4-bed Greenville home ended with:

> Located in one of Greenville's great school districts with a quick commute to ECU Health.

The upstream `listing-description` skill loaded `fair-housing-overlay`, which SHOULD have caught "great school districts". It didn't, because the overlay's pattern was `\bgreat\s+schools\b` (plural without the "s") and the draft used "school districts" (different word). Edge case. Slipped through.

11 AM Wednesday — `compliance_supervisor.py` fires. The `check_criminal_leaks` function has a broader pattern (`\bgreat\s+schools?\s+district\b` AND `\bschool\s+district\b`). The draft hits the second pattern. BLOCK. Moved to `/content/blocked/2026-05-17/4823.json`. Daily brief Wednesday morning flags it.

Victoria opens the file at 6:30 AM. Reads the closing line. Rewrites it:

> Located in Greenville with a 12-minute commute to ECU Health.

Drops back in `queue/tomorrow/`. Publishes Thursday morning. No FHA violation in the MLS feed.

Net cost: 90 seconds of Victoria's morning. Net benefit: no FHA exposure, no MLS-feed downrank.

## The 47-phrase blocklist — sampled

Here's a window into what the supervisor catches that the foundation overlay didn't:

| Pattern blocked | Why it slipped past overlay |
|---|---|
| `\bgreat\s+schools?\s+district\b` | "School districts" instead of just "schools" |
| `\bsafe\s+(?:neighborhood|area|community)\b` | The 3 noun variants weren't all in the overlay |
| `\bquiet\s+(?:area|neighborhood|street(?!\s+with))\b` | Lookahead exempts the legitimate "low-traffic street" replacement |
| `\bfamily-friendly\b` | Hyphenated variant of "family friendly" |
| `\bmust\s+sell\b` | NAR Code 11 violation slipped past the MLS scarcity check |
| `\bowner\s+motivated\b` | NAR Code 11 — public seller-motivation disclosure |
| `\bonce-in-a-lifetime\b` | MLS scarcity in long-form variant |
| `\bback\s+to\s+school\b` | Q3 quarterly postcard FHA familial-status trap |
| `\bshow\s+stopper\b` | MLS data-quality cliché |

Patterns are tuned with negative lookaheads to avoid false positives on legitimate uses.

## The opt-out catch

Lead Mary Smith opted out of email at 10:30 AM Tuesday. The CRM opt-out cache (refreshed every 4 hours) updated at 12:00 PM Tuesday. The overnight nurture run on Tuesday at 7 PM tried to draft a touch for her using the stale cached snapshot... and now the upstream consent-gate has fresh data so it would block correctly.

But what about a touch drafted BEFORE her opt-out, queued for publish AFTER her opt-out?

11 AM Wednesday — `check_optout` fires. Pulls the current opt-out list (refreshed 1 hour ago). Mary Smith is on the list. The drafted touch in `queue/tomorrow/` is BLOCKED.

Cost without supervisor: a TCPA suit at $500-$1,500.
Cost with supervisor: zero.

## The dedup catch

Friday morning Victoria's daily brief says:

> 1 item held by compliance — review at /content/blocked/2026-05-17/
> 5012: check_dedup_30day — exact content already published in last 30 days

She opens 5012.json. It's a listing description for a 3-bed Winterville home. She looks at the 30-day history — and yes, 18 days ago she published a near-identical description. The generator had hit the same skill prompt with the same property data and produced identical body text.

Cost: 60 seconds of edit + republish.
Benefit: no follower confusion, no "are they reposting old content?" question from sellers.

## The closed-listing catch

Wednesday at 10:45 AM, a buyer closed on 1234 Honeysuckle Lane (the listing Victoria's been promoting for 6 weeks). MLS status flipped to CLD at 11:02 AM.

At 11:00 AM (12 minutes before the MLS flip), the compliance supervisor fired. Tomorrow's "Open House Sunday" post for Honeysuckle was queued.

If the supervisor had run at 10:55 AM, it would have passed the post (MLS still showed ACT). But the supervisor runs at 11:00 AM EXACTLY, and `check_closed_or_uc` made the API call at 11:00:14 — by then the MLS had already flipped to CLD.

Post BLOCKED. Victoria didn't promote an open house on a closed listing.

(This is a race-condition example. Most days the supervisor catches the phase change cleanly; on rare days it depends on which side of 11 AM the flip happened. The acceptable failure mode is shipping a post about a closed listing 24 hours late — better than not shipping at all.)

## What the supervisor does NOT catch

- A typo. Spelling is your job.
- A factually-wrong number (you typed $385K when the listing is $358K). Your eyes are the check.
- A tonal misstep within the bounds of compliance. The supervisor confirms rules; you confirm voice.
- A poorly-chosen photo. The image-rules check verifies dimensions + EHO presence; it doesn't verify aesthetic.

Compliance is a hard floor. Excellence is your job above the floor.

## The accumulating savings

Over 365 days, Victoria's supervisor blocked:

- 42 criminal-leak phrases (FHA + MLS).
- 8 closed-or-UC posts that would have shipped late.
- 3 opt-out violations (TCPA $500-$1,500 each).
- 17 30-day dedup hits.
- 5 daily-cap saves (Facebook downranking prevention).

Total cost of running the supervisor: ~$50/year in compute + log storage.
Total potential exposure prevented: ~$5,000-$15,000 in TCPA suits + 1-2 FHA defense events.

That's the math. Six-figure license + reputation, protected by a $50/year cron.
