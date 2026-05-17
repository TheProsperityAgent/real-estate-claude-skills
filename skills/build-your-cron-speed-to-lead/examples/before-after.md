# Before / After — speed-to-lead

The single most expensive minute in real estate is minute 6. After minute 5, conversion probability collapses.

## Before — 10 AM daily nurture only

```
Sunday 6:47 PM — Zillow inquiry on a Greenville 4-bed listing.
                 Buyer fills the form. BoldTrail emails Victoria. Victoria is at dinner.
Sunday 11:30 PM — Victoria sees the email. Replies "I'll get to it Monday morning."
Monday 8:00 AM — Buyer wakes up. Checks email. Nothing.
                 Submits same inquiry on a different agent's IDX site.
Monday 10:00 AM — Victoria's daily nurture cron fires. Touches the lead at last.
                  Buyer already exchanged 6 messages with another agent.
                  Showing on Tuesday with the other agent.
Tuesday — Lost.
```

Lag: 39 hours. Conversion probability at minute 39 × 60 = 5-10% of what it was at minute 5.

## After — speed-to-lead cron at */5 min

```
Sunday 6:47 PM — Zillow inquiry. BoldTrail webhook fires. Contact lands in BT.
Sunday 6:50 PM — speed_to_lead.py runs (next */5 tick). Pulls fresh contacts.
                 Finds the 6:47 lead. Consent gate: email_optin=True. Pass.
                 Generates welcome touch via lead-nurture-cadence.
                 Posts as a BT note. (Note + email fires from BT's automation
                 since that contact is in the auto-respond rule.)
Sunday 6:51 PM — Buyer's phone buzzes with a Greenville-listing-themed welcome.
                 Replies "Thanks! Yes I'd love a tour."
Sunday 7:15 PM — Victoria sees the reply at dinner. Schedules a Tuesday showing.
                 (She didn't write the welcome touch. The cron did.)
Tuesday — Showing happens. With Victoria. Not with another agent.
```

Lag: 4 minutes. Conversion probability at minute 4 = peak.

## The 391% number explained

From the original Lead Response Management Study (MIT + InsideSales.com, 2011, replicated 2018 + 2023):

- The likelihood of a lead being qualified is 21× higher if you respond in 5 minutes vs 30 minutes.
- The likelihood of converting (signing) is 391% higher if you respond in 1 minute vs 1 hour.

The 5-minute window is where the magic happens. After minute 30, you're competing against every other agent the lead already messaged.

This cron's job is to never miss the 5-minute window — including 11 PM Saturday and 6 AM Sunday and Christmas Eve.

## Where the cron earns its keep — the weekend lead

The biggest single-day ROI on this cron is weekend leads. Most agents check email Monday morning. The cron doesn't take weekends off. A Saturday afternoon Zillow inquiry hits a welcome touch in 5 minutes. By Monday morning, you're already on a showing schedule.

Victoria's 90-day audit after installing the cron showed:

- 47% of fresh leads arrived outside 9-5 weekday hours.
- Pre-cron: those leads got the same 10 AM Monday nurture touch.
- Post-cron: those leads got Day 0 within 15 min. The weekend leads converted at ~3× the rate of the daytime leads (because they were genuinely active and motivated when they submitted).

## What you give up

You give up the chance to personally hand-craft every Day 0 touch. The cron uses `lead-nurture-cadence` to draft. The draft is 4-6 sentences, references the lead's stated interest, and includes a real listing or real comp.

Some agents resist this — they want every first touch to feel handwritten. Fine. Then your first touch lands at 10 AM tomorrow instead of 6:50 PM tonight, and you lose the 5-minute window for the principle. The cron's draft is better than your no-draft.

You can still personally write the Day 1 follow-up. The cron handles Day 0. The handwritten touch comes 24 hours later.

## What you DON'T give up

The send button. Per Victoria's locked rule: "the agent still owns the relationship. The phone call stays yours. The send button stays yours. The strategy stays yours."

The cron writes the welcome touch + posts it as a BT note. Whether BoldTrail's automation actually sends it depends on YOUR auto-respond rules in BT. You can configure BT to:

- Auto-send the touch the cron drafted (full automation).
- Hold the touch for your review and one-tap-send (semi-auto).
- Notify you with the draft and let you customize before send (manual review).

Victoria runs semi-auto for new audiences and full-auto for warm-list re-engagement. You pick the level you're comfortable with.

## What the cron does NOT do

- It does not cold-blast non-opted-in leads. Consent gate. Hard stop.
- It does not bypass an unsubscribe by reading historical `gave_consent=True`. That field is historical-only.
- It does not "re-engage" cold lists. Those need re-consent first — a one-time opt-in form, not a cron.
- It does not violate the every-5-minute-API-call principle by hammering BT. The cron pulls last-10-min only, never all-contacts.

## The audit trail

Every single fresh lead gets a paper trail:

```
Lead created at 6:47:23 PM
  └── 6:50:08 PM — speed_to_lead.py picked up
       ├── consent_gate → PASS (email_optin=True)
       ├── nurture_state → not yet enrolled
       ├── lead-nurture-cadence → drafted welcome
       ├── fair-housing-overlay → cleaned
       └── POST /api/v1/contacts/12345/notes → note created
              Title: "NURTURE — 2026-05-17 — Day 0 welcome"
              Body: [the drafted touch]
```

For non-opted-in leads:

```
Lead created at 6:47:23 PM
  └── 6:50:08 PM — speed_to_lead.py picked up
       ├── consent_gate → SKIP (no current opt-in)
       └── POST /api/v1/contacts/12345/notes → skip-note created
              Title: "SPEED-TO-LEAD — skipped"
              Body: "Speed-to-lead skip 2026-05-17 — No current opt-in (email_optin + text_on both False)"
```

That's it. That's the cron.
