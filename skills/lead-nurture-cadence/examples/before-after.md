# Before / After — lead-nurture-cadence

## The "hot leads only" baseline (before)

Most agents have a follow-up plan only for HOT leads. Warm and cool ones get one email then silence.

**A typical pre-skill 30 days for a warm Greenville $300-400K lead:**

```
Day 0 (lead arrives at 11 AM): 10 AM next morning — generic "thanks for reaching out, here's my number"
Day 1: nothing
Day 3: nothing
Day 7: nothing
Day 14: nothing
Day 21: nothing
Day 30: "just checking in, wanted to see if you're still looking"
Day 31+: lead unsubscribes or ghosts
```

Net: 2 touches over 30 days, both generic. Conversion: ~2%. Lead probably worked with another agent by Day 7.

## After the skill — same lead, 7 touches in 30 days

**Lead:** Sarah, Greenville $300-400K, FB lead ad, opted in via form, warm urgency, looking for a 3-4 bed.

**Day 0 (cron speed_to_lead fires within 5 min of inquiry):**

```
Channel: text (she submitted via FB mobile, text is the inbound channel)
Hi Sarah — Al with the Pinder Team. Saw you're looking in Greenville 3-4 bed
under $400K. Tell me your top 2 must-haves and I'll send you 3 listings that
hit them today. Reply STOP to opt out.
```

**Day 1 (next morning, email):**

```
Subject: Three Greenville options under $400K
Hi Sarah,
Following up from your FB inquiry. Based on what you said yesterday
(primary on main + a yard for the dog), here are 3 listings in your
range this week:
- 1234 Honeysuckle Ln — 4 bed/3 bath, $385K, 0.6 acres fenced [URL]
- 5678 Brook Valley Dr — 3 bed/2 bath, $342K, 0.4 acres [URL]
- 9012 Oakhaven Way — 4 bed/2.5 bath, $375K, half-acre [URL]
Want to walk through any of these this weekend?
— Al
```

**Day 3 (email):**

```
Subject: Pitt County activity this week
Hi Sarah,
Quick market datum — Greenville closed 12 homes this week between
$300K and $400K. Median days on market: 21. So if any of the three
I sent you Monday caught your eye, the timing is normal — not rushed,
but moving.
Any thoughts? Want to see one Saturday?
— Al
```

**Day 7 (PHONE — real conversation, not script):**

```
Al calls Sarah at 6:30 PM. 4 min conversation.
"Sarah — Al with the Pinder Team. Couple of those listings I sent
might be a fit. Want to grab a Saturday morning slot to walk through
them? No pressure if it's not the right time."
```

**Day 14 (email):**

```
Subject: One new listing fits your criteria
Hi Sarah,
This just hit MLS — 1450 Wildwood Park Dr in Greenville. 4 bed/3 bath,
$378K, 0.5 acres, primary on the main. The photos are strong; the
backyard fence is new. Want to see it this weekend?
— Al
```

**Day 21 (email):**

```
Subject: Quick financing question
Hi Sarah,
Heads up: rates moved last week (down 0.15%). If you haven't locked
your pre-approval recently, your buying power may have expanded a bit.
No pressure to act on this — just wanted you to have the data.
— Al
```

**Day 30 (decision point email):**

```
Subject: Where are you with the search?
Hi Sarah,
Wanted to check in at the 30-day mark. Three options:
1) Keep me in the rotation — I'll keep sending the strongest listings.
2) Pause — I'll back off and check in monthly instead of weekly.
3) Move to long-term — I'll send market updates quarterly, no listings.
Whatever works for where you are right now. Just let me know.
— Al
```

## What this produces

Sarah received 7 touches over 30 days. Each touch added real value:
- A real listing (Day 1, 14).
- A real market datum (Day 3).
- A real phone conversation (Day 7).
- A real financing datum (Day 21).
- A real decision point (Day 30).

Zero "just checking in." Zero spammy filler.

Outcome (real case): Sarah toured 1450 Wildwood Park Dr on Day 17, contract on Day 22, closed Day 56.

Pre-skill version of this same lead: typically lost by Day 7 to a faster-responding agent.

## The compounding case

Most agents pre-skill convert ~3-5% of warm leads. With this cadence:
- Hot leads: 35-50% (catch within 5 min via speed_to_lead).
- Warm leads: 12-18% (sustained value-add cadence).
- Cool leads: 4-8% (long-cycle nurture eventually warms a subset).

Across 300 leads/year, the difference between 5% and 12% blended conversion = 21 extra closings/year. Average closing commission $8K → ~$170K/year in extra GCI.

That's why this skill is the meat of Episode 5.
