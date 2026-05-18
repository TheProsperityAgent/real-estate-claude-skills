---
name: claude-boldtrail-bridge
description: Connects Claude to BoldTrail (formerly kvCore) so contact data, notes, opt-in status, and nurture state flow between the two systems. The "moat" episode of Season 1 — built after fighting upper management for the integration path.
author: Al Pinder, Victoria Pinder
version: 1.0.0
---

# Claude → BoldTrail / kvCore Bridge

The moat skill of Season 1. Connects Claude to BoldTrail so every other skill in this repo can read contact context (consent state, prior notes, last touch date) and write back updates (touch logged, next-touch date set, opt-out honored). Without this bridge, the nurture-cadence skills produce text but you have to copy-paste everything into BoldTrail manually.

## When to use this skill

You use BoldTrail (or its legacy kvCore name) as your CRM. You want Claude-generated nurture touches to actually post to the right contact's note timeline + update the next-touch field. You want Claude to honor `email_optin` / `text_on` / `gave_consent` automatically.

This is the integration that makes the rest of the stack autonomous instead of advisory.

## Architecture

```
[Claude / a skill]
       │
       ├── Read: GET /api/v1/contacts/{id}
       │     ↳ name, email, phone, gave_consent, email_optin, text_on, tags, notes
       │
       ├── Generate: nurture touch using lead-nurture-cadence skill
       │
       └── Write: POST /api/v1/contacts/{id}/notes
             ↳ note title (PUBLIC-DATA — YYYY-MM-DD for AI-enriched lookups / NURTURE — for cadence)
             ↳ note body (the actual touch content)
             ↳ tag updates ("nurture-active" → "nurture-paused" if opt-out detected)
```

## Prerequisite — auth setup

```
1. BoldTrail dashboard → Settings → API → Generate API key
2. Encode as Basic Auth: base64("{api_key}:{user_id}")
3. Store in environment variable BOLDTRAIL_BASIC_AUTH
4. If your IP is whitelisted, requests work directly. Otherwise BoldTrail
   may require an enterprise-tier plan + IP whitelist conversation with
   their API team.
```

Note: BoldTrail's API is documented for select partners. If your dashboard doesn't show an API tab, escalate to upper management and request the integration path. This is the ENTIRE story of Episode 7.

## Prompt

```
I'm a real estate agent using BoldTrail. Bridge Claude to my BoldTrail
CRM for [READ / WRITE / READ+WRITE] operations.

OPERATION: [read contact / write note / update contact tag / pull nurture queue]
CONTACT (if known): [contact_id or name+email]
TASK: [what should happen on each side]

BOLDTRAIL CONSENT FIELDS (per memory):
- gave_consent: historical (stays True after unsub)
- email_optin: current state (trust this for email decisions)
- text_on: current state (trust this for SMS decisions)

REQUIRED COMPLIANCE (load fair-housing-overlay):
- TCPA: NEVER generate touch content for contacts where email_optin=False AND text_on=False
- TCPA: NEVER bypass an opt-out by reading historical gave_consent=True
- FH: lead routing decisions cannot be made on demographic factors
- ALWAYS log every Claude-generated action to the contact's note timeline

OUTPUT — paired actions:

1. READ STEP: the exact GET request to fetch the contact
2. CONSENT GATE: a yes/no decision based on email_optin + text_on
3. GENERATE STEP: the touch content (delegate to lead-nurture-cadence skill)
4. WRITE STEP: the exact POST request to log the note
5. NEXT-TOUCH: the suggested next-touch date (3 / 7 / 14 / 30 days based on cadence stage)

If consent gate fails (unsubscribed / no permission), skill OUTPUTS NOTHING
except a note logged "Skipped — consent withdrawn YYYY-MM-DD" so the audit
trail exists.
```

## Common workflows

**Pull tomorrow's call list:**
`GET /api/v1/contacts?next_touch_due_before=tomorrow&consent=opted-in&limit=50`

**Push a Claude-drafted note for a buyer in pre-approval:**
1. READ the contact
2. Run [`lead-nurture-cadence`](../lead-nurture-cadence/) for "actively browsing / pre-approved" stage
3. WRITE the note with title `NURTURE — {date} — pre-approved buyer touch`
4. Update tag `nurture-stage` to next value

**Honor a Claude-detected unsubscribe:**
1. Pattern-match Claude's reply parsing for "STOP" / "unsubscribe" / "remove me"
2. WRITE a system note: `Unsubscribe detected via Claude reply parsing on {date}`
3. PUT update: `email_optin=False`, `text_on=False`, tag `optout-honored`

## Companion content

YouTube Episode 7: [Claude → BoldTrail/kvCore Integration](https://youtube.com/@theprosperityagents)

This episode covers the negotiation that unlocked the integration path. The story IS the differentiator — most agents don't know BoldTrail's API exists at all.

## Compliance notes

Loads [`fair-housing-overlay`](../fair-housing-overlay/). Critical bridge-specific rules:
- The consent gate is a HARD STOP — content is never generated for non-consenting contacts
- All Claude-driven actions log to the note timeline (audit trail required for TCPA disputes)
- The skill never updates `gave_consent` directly — only `email_optin` / `text_on` (per BoldTrail field semantics)
- Re-engagement of unsubscribed contacts requires explicit re-consent (form re-submission, ad re-click) — the skill refuses to bridge for cold-list re-engagement

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
