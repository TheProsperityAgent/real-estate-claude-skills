# PRODUCTION — build-your-cron-day-ahead-pattern

How to actually ship the day-ahead pattern. The other four cron-class skills assume this is in place.

## What you need

- An evening batch API. Gemini Batch API is the cheapest (50% off live). OpenAI Batch API works too. Anthropic doesn't have a batch tier yet — use Gemini for batch, keep Anthropic for time-sensitive live calls.
- A morning slot in your day. 5:45-7:30 AM works for Victoria; pick yours and protect it.
- Gmail App Password for the alert-email path. (Or Discord webhook, Slack, etc. — the supervisor needs SOMETHING to alert through.)
- The other four cron-class skills running, OR your own equivalent crons that need orchestrating.

## Step 1 — Map your morning

Write down your morning before you write any cron. What time do you sit down? What time do you eat? What time do you need to be in the car?

Victoria's:
- 5:45 AM — coffee, sit down with the laptop
- 6:15 AM — `run_daily.sh` fires (she's reading, not coding)
- 7:00 AM — close laptop, breakfast with family
- 7:30 AM — kids out the door, she's free to take a showing call

Yours might be 6:00-7:30. Or 5:00-6:30. The numbers don't matter — the discipline of WRITING THE WINDOW DOWN does.

Then: no cron fires after the window ends. No exception. If a workflow needs to run in the day, schedule it for the evening prep slot the night before.

## Step 2 — Pick your evening prep slot

Evening prep is when LLM-heavy work happens. Typical slots:

- 7:00 PM — refresh voice gems (light call, fast)
- 7:45 PM — submit tomorrow's content to Gemini Batch API
- 8:15 PM — submit tomorrow's HeyGen renders
- 9:00 PM — laptop closes for the night

The batch API takes hours to complete. By 6:00 AM the next morning, the batch output is ready to collect.

## Step 3 — Build the morning master

Start with `run_daily.sh`. The other crons feed INTO `run_daily.sh` or run alongside it.

```bash
#!/bin/bash
set -a
. ~/content-agent/.env
set +a
cd ~/content-agent

TOMORROW=$(date -v+1d "+%Y-%m-%d")
echo "[$(date)] === RUN_DAILY START — target=$TOMORROW ==="

python3 batch_content_submit.py --collect || echo "Batch collect failed"
python3 main.py --target-date "$TOMORROW" || echo "Main failed"
python3 daily_brief.py

echo "[$(date)] === RUN_DAILY FINISH ==="
```

Test by hand once:
```bash
cd ~/content-agent
bash run_daily.sh
```

If the script reaches "RUN_DAILY FINISH" cleanly, you're ready for cron.

## Step 4 — Install the master cron

```cron
15 6 * * * cd ~/content-agent && bash run_daily.sh >> ~/content-agent/logs/cron.log 2>&1
```

Verify:
```bash
crontab -l | grep run_daily
```

## Step 5 — Add the supervisor

This is the step most agents skip. Don't.

```cron
0 */2 * * * cd ~/content-agent && python3 supervisor/supervisor.py >> ~/content-agent/logs/supervisor.log 2>&1
```

Every 2 hours, all day, every day. Silent unless something is broken.

After 48 hours of running, check the log:
```bash
tail -20 ~/content-agent/logs/supervisor.log
```

Should be mostly empty. Maybe one or two lines per cron failure. That's the supervisor doing its job — silent until something requires your attention.

## Step 6 — Wire the alert path

The supervisor only matters if you actually see the alerts. Wire to one of:

- Gmail App Password + SMTP (most universal).
- Slack webhook (if you live in Slack).
- Discord webhook (free, simple).
- Apple Push Notification via `terminal-notifier` (Mac only; ephemeral).

Gmail example:
```python
import smtplib
from email.message import EmailMessage

def send_alert(failures):
    msg = EmailMessage()
    msg["From"] = os.environ["GMAIL_FROM"]
    msg["To"] = os.environ["GMAIL_TO"]
    msg["Subject"] = f"⚠ {len(failures)} cron failures detected"
    msg.set_content("\n".join(f"{name}: {reason}" for name, reason in failures))

    with smtplib.SMTP_SSL("smtp.gmail.com", 465) as s:
        s.login(os.environ["GMAIL_FROM"], os.environ["GMAIL_APP_PASS"])
        s.send_message(msg)
```

App Password (not your account password) — generate in Google Account → Security → 2FA → App Passwords.

## Step 7 — Move LLM calls to Gemini Batch

The cost savings only show up after you migrate the high-volume drafts:

- Daily 3-brand blog drafts → batch (overnight).
- Daily MLS-to-social fan-out captions → batch.
- Pinterest pin descriptions → batch.
- Quarterly postcards (when running) → batch.

Keep on live:
- `speed_to_lead.py` welcome touches (5-15 min window — too tight for batch).
- Live CMA-during-appointment.
- Any agent-driven on-dispatch call.

Live calls drop from 300/day to ~30/day after migration. Cost drops from $0.90/day to $0.45/day (batched) + $0.09/day (live remainder) = $0.54/day vs $0.90/day. ~$130/year savings on this stack alone — and the savings scale linearly with content volume.

## Step 8 — Run for 30 days, then audit

After 30 days of the day-ahead pattern running:

1. Check `~/content-agent/logs/cron.log` — should have 30 `RUN_DAILY START + FINISH` pairs.
2. Check `~/content-agent/logs/supervisor.log` — should have 360 entries (every 2h × 30 days), most empty.
3. Pull your Gemini billing — confirm batch line item is ~50% of live line item.
4. Pull your Metricool scheduled-list — confirm 30 days of content scheduled.
5. Check `~/content-agent/logs/daily_brief.log` — should have 30 daily-brief emails sent.

If all five check out, the pattern is operating cleanly. From here on, the pattern is invisible.

## What can break in production

1. **Mac vs Linux date math** — `date -v+1d` vs `date -d "tomorrow"`. Use Python for date arithmetic in production scripts.
2. **Gemini Batch API timeout** — batch jobs can take 6+ hours on busy days. If 6:15 AM collect runs and the batch isn't done, you'll have empty drafts. Mitigation: submit the batch earlier in the evening (7 PM instead of 9 PM), buffer time.
3. **App Password rotation** — Google rotates App Passwords. The supervisor sends a failed-auth alert through the alert path that's broken. Mitigation: monitor App Password validity with `credential_monitor.py` (a separate cron, fires at 7:15 AM).
4. **Family-time creep** — over months, you start running "just one more thing" at 7:15, then 7:30, then 8 AM. The window erodes. Mitigation: write the window down. Audit monthly.

## Why this skill is THE FOUNDATION skill of Season 3

The other four Season 3 cron classes (`mls-to-metricool`, `3-brand-publisher`, `speed-to-lead`, `compliance-supervisor`) all sit on top of this orchestration pattern. They're independently useful — but they compose into a working stack only if the day-ahead pattern is the substrate.

Build this first. The others slot in.

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
