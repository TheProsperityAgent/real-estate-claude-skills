# Failure modes — day-ahead orchestration

## 1. Morning crons creep past the family-time window

**Symptom:** Cron entries originally at 6:15, 6:30, 6:45 slowly grow to 7:15, 7:30, 7:45 over months. You're back to laptop-time-during-breakfast.

**Why:** "Just one more thing." Each new cron seems harmless. The window erodes.

**Fix:**
1. Write the window down in `~/content-agent/CRON_WINDOW.md`. Audit monthly.
2. If a new cron doesn't fit inside 5:45-7:30, default to running it the night before OR queuing it for the next day's morning slot.
3. Anything LLM-heavy ALWAYS goes to evening prep. Morning is collect + schedule + verify only.

## 2. Gemini Batch job stuck overnight, morning collect runs empty

**Symptom:** 6:15 AM `run_daily.sh` runs. Batch collect returns 0 results. `main.py` has no drafts to publish.

**Why:** Batch API took longer than expected. The 7:45 PM submission is still processing at 6:15 AM.

**Fix:**
- Submit earlier (7:00 PM instead of 9:00 PM).
- `batch_content_submit.py --collect` should wait up to 30 minutes for completion before giving up.
- If still empty, fall back to a live API call for the highest-priority drafts so something publishes.

```python
# in run_daily.sh
python3 batch_content_submit.py --collect || \
  python3 main.py --target-date "$TOMORROW" --fallback-live
```

## 3. `supervisor.supervisor` doesn't actually check enough

**Symptom:** A cron silently fails. Supervisor runs fine. Days later you find the missing output.

**Why:** Supervisor was checking "did the log file have any line today" instead of "did the log have the expected SUCCESS line".

**Fix:** Check for SUCCESS markers in logs, not just non-empty:

```python
def check_run_daily():
    log = Path("~/content-agent/logs/cron.log").expanduser().read_text()
    today = dt.date.today().isoformat()
    # Look for RUN_DAILY FINISH line with today's date
    pattern = rf"\[{today}.*RUN_DAILY FINISH"
    if not re.search(pattern, log):
        return False, "No RUN_DAILY FINISH today"
    return True, ""
```

Empty log != success. Success has a specific marker.

## 4. Cron uses `date -d "tomorrow"` on a Mac

**Symptom:** `run_daily.sh` errors with `date: illegal option -- d`. Or worse, returns an empty string and everything downstream uses the wrong date.

**Why:** `date -d` is Linux. Mac uses `date -v+1d`.

**Fix:** Use Python for date arithmetic:
```bash
TOMORROW=$(python3 -c "import datetime; print((datetime.date.today() + datetime.timedelta(days=1)).isoformat())")
```
Cross-platform, no shell dialect issues.

## 5. Alerts go to email that's never checked

**Symptom:** Supervisor sends 12 alerts over 3 weeks. You don't see them because the alerts go to a Gmail you only check on Sundays.

**Why:** Alert path was the cheap-to-set-up channel, not the channel you actually live in.

**Fix:** Alert through the channel you check every hour. For most agents that's:
- Phone push (via terminal-notifier on Mac, or a Pushover/Pushbullet API).
- SMS via a Twilio sub-account ($1/month).
- Slack/Discord webhook to a channel you keep open.

Email is the LAST resort for cron alerts.

## 6. Multiple cron entries write to the same log file

**Symptom:** Log file has interleaved output from 3 crons. Hard to debug which cron produced which line.

**Why:** Convenience — easier to have one log than 10.

**Fix:** One log per cron:
```cron
15 6 * * * cd ~/content-agent && bash run_daily.sh >> logs/cron_run_daily.log 2>&1
30 8 * * * cd ~/content-agent && python3 metricool_image_sweeper.py --apply >> logs/cron_sweeper.log 2>&1
```

Logrotate each one weekly. Worth the 10 minutes of setup.

## 7. Writing logs to `/tmp`

**Symptom:** After Mac restart, all logs are gone. Cannot reconstruct what ran.

**Why:** macOS cleans `/tmp` on reboot. You wrote logs there because the path was short.

**Fix:** NEVER write pipeline logs, intermediate results, or recoverable state to `/tmp` on macOS. Use `~/content-agent/logs/` or `~/content-agent/runs/<timestamp>/`.

This is a Victoria-locked rule across every project, every cron, every script.

## 8. Cron starts BEFORE the `.env` is loaded

**Symptom:** Cron fires. Script errors with `KeyError: 'ANTHROPIC_API_KEY'`. The `.env` is right there.

**Why:** Cron runs in a minimal shell — no profile, no `.bashrc`, no `.zshrc`. Your `.env` was sourced in your interactive shell but NOT in the cron shell.

**Fix:** Source `.env` inline in the cron line or in the script's first line:
```cron
15 6 * * * cd ~/content-agent && set -a && . ./.env && set +a && bash run_daily.sh >> logs/cron.log 2>&1
```
Or:
```bash
#!/bin/bash
set -a
. ~/content-agent/.env
set +a
```

## 9. App Password expires silently

**Symptom:** Supervisor alerts via Gmail. Gmail App Password expired 30 days ago. The supervisor itself is broken — no alerts arrive.

**Why:** Google rotates App Passwords on a schedule.

**Fix:** Run a separate `credential_monitor.py` at 7:15 AM that pings each credential and alerts (via a SECONDARY channel — Pushover or Slack, not the same Gmail) if any are stale.

```python
# credential_monitor.py
def check_gmail():
    try:
        s = smtplib.SMTP_SSL("smtp.gmail.com", 465)
        s.login(GMAIL_FROM, GMAIL_APP_PASS)
        s.quit()
        return True, ""
    except smtplib.SMTPAuthenticationError:
        return False, "Gmail App Password expired"
```

## 10. The morning cron silently doesn't start because cron daemon isn't running

**Symptom:** Several days in a row, nothing happens at 6:15 AM. No log entries. No alerts. (Supervisor itself isn't running either.)

**Why:** On a Mac, `cron` (or `launchd`) was disabled by a system update or by Full Disk Access permission changes.

**Fix:**
- Verify cron is running: `pgrep -lf cron` should return a process.
- On Mac, grant Full Disk Access to `cron` in System Preferences → Privacy & Security.
- For mission-critical crons, consider `launchd` plists with `KeepAlive=true` — more robust than cron on Mac.

The day cron daemon dies, every other safety net dies with it. Make a manual "is cron alive?" check part of your weekly habit until you trust the daemon.
