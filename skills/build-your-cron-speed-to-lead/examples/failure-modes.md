# Failure modes — speed-to-lead

The expensive breaks. Each one with the fix.

## 1. Consent gate bypassed — non-opted-in lead got a touch

**Symptom:** A lead complains "I never signed up for emails from you" and threatens a TCPA suit. You check the logs and find a welcome touch went out 5 minutes after their inquiry — but their `email_optin` was False at the time.

**Why:** The consent gate was either disabled, read the wrong field (`gave_consent` historical instead of `email_optin` current), or had a logic error (`and` instead of `or`).

**Fix:** Hardcode the gate semantics. Test before going live:

```python
def consent_gate(contact):
    if contact.get("email_optin") is True:
        return "email"
    if contact.get("text_on") is True:
        return "text"
    return None  # HARD STOP
```

Never trust `gave_consent` for send decisions. Add a unit test that fails the build if the gate returns a channel for a contact with both opt-ins False.

## 2. Hammering BoldTrail's API

**Symptom:** BoldTrail rate-limits you. 429 responses. Cron starts dropping leads. You get an email from BT compliance asking what your script is doing.

**Why:** You pulled all-contacts every 5 min instead of last-10-min. Or you ran multiple instances of the cron at once.

**Fix:**
- `updated_since` parameter on the GET — pull only contacts updated in the last 10 minutes.
- Limit: 50 per pull. If you have more than 50 fresh leads in 10 minutes, you have a much bigger business than this cron is sized for — re-architect.
- Add a lock file to prevent concurrent runs:
```python
lock_file = Path("~/content-agent/state/speed_to_lead.lock").expanduser()
if lock_file.exists():
    sys.exit(0)  # already running
lock_file.touch()
try:
    main()
finally:
    lock_file.unlink()
```

## 3. Log file blows up

**Symptom:** `speed_to_lead.log` is 4 GB. Filesystem fills up. Other crons start failing.

**Why:** Cron logs every tick (every 5 min × 288 ticks/day × 365 days = 105,000 lines/year minimum), even when nothing happened.

**Fix:** Silent-unless-something-happened pattern. The cron only prints when a lead is processed OR when an error occurs. Most ticks output nothing.

Add log rotation as backup:
```bash
# /etc/newsyslog.d/speed_to_lead.conf (Mac) or logrotate (Linux)
~/content-agent/logs/speed_to_lead.log  640  4  *  $W6  Z
```
4 weeks rolling, gzip old.

## 4. Re-processing same lead every tick

**Symptom:** Same contact ID shows up in `PROCESSED` log 12 times in an hour. BT note timeline has 12 NURTURE notes for one contact.

**Why:** `nurture_state.json` not being read (or not being written) so `is_enrolled(contact_id)` always returns False.

**Fix:** Test the ledger by hand:
```bash
cd ~/content-agent
python3 -c "import nurture_state; print(nurture_state.is_enrolled(12345))"
python3 -c "import nurture_state; nurture_state.enroll(12345, 'Day 0 SENT', None)"
python3 -c "import nurture_state; print(nurture_state.is_enrolled(12345))"
```

The third call must return True. If not, the JSON path is wrong or write permissions are off.

## 5. Atomicity break — `nurture_state.json` corrupted

**Symptom:** `nurture_state.py` errors with `json.decoder.JSONDecodeError`. Cron crashes. No leads get processed.

**Why:** Two cron ticks tried to write the file at once, mid-write. Half-written JSON corrupts the file.

**Fix:** Atomic write — write to temp, rename:
```python
def _save(state):
    LEDGER.parent.mkdir(parents=True, exist_ok=True)
    tmp = LEDGER.with_suffix(".tmp")
    tmp.write_text(json.dumps(state, indent=2))
    tmp.rename(LEDGER)  # atomic on POSIX
```
Plus the lock file from failure mode #2.

## 6. The 11 PM cron tick hits a buyer's Do Not Disturb hours

**Symptom:** Lead complains "you texted me at 11 PM, that's harassment."

**Why:** Your cron isn't time-zone-aware and your BT automation sent an SMS at the lead's local 11 PM. TCPA enforces a 8 AM – 9 PM local-time window for SMS.

**Fix:**
- Add a quiet-hours check in `process_fresh_lead`:
```python
buyer_tz = contact.get("timezone", "America/New_York")
local_now = dt.datetime.now(zoneinfo.ZoneInfo(buyer_tz))
if local_now.hour < 8 or local_now.hour >= 21:
    # Delay until 8 AM local
    schedule_for(contact, when="next_8am_local")
    return
```
- Or configure BoldTrail's auto-respond rule to itself respect quiet hours and let the cron fire the note immediately; BT will hold the actual send until 8 AM local. Recommended.

## 7. Welcome touch references the wrong listing

**Symptom:** Lead inquired about a $400K Greenville 4-bed. Welcome touch references a $500K Winterville 3-bed. Lead replies "wrong house?"

**Why:** `lead-nurture-cadence` skill was passed a generic price band instead of the specific listing the lead inquired on.

**Fix:** Pull the lead's source-context (which listing URL or which ad they clicked) from BT and pass to the skill:
```python
welcome = run_skill(
    skill="lead-nurture-cadence",
    inputs={
        ...,
        "source_listing": contact.get("source_listing_url"),
        "source_listing_address": contact.get("source_listing_address"),
        "source_listing_price": contact.get("source_listing_price"),
    },
)
```

## 8. The skill produces a cold-text-blast pattern

**Symptom:** `lead-nurture-cadence` returns an SMS template that includes "we're following up with all leads in your area" or any other mass-blast pattern.

**Why:** The skill should refuse this by design (it's TCPA-prohibited), but a corrupt prompt template can drift.

**Fix:** Post-skill regex check before posting to BT:
```python
BLAST_PATTERNS = [
    r"all leads in your area",
    r"reaching out to everyone",
    r"mass email",
    r"to everyone on our list",
]
for p in BLAST_PATTERNS:
    if re.search(p, welcome, re.I):
        raise BlastPattern("Welcome touch reads as a mass-blast template")
```

## 9. BoldTrail field renamed — `email_optin` becomes `email_opt_in`

**Symptom:** Consent gate returns None for every lead, even ones who opted in. Speed-to-lead skips everything. Logs show "No current opt-in" 50× a day.

**Why:** BoldTrail renamed a field in their API. Your code is still looking for the old name.

**Fix:** Pull a sample contact via curl and `jq keys`:
```bash
curl -s -u "$AUTH" "$BT_BASE/contacts/12345" | jq keys
```
Update the field name in `consent_gate`. Add a smoke test that runs daily:
```python
def daily_smoke_test():
    sample = fetch_one_contact()
    assert "email_optin" in sample, "BT field renamed — update consent_gate"
    assert "text_on" in sample, "BT field renamed — update consent_gate"
```

## 10. The cron disabled because "it never finds anything"

**Symptom:** Six weeks in, you disable the cron because the log is empty 95% of the time and "nothing is happening".

**Why:** Misunderstanding — empty log = working correctly. The cron only logs when a fresh lead is processed.

**Fix:** Look at the BT notes timeline instead of the log. Every fresh lead's BT contact has a `NURTURE — ... — Day 0 welcome` note in the timeline. Count those over a week. The cron is doing its job — silently.

The hardest part of running silent crons is not killing them out of impatience.
