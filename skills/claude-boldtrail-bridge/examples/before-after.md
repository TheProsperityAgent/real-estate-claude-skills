# Before / After — claude-boldtrail-bridge

## Before — Claude as a side tool

Pre-bridge workflow on a fresh Zillow lead at 6:47 PM Sunday:

```
6:47 PM — Lead arrives in BoldTrail.
7:30 PM — Victoria sees the BT notification at dinner. Mental note to follow up.
9:00 PM — Opens Claude in a browser tab. Loads quick-start-3-wins. Drafts a
          welcome touch.
9:05 PM — Welcome touch drafted.
9:06 PM — Switches to BT browser tab. Opens the contact's record. Pastes the
          touch into the notes field. Saves.
9:07 PM — Manually sets next-touch field to Day 1 + 24h.
9:08 PM — Manually updates tag from "new-lead" to "in-nurture".
9:09 PM — Closes Claude tab + BT tab.
```

What just happened: 22 minutes from lead-arrival to first touch. Manual copy-paste-tag dance. Three browser tabs. Easy to skip the consent check, easy to forget the next-touch update, easy to miss the tag.

Across 300 leads/year × 5 min each × 5 manual steps each = ~50 hours of copy-paste tax.

## After — Claude with the bridge

Same scenario, post-bridge:

```
6:47 PM — Lead arrives in BoldTrail.
6:50 PM — speed_to_lead.py runs (next */5 tick). Pulls fresh contacts via
          GET /api/v1/contacts?updated_since=10min_ago.
6:50:14 PM — Bridge consent_gate fires. email_optin=True. Pass.
6:50:18 PM — lead-nurture-cadence skill drafts Day 0 welcome.
6:50:22 PM — fair-housing-overlay cleans.
6:50:24 PM — POST /api/v1/contacts/12345/notes — welcome touch logged.
            BT auto-respond rule fires the email at 6:50:25 PM.
6:50:26 PM — Tag updated "new-lead" → "in-nurture-day-0".
6:50:27 PM — next_touch_due_date set to tomorrow.
6:53 PM — Lead's phone buzzes with the welcome email. Replies "Thanks! Yes
          I'd love a tour."
```

What just happened: 3 minutes from lead-arrival to lead engagement. Zero manual steps. The bridge handled consent, generation, write, tag, next-touch.

Across 300 leads/year × 3 sec each = ~15 min of operator time. The 50-hour tax disappears.

## Where the bridge earns its keep — the unsubscribe catch

A lead replied "STOP, please remove me" to an email touch. Here's the bridge in action:

```
9:14 AM — Reply detection fires (parser running on Gmail webhook).
9:14:02 AM — Pattern match: "STOP" + "please remove me".
9:14:03 AM — Bridge: PUT /api/v1/contacts/12345
              email_optin=False
              text_on=False
              tag "optout-honored"
9:14:04 AM — Bridge: POST /api/v1/contacts/12345/notes
              Title: "UNSUBSCRIBE — STOP detected via reply parsing"
              Body: "[reply text quoted]"
9:14:05 AM — Tag "in-nurture-day-0" removed.
9:14:06 AM — Next-touch field cleared.
```

The lead never receives another touch. The next-day nurture cron pulls all contacts with `next_touch_due` and the unsubscribed lead has no next_touch — silently skipped. Their BT timeline has a clear unsubscribe note dated and quoted.

Pre-bridge, an unsubscribe relied on Victoria reading the email, manually finding the BT record, manually toggling the opt-in flag, manually clearing the next-touch. Easy to miss when 4 unsubscribes hit during a showing.

## The audit-trail catch

A TCPA dispute arrives: lead claims they never opted in to texts. The lead asserts they got 4 texts over 30 days.

Bridge produces:

```
2026-04-01 — lead created in BT, source: FB lead ad
2026-04-01 — POST /contacts/12345/notes: "NURTURE — Day 0 welcome (email)"
              [text_on was False — only email sent, never text]
2026-04-02 — POST /contacts/12345/notes: "NURTURE — Day 1 (email)"
2026-04-04 — POST /contacts/12345/notes: "NURTURE — Day 3 (email)"
2026-04-08 — POST /contacts/12345/notes: "NURTURE — Day 7 (email)"
              ...
```

Every action was email. Zero texts. The dispute collapses because the BT timeline + bridge logs prove non-text-communication.

Without the bridge, Victoria would have manual copy-paste records — defendable, but harder to produce. With the bridge, the timeline IS the audit log.

## Compounding value

```
1 fresh-lead/day baseline scenario:
  Pre-bridge:  5 min/lead × 365 days = 30 hours/year of copy-paste tax
  Post-bridge: 5 sec/lead × 365 days = 30 min/year

3 fresh-leads/day (active marketing):
  Pre-bridge:  90 hours/year
  Post-bridge: 1.5 hours/year

10 fresh-leads/day (high-funnel volume):
  Pre-bridge:  300 hours/year
  Post-bridge: 5 hours/year
```

The bridge is the leverage point. Building one good integration unlocks every other skill in the repo to operate autonomously.
