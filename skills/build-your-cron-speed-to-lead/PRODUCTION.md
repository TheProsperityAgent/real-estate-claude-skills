# PRODUCTION — build-your-cron-speed-to-lead

How to actually ship this cron on your machine. Same flow Victoria walks new Pinder Team agents through.

## What you need

- BoldTrail (or kvCore legacy) CRM with API access. Most plans have it; Enterprise tier may be required for unrestricted endpoints. If your dashboard does not show an API tab, you need to escalate to upper management at BoldTrail and request the integration path. This negotiation is part of the Episode 7 story — most agents do not know the API exists.
- Basic Auth credentials: base64 of `{api_key}:{user_id}`. Generate in BoldTrail → Settings → API. Encode and store in `BOLDTRAIL_BASIC_AUTH` in your `.env`.
- IP whitelist setup if BoldTrail's compliance team requires it (depends on plan).
- Anthropic API key for the `lead-nurture-cadence` generation call.
- The `claude-boldtrail-bridge` + `lead-nurture-cadence` + `fair-housing-overlay` skills loaded.

## Step 1 — Test the BoldTrail API connection in isolation

Before wiring any cron, prove the API works from your machine:

```bash
curl -s -u "$BT_API_KEY:$BT_USER_ID" \
  "https://api.boldtrail.com/api/v1/contacts?limit=1" | jq .
```

If you get a contact back, you're good. If you get 401, the auth is wrong. If you get 403, you might need an IP whitelist. If you get 404, the endpoint path is wrong for your tier.

Do not move forward until this curl works.

## Step 2 — Run `speed_to_lead.py` by hand once

Drop in a test contact in BoldTrail with `email_optin=True`. Wait 30 seconds. Run:

```bash
cd ~/content-agent
set -a; . .env; set +a
python3 speed_to_lead.py
```

You should see one of these outputs:

- `[2026-05-17 14:23:01] PROCESSED contact 12345 (email)` — works.
- `[2026-05-17 14:23:01] 1 fresh leads processed` — works.
- (no output) — no fresh leads in the window; expected if test contact is older than 10 min.

Open BoldTrail and find the test contact. There should be a new note titled `NURTURE — 2026-05-17 — Day 0 welcome` with the generated touch body.

If the note never lands, debug the POST in `process_fresh_lead`. Most likely the URL path is wrong for your BoldTrail tier or the auth header is malformed.

## Step 3 — Test the consent gate

Create a second test contact with `email_optin=False` and `text_on=False`. Run `speed_to_lead.py` again.

You should see no `PROCESSED` line — but you SHOULD see a new note on that contact titled `SPEED-TO-LEAD — skipped` with body `No current opt-in (email_optin + text_on both False)`.

This is the TCPA audit trail. If a dispute hits, you have proof you did NOT communicate with the non-opted-in lead AND that you logged the skip.

If the skip note doesn't land, the `log_skip` function is broken. Fix before going live.

## Step 4 — Install the cron

```cron
*/5 * * * * cd ~/content-agent && set -a && . ./.env && set +a && python3 speed_to_lead.py >> ~/content-agent/logs/speed_to_lead.log 2>&1
```

Paste into `crontab -e`. Save.

Verify:
```bash
crontab -l | grep speed_to_lead
```

Should return the line.

## Step 5 — Wait an hour, then check the log

```bash
tail -50 ~/content-agent/logs/speed_to_lead.log
```

Expected: zero output if no fresh leads came in during the hour. If a fresh lead arrived, one line per lead processed.

The log being mostly empty is correct. Silent crons are gold.

## Step 6 — Wait a week, then check the metrics

After 7 days:

1. Pull `speed_to_lead.log` — count `PROCESSED` lines. That's the number of leads that hit Day 0 within 5-15 min.
2. Pull `nurture_state.json` — count enrolled contacts in the last 7 days.
3. Pull BoldTrail's contact list — count contacts where `created_at` is within the last 7 days AND there is a note titled `NURTURE — ... — Day 0 welcome`.

The three numbers should match. If they don't, something is dropping leads:

- More leads in BT than in nurture_state → enrollment is failing.
- More enrollments than PROCESSED log lines → the log is being truncated or rotated mid-run.
- More PROCESSED than enrollments → you're re-processing contacts (the `is_enrolled` check is broken).

## What changes the day you ship this cron

Day 1: a Zillow inquiry hits at 6:47 PM on Sunday. The cron picks it up at 6:50 PM. By 6:51 PM, the welcome touch is in their inbox. Buyer replies at 6:53 PM. By Monday morning you have a showing scheduled.

That outcome happens 5-10% of the time. The other 90-95% of the time the welcome touch lands and nothing happens. But the 5-10% conversion lift is what compounds across the year.

Most agents you compete against still rely on a daily 10 AM nurture run. You are 22 hours ahead by 8 AM the next day.

## What can break in production

1. **BoldTrail rate-limits the API.** Their plan-tier rate limits are real. If you blow through, the cron skips that 5-minute tick. Fix: track 429s, exponential backoff, alert if backoff exceeds 30 min.
2. **An opt-in flag goes stale.** The lead opted in via form, then unsubscribed via email reply. `email_optin` flips to False. The next cron tick correctly skips them — but you might still be on file as having sent the welcome touch 6 hours earlier. That's fine; the welcome touch was opt-in at send-time.
3. **The Anthropic API rate-limits.** During a busy lead-hour, multiple cron ticks try to generate at once. Either queue or use Gemini Batch API for non-Day-0 touches (Day 0 must be live for the speed-to-lead window).
4. **`nurture_state.json` corruption.** A bad write mid-cron leaves the file half-finished. Next cron crashes. Mitigation: write to a temp file and rename atomically.

## The TCPA audit trail — keep this in writing

Anywhere in this repo or on your machine: if a TCPA dispute escalates, you produce:

1. The `speed_to_lead.log` entries showing every lead processed + every skip.
2. The BoldTrail notes timeline (each `NURTURE` or `SPEED-TO-LEAD — skipped` note timestamped).
3. The `nurture_state.json` snapshot showing enrollment decisions.

Together that's the paper trail. The lawyer (the dispute lawyer, not yours) cannot argue you "blasted them" if your own logs show the consent gate fired and the skip was recorded.

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
