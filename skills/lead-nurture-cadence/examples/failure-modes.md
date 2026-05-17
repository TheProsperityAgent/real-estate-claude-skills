# Failure modes — lead-nurture-cadence

## 1. Running on a lead with unknown consent status

**Symptom:** Skill refuses with `TCPA — consent status must be known before nurture`.

**Why:** Working as designed. The skill never produces content for a lead where `consent_on_file=unknown`.

**Fix:** Route the lead through a re-consent path first. One-time opt-in form, re-subscribe link, permission-based ad. After they explicitly opt back in, the skill resumes.

This is the gate that protects you from TCPA suits. Don't try to bypass it.

## 2. Touches drift to "just checking in" framing

**Symptom:** Day 14 or Day 21 touch says "just wanted to see if you're still looking" with no real value attached.

**Why:** Your prompt didn't pass a real listing or comp for that touch.

**Fix:** Every touch needs real input data:
- Day 1: 3 listings in their range.
- Day 3: a market datum (recent sold count, DOM trend).
- Day 14: a NEW listing that fits their criteria.
- Day 21: a financing or process angle (rate move, lender update, market shift).

If you don't have real data for a touch, SKIP that touch. Don't fill with "checking in."

## 3. Cadence generates mass-blast SMS

**Symptom:** Output includes SMS templates that say "we're following up with all our recent leads" or similar mass-blast framing.

**Why:** Prompt drift. The skill should refuse this by design.

**Fix:** Verify the prompt template doesn't include "mass" or "blast" language. The skill's TCPA rule:
> NEVER produces auto-dialer-compatible mass templates

If output drifts mass-blast, add to prompt: `One-to-one only. NEVER write a template that could be sent to multiple leads. Every touch is for THIS lead specifically.`

## 4. SMS touches missing the STOP-to-opt-out footer

**Symptom:** Output text touches don't include `Reply STOP to opt out.`

**Why:** The skill's TCPA rule includes the STOP footer by default; if missing, your prompt may have suppressed it.

**Fix:** Every SMS touch in TCPA jurisdictions (which is everywhere in the US) must include opt-out instructions. Re-emphasize in prompt:
> Every SMS touch must end with "Reply STOP to opt out." — non-negotiable.

## 5. Cadence schedules Day 7 phone call at 11 PM

**Symptom:** Cadence says "Day 7: phone call at 11 PM" or schedules a touch outside reasonable hours.

**Why:** Time-zone unaware. Or you didn't specify the lead's local time.

**Fix:**
- All phone touches default to 9 AM - 7 PM lead-local time.
- All SMS touches default to 8 AM - 9 PM lead-local time (TCPA-mandated window).
- Pass the lead's timezone explicitly: `lead_timezone: America/New_York`.

## 6. Past-client cadence runs the warm-lead cadence

**Symptom:** A past client (closed 18 months ago) receives a "Day 1: three listings in your range" email — but they're not actively looking.

**Why:** Variant not specified. Skill defaulted to standard buyer cadence.

**Fix:** Use the past-client variant explicitly:
> Build a 4-touch annual cadence for a past client who closed [N] months ago. Touches: closing anniversary card, neighborhood-value-update at year 1, refer-a-friend ask at year 2, full-CMA-offer at year 3.

Past-client cadence is annual, not 30-day. Different rhythm.

## 7. Cadence assumes a listing the lead never asked about

**Symptom:** Day 1 email recommends 3 luxury $1.5M listings. The lead is shopping $300K starter homes.

**Why:** Listings passed in prompt didn't match the lead's price band.

**Fix:** Filter listings before passing to prompt. The skill builds the cadence around the listings you give it — if you give it wrong-band listings, the cadence is wrong-band.

```python
matching = [l for l in mls_pull if l["price"] >= 280000 and l["price"] <= 420000]
```

## 8. Cadence misses the inbound channel preference

**Symptom:** Lead submitted via text-based FB form. Skill returns cadence using email Day 0.

**Why:** Inbound channel not passed in prompt.

**Fix:** The Day 0 prompt block specifies: `Channel: [text or email — pick based on what they used]`. Always pass the inbound channel:
```
lead_inbound_channel: text (FB lead ad mobile submission)
```

Match the channel they used. Switching from text inbound to email outbound feels off to the lead.

## 9. Day 30 touch doesn't include the "decision point"

**Symptom:** Day 30 sends another listing email, not a decision point.

**Why:** Prompt didn't specify the Day 30 framing.

**Fix:** Day 30 is the decision point. The cadence asks the lead to choose:
1. Stay in the rotation (continue regular touches).
2. Pause (back off to monthly).
3. Move to long-term (quarterly market-update only).

This explicit ask is what prevents lead-attrition without you realizing. Re-emphasize in the prompt: `Day 30 MUST be a decision point — never just another listing email.`

## 10. The skill cadence drifts to push-sales tone

**Symptom:** Touches read as "BUY NOW, time is running out, this is the perfect home for you" — pressure tactics.

**Why:** Generic AI sales-copy training drift.

**Fix:** Pinder Team voice is calm, specific, optimistic, never pressure-y. Add to prompt:
> Tone: calm. Specific. Helpful. Never pressure-y. The lead has 30 days
> of touches to decide; we don't need today's email to close. Trust the
> cadence.

Sustained value beats sales pressure across 30 days. The skill's job is to keep showing up usefully.
