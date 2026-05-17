---
name: build-your-cron-day-ahead-pattern
description: The universal orchestration pattern behind every other cron in this season — eve prep drafts overnight (50% cheaper via Gemini Batch API), morning collect at 6:15 AM schedules TOMORROW's content. Plus the every-2-hour supervisor.supervisor that watches every other cron. Family-time-protected 5:45-7:30 AM window locked.
author: Al Pinder, Victoria Pinder
version: 1.0.0
---

# Build Your Cron — Day-Ahead Orchestration Pattern

The universal pattern behind every other cron in this season. If you understand this one, the other four make sense automatically.

The pattern: evening drafts overnight (cheap, no time pressure), morning collects and schedules TOMORROW's content (5-10 min of work before breakfast), `supervisor.supervisor` runs every 2 hours and verifies real output.

By 6:30 AM your entire next day is queued. By 7 AM you're at breakfast.

## When to use this skill

You have at least two crons running and you're starting to feel the morning crunch. You want a master orchestration pattern that protects family time, halves your LLM API bill, and gives you a watchdog that confirms everything actually ran.

You're comfortable enough with cron to chain 4-5 entries together. You have an evening API (Gemini Batch or equivalent) that runs cheaper than live calls.

## The pattern

```
EVENING PREP (the day before)
  19:00 — refresh_gems.py             (Gemini Voice Gems refresh)
  19:45 — batch_content_submit.py     (Gemini Batch API → 50% cheaper)
  20:15 — heygen_daily_batch.py       (HeyGen renders overnight)

MORNING COLLECT + SCHEDULE TOMORROW (sit-down at 5:45, finish by 7)
  05:45 — seo_pulse_reader.py         (GSC live alert)
  06:00 — heygen_check_completed.py   (overnight HeyGen → Metricool tomorrow)
  06:15 — run_daily.sh (MASTER)       (collects evening drafts, schedules tomorrow)
  06:20 — agent15_ops_monitor.py
  06:30 — google_search_console.py + youtube_index.py
  06:45 — youtube_social_share.py
  06:50 — schedule_5pm_shorts.py + schedule_prosperity_5pm.py
  06:55 — listing_phase_scanner.py
  07:00 — pinterest_youtube_catchup.py + agent12_analytics.py daily
  07:15 — credential_monitor.py
  07:30 — social_engagement_agent.py daily + reckless_campaign.py

CROSS-CRON SUPERVISOR (every 2 hours, all day)
  */120 — supervisor.supervisor       (verifies every other cron's real output)

SAFETY-NET POLLS (across the day)
  10:00 + 14:00 + 18:00 — heygen_check_completed.py (catch late renders)
  08:30 + 16:00 — metricool_image_sweeper.py --apply
  11:00 — compliance_supervisor.py
```

By 7:30 AM, the whole next 24 hours are scheduled, supervised, and audit-trailed.

## The 50% cost saving — Gemini Batch API

This is the single biggest cost lever. Live LLM API calls (Anthropic, OpenAI, Gemini live) charge full price for sub-second response. Gemini Batch API processes overnight and charges 50% of the per-token rate.

The trade-off: response time is 2-6 hours instead of seconds. For content drafted overnight for tomorrow, the slow response is irrelevant — by morning the drafts are done.

```python
# batch_content_submit.py — submits at 7:45 PM, picks up at 6:15 AM
import google.generativeai as genai

genai.configure(api_key=os.environ["GEMINI_API_KEY"])

# Submit tonight's batch
batch_job = genai.batch.create(
    requests=[
        {"model": "gemini-2.0-flash", "contents": [{"role": "user", "parts": [{"text": prompt}]}]}
        for prompt in tomorrows_prompts
    ],
    output_uri="gs://your-bucket/batch-out/",
)
print(f"Submitted batch {batch_job.name}")
```

Morning collect:
```python
# run_daily.sh calls this first
import google.generativeai as genai

# Pull last night's results
results = genai.batch.get_results(last_night_batch_id)
for r in results:
    write_draft_to_queue(r.content)
```

For ~300 LLM calls/day at $0.003 average per call, that's $900/year live → $450/year batch. Cheaper compounds when you scale to multiple brands.

What stays on the live API (you can't batch these):
- `speed_to_lead.py` — Day 0 must respond in 5-15 min, batch is too slow.
- Real-time CMA narratives during a kitchen-table appointment.
- Any agent-driven on-dispatch request.

What moves to batch:
- Daily 3-brand blog drafts.
- Daily MLS-to-social fan-out captions.
- Pinterest pin descriptions for the YT backlog catchup.
- Quarterly postcard drafts.

## The family-time-protected window — LOCKED

Victoria's morning: 5:45 = sit down, 6:15 = `run_daily.sh` fires, 7:00 = breakfast.

New crons fit INSIDE the 5:45-7:30 AM window OR fire BEFORE 5:45. Never propose a 7-9 AM "work" slot — that's family time.

This is not aesthetic preference. The cron pattern only works if the morning is collect-and-schedule, not generate-and-stress. If you put LLM-heavy generation in the morning slot, you'll hit rate limits AND be on the laptop during breakfast.

Your morning may shift earlier or later — pick your window and protect it. The pattern adjusts.

## The cross-cron supervisor — `supervisor.supervisor`

This is the one piece most agents skip on first build. Don't.

Every 2 hours, `supervisor.supervisor` runs across every other cron and verifies:

- `run_daily.sh` produced a finished log entry within the last 24 hours.
- `metricool_image_sweeper.py` ran twice today.
- `speed_to_lead.py` log has at least one tick in the last 10 minutes (cron is alive).
- `compliance_supervisor.py` produced a `passed_N_blocked_M` line yesterday.
- `heygen_check_completed.py` polled at least 4 times today.

If any cron is missing its expected output, immediate Gmail alert with the specific cron name and the expected vs actual.

Silent unless something is broken. The whole point of running 15 crons is that you don't need to babysit them — but if one silently dies, you need to know within hours, not weeks.

```python
#!/usr/bin/env python3
"""
supervisor.supervisor — runs every 2 hours.
Verifies real output across all pipelines. Alerts on missing output.
"""
import os, smtplib, datetime as dt
from email.message import EmailMessage

CHECKS = {
    "run_daily.sh": {
        "log": "~/content-agent/logs/cron.log",
        "expected_pattern": r"RUN_DAILY FINISH",
        "expected_within_hours": 24,
    },
    "metricool_image_sweeper.py": {
        "log": "~/content-agent/logs/sweeper.log",
        "expected_count_today": 2,
    },
    "speed_to_lead.py": {
        "log": "~/content-agent/logs/speed_to_lead.log",
        # silent on most ticks, just verify the cron file isn't empty for >12h
    },
    "compliance_supervisor.py": {
        "log": "~/content-agent/logs/compliance.log",
        "expected_pattern": r"\d+ passed, \d+ blocked",
        "expected_within_hours": 24,
    },
    "heygen_check_completed.py": {
        "log": "~/content-agent/logs/heygen.log",
        "expected_count_today": 4,
    },
}

def main():
    failures = []
    for name, spec in CHECKS.items():
        passed, reason = run_check(name, spec)
        if not passed:
            failures.append((name, reason))

    if failures:
        send_alert(failures)
    # else: silent

if __name__ == "__main__":
    main()
```

## The build (the master orchestrator + the supervisor)

### `run_daily.sh` (the 6:15 AM master)

```bash
#!/bin/bash
# run_daily.sh — the master cron.
set -a
. ~/content-agent/.env
set +a
cd ~/content-agent

TOMORROW=$(date -v+1d "+%Y-%m-%d")   # Mac. Use date -d "tomorrow" on Linux.
echo "[$(date)] === RUN_DAILY START — target=$TOMORROW ==="

# 0. Collect last night's Gemini Batch API output (50% cheaper)
python3 batch_content_submit.py --collect || echo "Batch collect failed, continuing"

# 1. LLM-draft 3-brand content for tomorrow
python3 main.py --target-date "$TOMORROW" || echo "Main failed, continuing"

# 2. Post-mining + agent10 daily ops
python3 post_mining_sheet.py || echo "Mining failed, continuing"
python3 mining_agent.py || echo "Mining agent failed, continuing"

# 3. Daily brief email
python3 daily_brief.py

echo "[$(date)] === RUN_DAILY FINISH ==="
```

`set -e` is INTENTIONALLY NOT used. One cron failure should not kill the morning sequence — log + continue.

## The cron lines

See `cron.example` for the complete morning + evening + supervisor chain.

## What this saves you

| Without day-ahead | With day-ahead |
|---|---|
| Morning content generation = 60-90 min/day stressed | 5-10 min/day collect + spot-check |
| Live API cost = $900/year | Batch API cost = $450/year |
| Cron silently dies, found 2 weeks later | Supervisor catches within 2 hours |
| 7-9 AM laptop time bleeds into family time | 7-9 AM is family time, locked |

## Companion content

YouTube Episode 14: `Build Your Cron — Day-Ahead Orchestration Pattern` — Al + Victoria show the actual evening → morning → supervisor chain on Victoria's machine, including the day `supervisor.supervisor` caught a dead Metricool API token.

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
