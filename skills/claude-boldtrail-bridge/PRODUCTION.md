# PRODUCTION — claude-boldtrail-bridge

The "moat" skill of Season 1. Connects Claude to your BoldTrail (kvCore) CRM so every other skill's output actually posts to a contact's note timeline instead of getting copy-pasted by hand.

Without this bridge, the rest of the stack is advisory. With it, the stack is autonomous.

## Where this fits in the stack

```
[any skill output — lead-nurture-cadence, mls-to-social, etc.]
        │
        ▼
claude-boldtrail-bridge
        │
        ├── READ:  GET /api/v1/contacts/{id}   ← context: consent, last touch, tags
        ├── GATE:  consent check on email_optin + text_on
        ├── GENERATE: route through the upstream skill
        └── WRITE: POST /api/v1/contacts/{id}/notes  ← logs the action
        │
        ▼
[BT note timeline updated]
[Next-touch field set]
[Tags updated if state changed]
```

Every action that touches a contact flows through this bridge. The consent gate runs at write-time, not just at generate-time. Defense in depth.

## The auth story — Episode 7's "moat"

Most agents don't know BoldTrail has an API. The dashboard hides it. To unlock:

1. BoldTrail dashboard → Settings → API tab. (If you don't see it, escalate.)
2. Generate an API key.
3. Encode as Basic Auth: `base64("{api_key}:{user_id}")`.
4. Store in `BOLDTRAIL_BASIC_AUTH` env var.
5. If your IP isn't whitelisted, BoldTrail may require an enterprise-tier plan + IP-whitelist conversation with their API team.

The negotiation IS the story of Episode 7. Most agents quit at step 1. The agents who fight through unlock the moat.

## The consent gate — three rules

```python
# Rule 1: gave_consent is HISTORICAL — stays True after unsub. NEVER trust for send decisions.
# Rule 2: email_optin is CURRENT — trust this for email touch decisions.
# Rule 3: text_on is CURRENT — trust this for SMS touch decisions.

def consent_gate(contact, channel):
    if channel == "email":
        return contact.get("email_optin") is True
    if channel == "text":
        return contact.get("text_on") is True
    return False
```

If consent gate fails, the bridge:
- Outputs NOTHING to the contact (no email, no text).
- DOES write a note titled `Skipped — consent withdrawn YYYY-MM-DD` so the audit trail exists.

The skip-note is critical. If a TCPA dispute hits, the note timeline shows you didn't touch them AND you logged the skip decision.

## What the bridge writes — note title formats

```
NURTURE — 2026-05-17 — Day 7 follow-up
NURTURE — 2026-05-17 — Day 0 welcome
SPEED-TO-LEAD — skipped (no current opt-in)
PHASE — under contract on 1234 Main St
CLOSED — 2026-04-22 — buyer side
UNSUBSCRIBE — STOP detected via reply parsing
```

Every Claude-driven action gets a note. The note title indicates what kind of action; the body has the actual content. The timeline becomes your audit trail.

## The "PHONE CALL stays yours" rule

The bridge writes notes and updates fields. It does NOT initiate phone calls. Phone is the agent's job.

Locked rule per Episode 3: "The relationship stays human. The phone call stays yours. The send button stays yours. The strategy stays yours."

The bridge handles the digital paper-trail. You handle the human moments.

## Common workflows the bridge powers

**Workflow 1 — Daily call list (cron at 7 AM):**
```python
contacts = GET /api/v1/contacts?next_touch_due_before=today&consent=opted-in&limit=20
email_call_list(contacts)
```

**Workflow 2 — Speed-to-lead Day 0 (cron every 5 min):**
```python
fresh = GET /api/v1/contacts?updated_since=10min_ago
for c in fresh:
    if consent_gate(c, channel=c.inbound_channel):
        touch = run_skill("lead-nurture-cadence", inputs=...)
        POST /api/v1/contacts/{c.id}/notes  # logged + sent via BT auto-respond rule
```

**Workflow 3 — Honor unsubscribe (every email reply parser):**
```python
if reply.matches(r"\b(STOP|UNSUBSCRIBE|REMOVE ME)\b"):
    PUT /api/v1/contacts/{c.id}  # email_optin=False, text_on=False, tag "optout-honored"
    POST /api/v1/contacts/{c.id}/notes  # "Unsubscribe detected YYYY-MM-DD"
```

**Workflow 4 — Phase change auto-tag (cron at 6:50 AM):**
```python
under_contracts = GET /api/v1/contacts?tag=active-buyer
for c in under_contracts:
    if c.mls_status_now == "UC":
        PUT /api/v1/contacts/{c.id}  # tag "under-contract", next_touch_due in 14 days
        POST /api/v1/contacts/{c.id}/notes  # "PHASE — under contract on {address}"
```

## When the bridge refuses

- Re-engagement of unsubscribed contacts. Hard refuse. Route to re-consent.
- Touches to non-opted-in contacts. Hard refuse + audit-trail skip note.
- Demographic-based routing decisions ("send this to all young buyers"). FH violation, hard refuse.
- Editing the `gave_consent` field. The skill never modifies it — only `email_optin` / `text_on` per BT field semantics.

## Where this connects to the cron classes

- `build-your-cron-speed-to-lead` — every-5-min fresh-lead catcher. Calls the bridge for every fresh lead's consent gate + note write.
- `build-your-cron-compliance-supervisor` — 11 AM run. Verifies every BT note posted yesterday passed compliance.
- The daily 10 AM nurture, the 7 AM call list, the 6:50 AM phase scanner — all bridge-powered.

## See also

- `examples/before-after.md` — Day 0 fresh-lead flow walkthrough
- `examples/failure-modes.md` — the gate breaks and the fixes
- `cron.example` — the four bridge-related cron lines

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
