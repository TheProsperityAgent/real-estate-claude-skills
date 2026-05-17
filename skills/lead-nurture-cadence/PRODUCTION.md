# PRODUCTION — lead-nurture-cadence

The "every lead gets a real plan" skill. Writes a 30-day multi-touch cadence per lead — content, channel, spacing. TCPA-aware. Pairs with `claude-boldtrail-bridge` and `speed_to_lead.py` for the full autonomous nurture stack.

## Where this fits in the stack

```
New lead arrives in BoldTrail
    │
    ▼
speed_to_lead.py (every */5 min)  ← Day 0 within 5-15 min
    │
    ▼
lead-nurture-cadence  ← drafts the 30-day plan
    │
    ▼
claude-boldtrail-bridge  ← posts touches as BT notes, honors opt-outs
    │
    ▼
agent10_crm_nurture.py (10 AM daily)  ← executes the day's due touches
    │
    ▼
nurture_supervisor.py (10-20 hourly Mon-Sat)  ← silent unless broken
```

This skill is the brain. The other crons + skills are the execution.

## The 30-day cadence

7 touches over 30 days for a typical warm/cool lead:

| Day | Channel | Content focus |
|---|---|---|
| 0 (within 5 min) | Lead's inbound channel (text or email) | Direct answer to what they asked |
| 1 (next morning) | Email | Value add — a real listing or comp |
| 3 | Email | Same town, different angle (DOM trend, recent activity) |
| 7 | Phone (call) | "Just a quick check on what you saw" — real conversation, not script |
| 14 | Email | A specific property fit for their criteria |
| 21 | Email | Financing or process angle (not a sale) |
| 30 | Email + decision point | Continue / pause / move to long-term |

Each touch is one-to-one. Each touch adds a real piece of value. Never "just checking in."

For HOT leads (talking to other agents): touches compress to Day 0, 1, 2, 4, 7, 14. Faster cadence.

For COOL leads (6+ months out): touches stretch to Day 0, 7, 21, 30, 60, 90. Slower cadence.

## The consent gate — non-negotiable

```python
if consent_on_file == "unknown":
    skill.refuse(reason="TCPA — consent status must be known before nurture")
```

The skill REFUSES to produce content for leads where consent status is "unknown". No exceptions. No bridge.

If consent is unknown, you route the lead to a re-consent path (one-time opt-in form, re-subscribe link, permission-based ad). After they opt back in, the skill resumes.

## The "no just checking in" rule

Every touch must add value. The skill rejects "just checking in" framing. The skill substitutes with a real listing, real comp, real market datum.

Why: "just checking in" is the touch agents send when they have nothing to say. After 3 of them the lead unsubscribes. With a real piece of value per touch, the lead engages.

## What the skill produces — sample touch (Day 7, warm lead)

```
Day 7 (warm, Greenville $300-400K buyer)
Channel: email
Subject: One new listing in your range — 1234 Honeysuckle

Body:
Hi Sarah,
Just landed in Greenville this morning — 4 bed, 3 bath, $385K at 1234
Honeysuckle Lane in Brook Valley. Primary on the main level and a
screened porch off the back. Built 2018. The fenced lot is 0.6 acres,
which is generous for that part of town.
Worth a look? If you want photos, here's the link: [URL].
— Al

[STOP-to-opt-out footer per TCPA]
```

Real listing. Real number. Real next step. Zero "checking in" filler.

## Variants

| Variant | Use case |
|---|---|
| Default 30-day | New buyer/seller lead, consent on file |
| Past-client win-back | Closed client, 1+ years post-close, annual cadence |
| Open-house attendee | Walked through your OH, gave you their info |
| Cold-list re-engagement | REFUSED — requires re-consent first |

## Integration with the cron stack

The skill outputs the 30-day plan. The plan gets written to `~/content-agent/state/nurture_state.json`. `agent10_crm_nurture.py` (10 AM daily) reads the plan and fires today's due touches.

When a touch fires, it routes through `claude-boldtrail-bridge` (which re-checks consent at send-time) and posts to the BT note timeline.

`nurture_supervisor.py` (10-20 hourly) verifies the day's touches actually landed.

## When to NOT run this skill

- The lead opted out. The skill refuses; route to re-consent first.
- The lead's consent status is unknown. Same refusal.
- The lead is a past client beyond your past-client cadence (3+ years out). Use a different framing.
- The lead is cold-list. Re-consent first, always.

## See also

- `examples/before-after.md` — a real $300-400K FB lead → 7-touch cadence
- `examples/failure-modes.md` — the breaks Victoria has seen
- `cron.example` — the supporting crons (10 AM nurture + */5 speed-to-lead)

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
