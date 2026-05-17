# Failure modes — compliance supervisor

## 1. False positive nukes a perfectly fine post

**Symptom:** Listing description gets blocked. You open the file. The "violation" is the word "school" appearing in the neighborhood name (e.g., "Located in Oakwood School District, a 50-year-old subdivision named after a school that has since closed.").

**Why:** Regex pattern matched without word-boundary protection. Or pattern matched a legitimate place name.

**Fix:** Refine the pattern with negative lookaheads/lookbehinds:

```python
# Block "great schools" but allow "Oakwood School District" as a place name
r"\b(?<!subdivision\snamed\safter\sthe\s)great\s+schools?\b"
```

Or pre-process the body to mask known proper-noun matches before running checks. Maintain a `proper_nouns.txt` list of subdivisions, parks, employer names that contain trigger words. Mask them with placeholders, run checks, unmask.

## 2. Live MLS API down — supervisor stalls

**Symptom:** Supervisor stops processing the queue after the first item that hits `check_closed_or_uc`. Stuck waiting for the MLS API timeout.

**Why:** `requests.get` with no timeout — defaults to forever.

**Fix:**
```python
r = requests.get(f"{MLS_API}/listing/{mls_id}/status", timeout=10)
```
Plus a fallback: if MLS API is unreachable for >5 minutes, fail open (allow the post, log a warning) — don't halt the entire publishing pipeline because of an upstream MLS outage. Alert the agent so they can manually verify if it matters.

## 3. Opt-out cache stale by hours

**Symptom:** A lead unsubscribes at 10:30 AM. Cache last refreshed at 8:30 AM. 11:00 AM compliance run uses stale cache, approves a touch. Touch lands in their inbox.

**Why:** Cache TTL too long.

**Fix:** Two-layer check:
1. Cache refresh every 4 hours (covers most cases).
2. For high-stakes touches (paid-ad recipients, paying-client communication), do a real-time CRM lookup at the moment of publish — not just at supervisor time.

The supervisor is the floor. Real-time check is the ceiling for high-stakes content.

## 4. Daily-cap rolls over at wrong midnight

**Symptom:** Friday at 11:30 PM Eastern, you have 4 IG posts scheduled for Friday. The supervisor approves a 5th post for "Saturday" because UTC midnight already passed. Facebook downranks Friday's bunch.

**Why:** Cron ran without `TZ=America/New_York`. Daily-cap counter rolled over at UTC midnight (7 PM Eastern), so it reset 4 hours early.

**Fix:** Hardcode the timezone in the cron line:
```cron
0 11 * * * cd ~/content-agent && TZ=America/New_York python3 compliance_supervisor.py >> ~/content-agent/logs/compliance.log 2>&1
```
And in `check_daily_cap`:
```python
import zoneinfo
local_today = dt.datetime.now(zoneinfo.ZoneInfo("America/New_York")).date()
```

## 5. Supervisor catches everything → real publishing stops

**Symptom:** Supervisor flags 20 items as blocked overnight. Tomorrow's queue is empty. Nothing publishes.

**Why:** A pattern in the blocklist is too aggressive (probably new), or the upstream generator drifted (probably new model behavior) and 90% of its output now violates.

**Fix:** Two paths:
1. **If the pattern is wrong:** review the blocked items, identify the over-matching pattern, refine it.
2. **If the upstream generator drifted:** review the patterns the items hit, update the upstream skill's `fair-housing-overlay` reference to be stricter at generation time.

Either way, fix the source. Do not disable the check.

## 6. Dedup hash too sensitive — near-identical drafts blocked

**Symptom:** Two listing descriptions for the same property type (3-bed Winterville) get hash-deduped because the body text is 95% identical (same template).

**Why:** MD5 hash is binary — any 1-char difference is a miss, but two near-identical bodies look identical to a human.

**Fix:** Use a fuzzy hash (e.g., `simhash` or `tlsh`) and threshold at 90% similarity. Or template-aware comparison that ignores the listing-specific data and compares only the narrative shell.

## 7. The "I'll review blocked items later" failure

**Symptom:** Two weeks of blocked items pile up in `/content/blocked/`. You never read the daily brief carefully. Compounding violations queue up because the underlying skill drift wasn't fixed.

**Why:** Human review step skipped.

**Fix:** Make the daily brief LOUD when blocks pile up:
```python
if len(blocked) > 5:
    subject = f"⚠ {len(blocked)} compliance blocks — review needed"
elif len(blocked) > 0:
    subject = f"Compliance: {len(blocked)} items held"
else:
    subject = "Compliance: clean"
```
Make the alert escalate proportionally to the backlog.

## 8. Image-rules check runs without the image present

**Symptom:** Post has a `<img>` reference but the image file hasn't been generated yet (generation happens later in the pipeline). The check fails because there's no image to verify dimensions on.

**Why:** Supervisor running before image generator completes.

**Fix:** Either reorder the pipeline (image generation BEFORE supervisor) or make the image-rules check tolerant: if image not yet rendered, mark as `deferred` and re-run at the next supervisor pass.

## 9. License-disclosure check trips on a buyer-side note that doesn't need it

**Symptom:** Internal BoldTrail notes get blocked because they lack a license number — but internal notes don't need one (only public-facing marketing does).

**Why:** Check applied universally without scoping to public vs internal.

**Fix:** Tag content items with a `surface` field (`public_marketing` / `internal_note` / `email_to_client`). Run license-disclosure check only when `surface == "public_marketing"`.

## 10. The supervisor itself becomes a single point of failure

**Symptom:** Supervisor crashes at 11:00:14. The day's content never gets reviewed. Tomorrow's queue ships without compliance.

**Why:** Crashes happen. Code is mortal.

**Fix:** Add a watchdog:
```python
# wrapped supervisor
import sys
try:
    main()
    write_health_status("PASS", dt.datetime.now())
except Exception as e:
    write_health_status("FAIL", dt.datetime.now(), str(e))
    raise
```
Then `supervisor.supervisor` (the every-2-hour cross-cron verifier — see `build-your-cron-day-ahead-pattern`) checks compliance health every 2 hours. If health status is FAIL, immediate Gmail alert.

The supervisor watches the supervisor.
