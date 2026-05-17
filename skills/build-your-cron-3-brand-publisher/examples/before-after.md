# Before / After — 3-brand publisher

## Before — the three-brand juggle

Most agents who run 2+ brands look like this:

```
Monday — LIG blog written, scheduled, social fanned out. Feels good.
Tuesday — Prosperity is overdue. Spent 90 min writing it. Stressed.
Wednesday — Skipped both brands. Showings.
Thursday — Tried to write all three. Got LIG done. Prosperity was 60% done.
           Victoria's author blog hasn't shipped in 2 weeks.
Friday — One brand published, two went dark for the week.
Sat-Sun — Backlog compounds.
```

Net result after 3 months: one strong brand, two atrophied brands. The two atrophied brands lose SEO rank because Google reads them as dormant.

## After — same realtor, this cron running

```
Monday — open the daily brief. 3 brands published, 3 socials scheduled, 0 alerts. Read for 90 seconds, close.
Tuesday — same.
Wednesday — same. Spent the saved 4 hours on listing appointments.
Thursday — same. One alert from the brand-separation check ("LIG post mentions Prosperity URL"). Fix in 10 minutes.
Friday — same. Three brands compounding SEO together.
```

Net result after 3 months: three brands shipping daily, three brand-aware dedup ledgers preventing repeat-idea drift, voice gems holding each brand's voice steady.

## What broke before the cron — concrete failure list

1. **LIG drift to recruiting framing.** Victoria wrote a "why I love Greenville" post and ended with "if you're an agent thinking about a move, our team at eXp..." — bad. LIG audience is buyers/sellers, not agents. The router now blocks any LIG post that contains recruiting patterns.

2. **Prosperity going neutral on competitors.** Pre-cron Prosperity posts sometimes said "Compass has a great agent-experience model." Neutral comparison = SEO gift to competitor. The cron's audience-rule forces the eXp pivot.

3. **Victoria author blog dark for 30 days.** Pre-cron, the author brand was always the last one written and the first one skipped. The cron treats all three brands as peers — each gets its own dedicated router run.

4. **Same idea on two brands.** Pre-cron, the "spring market activity" angle ran on LIG Tuesday and Prosperity Wednesday. Reads as one author writing two brands (because she was). Now the per-brand dedup ledger blocks topic re-use across brands in a 7-day window.

5. **Rank Math meta empty.** Pre-cron, Victoria pushed posts with Yoast meta keys (her old workflow). Rank Math silently dropped them. SEO meta blank for ~3 weeks before someone noticed. The cron's publisher verifies meta saved by fetching live HTML.

## What the cron does NOT do for you

- It does not pick which 3 brands you should run. That's a business decision.
- It does not write the voice gems. You write each gem once, based on your audience, your voice, your locked pivots. The cron then enforces them daily.
- It does not coach a struggling brand. If a brand has no audience, the cron will ship 365 posts to nobody. The cron amplifies clarity; it does not create it.

## What you get back

| Manual cost (3 brands) | Cron cost (3 brands) |
|---|---|
| 4-5 hours/day juggling | 5 min/day daily-brief read |
| Brand drift on the brand you skipped | Zero drift (gems lock voice) |
| SEO atrophy on neglected brands | All three brands shipping daily |
| Two posts on the same idea Monday and Tuesday | Per-brand dedup ledger blocks |
| Yoast meta keys silently dropped on Rank Math sites | Live-HTML verification catches the drop |

## When this cron makes sense vs not

**Makes sense:** 2+ brands, each with a distinct audience + voice. You want all of them shipping daily.

**Does NOT make sense:** one brand. You're over-engineering. Use `build-your-cron-daily-mls-to-metricool` directly.

**Makes sense at the high end:** 4-5 brands. The architecture scales linearly — add a router, add a voice gem, add a dedup ledger. No code rewrite.

**Does NOT scale:** 10+ brands. At that point you're an agency and you need a different model (per-client config, multi-tenant publisher). The Pinder Team's three-brand stack is the upper end of "two-realtor solo".
