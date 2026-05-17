# Before / After — daily MLS to Metricool

The same realtor's morning, before and after this cron.

## Before the cron — Victoria's actual 8 AM workflow circa 2024

```
8:00 AM — Sit down with coffee. MLS export email is sitting in the inbox.
8:05 AM — Open the CSV. Skim today's new listings under $400K.
8:15 AM — Open WordPress LIG admin. Start drafting today's blog from scratch.
9:10 AM — Blog draft saved. Open Metricool. Start IG caption from scratch.
9:30 AM — IG caption done. Pivot to FB. Realize the framing is wrong for FB.
          Rewrite from scratch.
9:50 AM — FB done. TikTok. Different cap (300 chars). Rewrite again.
10:05 AM — TikTok done. LinkedIn. Different audience (other agents). Rewrite.
10:30 AM — LinkedIn done. Schedule all 4 in Metricool. Two of them land in the
           same 10:30 AM slot (no dedup). Manually shuffle.
10:45 AM — Realize the Canva image is the wrong aspect ratio for Pinterest.
           Re-crop. Re-upload.
11:00 AM — Done. 3 hours in. Family is asking when breakfast is.
```

Three hours from MLS pull to scheduled. Captions rewritten 4× because no platform-specific drafting up front. One slot collision. One image re-crop. No daily brief — she just hopes it landed.

## After the cron — same realtor, 2026

```
5:45 AM — Sit down with coffee. Daily brief email has hit the inbox.
5:50 AM — Read the brief: "Yesterday's MLS pulled 12 new listings. 4 posts
          scheduled for today: 9 AM IG, 11 AM FB, 1 PM Pinterest, 3 PM
          LinkedIn. All FH-clean. metricool_dedup confirmed slots spaced."
5:55 AM — Open Metricool to spot-check the 9 AM IG caption. Reads clean.
          Close Metricool.
6:00 AM — Family breakfast prep.
6:15 AM — run_daily.sh fires in the background. Tomorrow's drafts get queued.
          She doesn't open the laptop.
7:00 AM — Breakfast.
9:00 AM — IG post fires (scheduled). She is on a showing in Winterville.
```

Six minutes of work. The cron does everything else. Family time is family time.

## What changed structurally

1. **Day-ahead, not same-day.** Drafts run at 6:15 AM TODAY for TOMORROW's posts. Every post gets 24+ hours of marination instead of 90 minutes of stressed-out edit-pass.
2. **One generator, 4 platform versions in parallel.** `mls-to-social` skill drafts all 5 platform captions (IG / FB / TikTok / X / LinkedIn) in one LLM call. No 4× rewrite tax.
3. **Dedup before scheduling.** `metricool_dedup.py` picks next-open slots ≥90 min apart. Slots never bunch.
4. **Image sweeper safety net.** Twice-daily sweeper catches dimension mismatches. She never re-crops Canva exports.
5. **Daily brief email.** Replaces "open Metricool 4× a day to check." If the email looks good, the cron worked.

## The math

Manual daily cost: ~3 hours × 365 days = ~1,095 hours/year.
Cron daily cost: ~6 minutes (read the brief) × 365 days = ~36 hours/year.
Hours back: ~1,059 hours/year. That's ~6 months of 40-hour weeks.

That gap is why Victoria and Al are ICON agents at eXp instead of two solo realtors splitting time between content and contracts.

## What you ship in the first week

Week 1 of running the cron:
- Day 1-2: cron fires, you check Metricool every morning to verify scheduling worked.
- Day 3-4: you stop opening Metricool. The daily brief tells you everything you need.
- Day 5: an MLS pull throws an unexpected column. The supervisor catches it, you fix the parser, life goes on.
- Day 7: you have shipped 28 social posts across 4 platforms. You worked maybe 30 minutes of "content" all week.

Week 2: you start using the saved hours to actually do real-estate work. Showings, listing appointments, sphere calls.

That is the entire point of the cron-class pattern. The cron is a coworker who doesn't bill, doesn't take sick days, and doesn't violate fair housing.
