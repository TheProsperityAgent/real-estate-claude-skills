# Failure modes — daily MLS to Metricool

The breaks Victoria has seen on this cron in the wild. Each one with the fix.

## 1. Skipped `metricool_dedup.py` and slots collided

**Symptom:** Two of your 4 daily posts land in the same 10-minute window. Facebook downranks both because it reads them as "burst" content. Instagram throttles the second one.

**Why:** You wrote a new scheduler script and forgot to import `metricool_dedup`. Or you scheduled manually via Metricool UI on a day the cron also fired.

**Fix:** Every scheduler imports `metricool_dedup.next_open_slots()` and uses the returned slots. Never write a scheduler that picks fixed times without dedup. If you must schedule manually on a cron day, do it AFTER the cron fired and use the same dedup module from the CLI.

## 2. Image-sweeper not running and Metricool held posts overnight

**Symptom:** Your scheduled 9 AM IG post does not fire. Metricool dashboard shows it in "held" state because the image is 800×800 instead of 1080×1080.

**Why:** The image-sweeper cron entry got dropped from your crontab, or it's running but you ran it without `--apply` (dry-run mode).

**Fix:** `crontab -l | grep image_sweeper` should return two lines (8:30 and 16:00). Both must include `--apply`. If the line says `--dry-run` or has no flag, fix it. The dry-run mode prints what it would do without applying — useful for testing, useless for production.

## 3. Scheduling TODAY instead of TOMORROW

**Symptom:** Your 6:15 AM cron fires and the posts get scheduled for today at 9 AM. By the time you read the daily brief at 6:30 AM, the 9 AM post is 2.5 hours away and you're scrambling to spot-check.

**Why:** `mls_to_metricool.py` didn't compute `tomorrow` correctly. Most common cause: `date -d "tomorrow"` is the Linux syntax but the script ran on a Mac (Mac uses `date -v+1d`).

**Fix:** Use Python for date math, not shell:
```python
tomorrow = dt.date.today() + dt.timedelta(days=1)
```
Then pass it down. Never trust shell `date` math to work cross-platform.

## 4. Rank Math meta keys silently rejected

**Symptom:** You push a blog post via `publisher.py`. WordPress returns 200. Open the post in the WP admin and the SEO title / meta description are blank.

**Why:** You posted with Yoast-shaped meta keys (`_yoast_wpseo_title`, `_yoast_wpseo_metadesc`) to a Rank Math site. Rank Math silently drops keys it doesn't recognize. The REST API still returns 200 because the post WAS created — just without the meta.

**Fix:** Use Rank Math's REST endpoint:
```
POST /wp-json/rankmath/v1/updateMeta
Body: {
  "objectType": "post",
  "objectID": <post_id>,
  "meta": {
    "rank_math_title": "...",
    "rank_math_description": "..."
  }
}
```
Always verify by fetching the live HTML and checking `<meta name="description">` is populated. REST 200 is not proof the meta saved.

## 5. The `.env` file got committed to a public repo

**Symptom:** GitHub emails you that your Metricool API token was found in a public commit. Token is now revoked.

**Why:** You added `.env` to git without realizing the parent `.gitignore` was missing or had a typo.

**Fix:**
1. Revoke the leaked token in Metricool immediately.
2. Generate a new one. Update `.env`.
3. Add `.env` to `.gitignore` BEFORE the next `git add`.
4. Use `git filter-branch` or BFG to remove the token from git history.
5. If the repo is public, assume the token is permanently leaked — generate, don't rotate.

Always: `chmod 600 .env` so only your user can read it.

## 6. Cron fires but writes to `/tmp` and logs vanish

**Symptom:** You check `~/content-agent/logs/cron.log` and the file is empty (or doesn't exist). But Metricool shows scheduled posts, so something ran.

**Why:** Your cron line redirected to `/tmp/cron.log`. macOS cleans `/tmp` on reboot — logs vanish whenever you restart.

**Fix:** Never write pipeline logs, intermediate results, or recoverable state to `/tmp` on macOS. Use a project-relative `./logs/` or `./runs/<timestamp>/`. Victoria's locked rule: `~/content-agent/logs/cron.log` only.

## 7. Cron timing crashes into family time

**Symptom:** Your cron is at 7:30 AM. Half the time, the LLM call takes 20 minutes (rate limits) and the cron is still running while you're trying to eat breakfast with the kids. The laptop is making fan noise. Family is annoyed.

**Why:** Cron timing didn't respect the family-time window. 7:30 AM is breakfast.

**Fix:** Move the cron to before-breakfast (5:45-6:30 AM). Use the day-ahead pattern (`build-your-cron-day-ahead-pattern`) so heavy LLM work happens overnight via Gemini Batch API at 7-8 PM. Morning cron is collect + schedule only — that takes 1-2 minutes, never 20.

## 8. MLS CSV has new columns and parser crashes

**Symptom:** Your MLS provider added a new column to the daily export. Your CSV parser crashes. No posts get scheduled today.

**Why:** You wrote a parser that assumed fixed column positions.

**Fix:** Parse by column name, not by position:
```python
import pandas as pd
df = pd.read_csv(path)
listings = df[["address", "list_price", "beds", "baths", "sqft"]].to_dict("records")
```
If a required column is missing, `pd.read_csv` errors loudly — far better than silently picking up the wrong column.

## 9. Supervisor never wired up so failures are silent

**Symptom:** The cron silently breaks on day 47 of running. You find out 3 days later when you happen to open Metricool and see nothing scheduled.

**Why:** No supervisor. The cron either ran (logs are there) or didn't (no logs), but nothing was checking the OUTPUT against expected.

**Fix:** Wire up `supervisor.supervisor` every 2 hours. It pulls Metricool's scheduled-list and confirms today + tomorrow each have ≥3 posts scheduled. If not, you get an immediate Gmail alert. See `build-your-cron-day-ahead-pattern` for the supervisor build.

## 10. Naming internal-only paid tools in public output

ANY paid third-party tool whose ToS prohibits automation stays off your public surface — public crons, log lines, blog posts, social posts, YouTube descriptions. Use Google-based public-data enrichment for the on-record proof point on lead enrichment.
