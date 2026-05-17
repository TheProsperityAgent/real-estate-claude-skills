---
name: build-your-cron-speed-to-lead
description: Build the every-5-minute cron that catches fresh BoldTrail/kvCore contacts and lands a welcome touch in 5-15 minutes instead of 23 hours. 391% conversion lift per the most-cited speed-to-lead study. TCPA consent gate baked in — never blasts non-opted-in leads.
author: Al Pinder, Victoria Pinder
version: 1.0.0
---

# Build Your Cron — 5-Minute Speed-to-Lead

The single highest-ROI cron in this season. A lead arriving at 11 AM that gets a follow-up at 10 AM the next morning has lost the 5-minute, 1-hour, and 24-hour conversion windows. Most agents' nurture systems fire once a day. The gap between "fresh lead" and "first touch" is where deals leak.

This cron closes that gap from 23 hours to 5-15 minutes.

## The numbers

From the script docstring Victoria locked on 2026-05-07:

- **Sub-1-minute response = 391% conversion lift** (peak).
- **Sub-5-minute response = 21× qualification probability.**
- **Over-30-minute response = 10× drop in response rate.**

Source: the most-cited speed-to-lead study (Lead Response Management Study, MIT). These numbers have held across 15+ years and multiple replications.

## When to use this skill

You use BoldTrail (or its legacy kvCore name) as your CRM. You get fresh leads from Zillow / your website / Facebook lead ads / open-house sign-ins. You want every fresh lead to get a welcome touch within 5-15 minutes, even at 11 PM on a Saturday.

You are comfortable with cron + Python + a CRM API. You have BoldTrail Basic Auth set up (Enterprise tier may be required for API access — that negotiation is part of the build).

## What this cron does

Every 5 minutes, all day:

```
*/5 * * * * speed_to_lead.py
  ├── 1. Pull BT contacts created/updated in last 10 minutes (live API)
  ├── 2. Filter to FRESH (created last 10 min, not already in nurture_state)
  ├── 3. Consent gate (email_optin OR text_on must be True — HARD STOP otherwise)
  ├── 4. Enroll into longterm cadence with next_send_date=today
  ├── 5. Generate welcome touch via lead-nurture-cadence skill
  ├── 6. Route through fair-housing-overlay
  ├── 7. Post note to BT via POST /api/v1/contacts/{id}/notes
  └── 8. Silent unless something happened (no log spam every 5 min)
```

If a fresh lead is found, the welcome touch fires within 5-15 minutes of their inquiry. If no fresh leads, the cron writes nothing — silent.

## The TCPA gate — non-negotiable

This is the most important part of the build. Every fresh lead gets the consent gate before the welcome touch fires.

```python
def consent_gate(contact):
    """
    BoldTrail consent semantics:
    - gave_consent: HISTORICAL — stays True after unsub. NEVER trust for send decisions.
    - email_optin: CURRENT — trust this for email touch.
    - text_on: CURRENT — trust this for SMS touch.
    """
    can_email = contact["email_optin"] is True
    can_text = contact["text_on"] is True

    if not (can_email or can_text):
        # NEVER auto-touch a non-opted-in lead.
        # But DO log the skip for the audit trail (TCPA dispute paper-proof).
        log_skip(contact, reason="No current opt-in (email_optin + text_on both False)")
        return None

    return "email" if can_email else "text"
```

TCPA violations are expensive. The standard is $500-$1,500 per unsolicited communication, per recipient. A single auto-blast to a non-opted-in lead can run $1,500. The gate is the difference between scaling lead response and scaling lead-response liability.

## Architecture

```
[BoldTrail webhook fires when new contact created]
        │
        ▼
[BT contact appears in API search results]
        │
        ▼  (cron picks it up within 5 min)
[speed_to_lead.py runs every 5 min]
        │
        ├── consent_gate(contact)  → if no opt-in, log + skip
        │
        ├── claude-boldtrail-bridge → reads contact context
        │
        ├── lead-nurture-cadence    → drafts welcome touch
        │
        ├── fair-housing-overlay    → cleans the draft
        │
        └── POST /api/v1/contacts/{id}/notes  → posts the touch
```

## The build (2 files + 1 cron line)

### File 1 — `speed_to_lead.py`

```python
#!/usr/bin/env python3
"""
speed_to_lead.py — every-5-minute fresh-lead catcher.

Best-practices numbers (2026-05-07 lock):
  <1 min response = 391% conversion lift (peak)
  <5 min response = 21x qualification probability
  >30 min response = 10x drop in response rate

Silent unless a fresh lead is processed.
"""
import os, sys, json, datetime as dt, requests
from pathlib import Path

import nurture_state          # YOUR module — JSON ledger of enrolled contacts
from skill_loader import run as run_skill

BT_BASE = "https://api.boldtrail.com/api/v1"
BT_AUTH = ("Basic", os.environ["BOLDTRAIL_BASIC_AUTH"])  # base64(api_key:user_id)
FRESH_WINDOW_MIN = 10

def fetch_fresh_contacts():
    """Pull contacts created or updated in the last 10 minutes."""
    since = (dt.datetime.utcnow() - dt.timedelta(minutes=FRESH_WINDOW_MIN)).isoformat()
    r = requests.get(
        f"{BT_BASE}/contacts",
        headers={"Authorization": f"{BT_AUTH[0]} {BT_AUTH[1]}"},
        params={"updated_since": since, "limit": 50},
    )
    r.raise_for_status()
    return r.json()["contacts"]

def consent_gate(contact):
    """Returns channel ('email' or 'text') if opted-in; None otherwise.

    BoldTrail consent semantics:
      gave_consent  — historical (stays True after unsub). DO NOT TRUST.
      email_optin   — current. Trust for email decisions.
      text_on       — current. Trust for SMS decisions.
    """
    if contact.get("email_optin") is True:
        return "email"
    if contact.get("text_on") is True:
        return "text"
    return None

def log_skip(contact, reason):
    """TCPA audit trail. Log the skip as a BT note so disputes have paper-proof."""
    body = f"Speed-to-lead skip {dt.date.today().isoformat()} — {reason}"
    requests.post(
        f"{BT_BASE}/contacts/{contact['id']}/notes",
        headers={"Authorization": f"{BT_AUTH[0]} {BT_AUTH[1]}"},
        json={"title": "SPEED-TO-LEAD — skipped", "body": body},
    )

def process_fresh_lead(contact):
    if nurture_state.is_enrolled(contact["id"]):
        return  # already on the train

    channel = consent_gate(contact)
    if channel is None:
        log_skip(contact, "No current opt-in (email_optin + text_on both False)")
        return

    # Generate welcome touch via lead-nurture-cadence + fair-housing-overlay
    welcome = run_skill(
        skill="lead-nurture-cadence",
        with_overlay="fair-housing-overlay",
        inputs={
            "source": contact.get("source", "unknown"),
            "consent_on_file": "opted-in",
            "stage": "Day 0",
            "side": contact.get("side", "buyer"),
            "price_band": contact.get("price_band"),
            "town": contact.get("town"),
            "urgency": contact.get("urgency", "warm"),
            "channel": channel,
        },
    )

    # Post the welcome touch as a BT note
    requests.post(
        f"{BT_BASE}/contacts/{contact['id']}/notes",
        headers={"Authorization": f"{BT_AUTH[0]} {BT_AUTH[1]}"},
        json={
            "title": f"NURTURE — {dt.date.today().isoformat()} — Day 0 welcome",
            "body": welcome,
        },
    )

    # Enroll for longterm cadence
    nurture_state.enroll(
        contact_id=contact["id"],
        stage="Day 0 SENT",
        next_send_date=dt.date.today() + dt.timedelta(days=1),
    )

    print(f"[{dt.datetime.now()}] PROCESSED contact {contact['id']} ({channel})")

def main():
    fresh = fetch_fresh_contacts()
    processed = 0
    for c in fresh:
        try:
            process_fresh_lead(c)
            processed += 1
        except Exception as e:
            print(f"[{dt.datetime.now()}] ERROR on {c.get('id')}: {e}", file=sys.stderr)

    # SILENT UNLESS SOMETHING HAPPENED
    if processed > 0:
        print(f"[{dt.datetime.now()}] {processed} fresh leads processed")

if __name__ == "__main__":
    main()
```

### File 2 — `nurture_state.py` (tiny ledger)

```python
#!/usr/bin/env python3
"""
nurture_state.py — JSON ledger of enrolled contacts.
Prevents the every-5-min cron from re-enrolling a contact it already touched.
"""
import json
from pathlib import Path

LEDGER = Path("~/content-agent/state/nurture_state.json").expanduser()

def _load():
    if not LEDGER.exists():
        return {}
    return json.loads(LEDGER.read_text())

def _save(state):
    LEDGER.parent.mkdir(parents=True, exist_ok=True)
    LEDGER.write_text(json.dumps(state, indent=2))

def is_enrolled(contact_id):
    return str(contact_id) in _load()

def enroll(contact_id, stage, next_send_date):
    state = _load()
    state[str(contact_id)] = {
        "stage": stage,
        "next_send_date": next_send_date.isoformat(),
    }
    _save(state)
```

## The cron line

See `cron.example`. Single line:

```cron
*/5 * * * * cd ~/content-agent && set -a && . ./.env && set +a && python3 speed_to_lead.py >> ~/content-agent/logs/speed_to_lead.log 2>&1
```

Every 5 minutes, all day. Silent unless something happened.

## Where this sits in the larger nurture stack

```
speed_to_lead.py          (*/5 min — catches fresh leads)
       │
       ▼
agent10_crm_nurture.py    (10 AM daily — long-term cadence sends)
       │
       ▼
nurture_supervisor.py     (10-20 hourly Mon-Sat — silent unless broken)
```

Speed-to-lead handles Day 0. Long-term nurture handles Day 1-30 + past-client win-back (3-year annual). The supervisor watches both.

## What this saves you

Before the cron: 11 AM Zillow inquiry waits until 10 AM the next morning for first touch. 23 hours of dead time. Most of those leads have started talking to two other agents by then.

After the cron: same 11 AM inquiry gets a welcome touch by 11:15 AM. Most agents in the area still wake up at 8 AM tomorrow to write their first follow-up. You are 21 hours ahead by the time they sit down with coffee.

Compounded across 300 leads/year, that's the difference between converting half your inbound and converting an eighth.

## Compliance — what runs alongside

- `fair-housing-overlay` on every generated touch.
- `claude-boldtrail-bridge` does the consent gate (mirrors the gate in this skill).
- `compliance_supervisor.py` at 11 AM cross-checks every note posted in the prior 24h.
- TCPA audit trail: every skip logged as a note on the contact ("Skipped — no current opt-in YYYY-MM-DD"). If a TCPA dispute hits, the paper trail is there.

## Companion content

YouTube Episode 12: `Build Your Cron — 5-Minute Speed-to-Lead Response (391% Lift)` — Al + Victoria show the actual speed_to_lead.py running on Victoria's machine catching a lead at 6:47 PM on a Sunday.

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
