# Failure modes — claude-boldtrail-bridge

## 1. BoldTrail API tab not visible in your dashboard

**Symptom:** You go to Settings expecting an API tab. There's nothing.

**Why:** BoldTrail's API is documented for select partners. Lower-tier plans hide it. Some agents need to escalate.

**Fix:** Email BoldTrail support and request API access. Reference the "select partner" language from their docs. Be ready to upgrade to enterprise tier and possibly arrange an IP whitelist. This is the negotiation that IS Episode 7 — most agents quit here. Don't.

## 2. Auth returns 401 even with valid credentials

**Symptom:** `curl -u "key:user_id" $BT_BASE/contacts` returns `{"error":"Unauthorized"}`.

**Why:** Most common cause: you base64-encoded the colon-joined string but forgot to set the `Authorization: Basic <base64>` header format.

**Fix:**
```bash
ENCODED=$(printf "%s" "$API_KEY:$USER_ID" | base64)
curl -H "Authorization: Basic $ENCODED" $BT_BASE/contacts
```

Or in Python, requests handles it:
```python
import requests
r = requests.get(f"{BT_BASE}/contacts", auth=(API_KEY, USER_ID))
```

Test the auth in isolation BEFORE wiring the bridge into any cron.

## 3. Consent gate reads `gave_consent` instead of `email_optin`

**Symptom:** A touch goes to a contact who unsubscribed last week. They complain.

**Why:** Bridge code is checking `gave_consent` (historical) instead of `email_optin` (current).

**Fix:**
```python
# WRONG — historical, never trust for send decisions
if contact.get("gave_consent"):
    send(contact)

# RIGHT — current state, trust for send decisions
if contact.get("email_optin") is True:
    send(contact)
if contact.get("text_on") is True:
    text(contact)
```

`gave_consent=True` means they EVER opted in. It does NOT mean they currently want communication. After unsubscribe, `gave_consent` stays True but `email_optin` flips to False.

## 4. Bridge skips a contact but doesn't log the skip

**Symptom:** Consent gate fails. Bridge does nothing. No BT note posted. Audit trail incomplete.

**Why:** Skip logic returned early without posting the audit note.

**Fix:** Every skip MUST log a note:
```python
if not consent_gate(contact, channel):
    POST /api/v1/contacts/{id}/notes  # title: "Skipped — consent withdrawn YYYY-MM-DD"
    return  # NOW return
```

The skip note is non-optional. It's your TCPA defense.

## 5. Tag updates leave the contact in a stale state

**Symptom:** Contact got the Day 7 touch. Tag still says "in-nurture-day-0".

**Why:** Bridge wrote the touch but forgot to update the tag.

**Fix:** Tag update is part of every write workflow:
```python
def post_nurture_touch(contact, day):
    POST /notes  # the touch
    PUT /contacts/{id}  # tag "in-nurture-day-{day}"
    PUT /contacts/{id}  # next_touch_due = today + cadence-spacing[day]
```

Wrap in a transaction-style helper so all three writes succeed or none do.

## 6. BoldTrail API rate-limits the cron

**Symptom:** Cron starts returning 429s. Some leads get processed, others get dropped.

**Why:** Bridge hammering BT without throttling.

**Fix:**
- Limit pulls to last-10-min windows (don't pull all-contacts).
- Use `limit=50` on every list query.
- Exponential backoff on 429: `time.sleep(2 ** attempt)`.
- Cache static fields (contact ID, source) for 1 hour to reduce repeat-fetch.

## 7. Bridge updates `gave_consent` (it shouldn't)

**Symptom:** A contact's `gave_consent` field flipped from True to False via the bridge.

**Why:** Bridge code accidentally wrote to `gave_consent` when it meant to write to `email_optin`.

**Fix:** The bridge should NEVER write `gave_consent`. That field is historical per BT semantics. Only modify `email_optin` and `text_on`:
```python
# Honor unsubscribe — correct
PUT /contacts/{id}  # email_optin=False, text_on=False

# WRONG — never do this
PUT /contacts/{id}  # gave_consent=False  ← BT field semantics violated
```

## 8. Bridge handles a reply parse incorrectly

**Symptom:** Lead replied "STOP putting these in spam folder" (not an unsubscribe — they're complaining about spam folder issues). Bridge interpreted as unsubscribe.

**Why:** Pattern match was too loose: `\bSTOP\b` matched the word out of context.

**Fix:** Tighten the pattern to require unambiguous unsubscribe phrases:
```python
UNSUBSCRIBE_PATTERNS = [
    r"^\s*STOP\s*$",                    # standalone STOP
    r"\bunsubscribe\s+me\b",
    r"\bremove\s+me\s+from\s+(?:your\s+)?list\b",
    r"\bplease\s+take\s+me\s+off\b",
    r"\bdon'?t\s+contact\s+me\b",
]
```

Standalone STOP requires the entire message to be just "STOP" (per TCPA standard SMS conventions).

## 9. Bridge runs without IP whitelist after BT enabled one

**Symptom:** Cron suddenly returning 403s on requests that worked yesterday.

**Why:** BT compliance team added an IP whitelist requirement (typical at enterprise tier).

**Fix:** Email BT API contact, request your machine's current IP added. If you run from a residential ISP with dynamic IP, ask about wider netblock allowlist or move the cron to a small VM with static IP.

This is a real conversation that took Victoria's team 3 weeks. Plan for it.

## 10. Bridge re-enrolls a previously unsubscribed contact

**Symptom:** Contact unsubscribed in February. Bridge re-enrolled them in May after they filled out another form.

**Why:** Re-enrollment didn't check the prior unsubscribe.

**Fix:** When enrolling, ALWAYS check for prior unsubscribe:
```python
def enroll(contact):
    if has_prior_unsubscribe(contact):
        # require explicit re-consent — DON'T auto re-enroll
        # route to re-consent path: form re-submission, ad re-click
        POST /notes  # "Re-enrollment blocked — prior unsubscribe on file"
        return None
    # ... normal enrollment
```

Filling out another form is NOT automatic re-consent. The lead must explicitly re-consent (e.g., a checkbox saying "I previously unsubscribed and want to re-subscribe to communications"). Otherwise you reactivate a TCPA-violating relationship.
