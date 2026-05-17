---
name: build-your-cron-daily-mls-to-metricool
description: Build your own daily cron that takes your MLS pull + your blog draft and fans it out to Metricool — 4 platforms per brand — without you babysitting the schedule. Same pattern Victoria runs at 6:15 AM. Includes Metricool dedup, twice-daily image-sweeper safety net, day-ahead scheduling. Pairs mls-to-social + canva-real-estate-lifecycle + Metricool MCP.
author: Al Pinder, Victoria Pinder
version: 1.0.0
---

# Build Your Cron — Daily MLS → Metricool Social Pack

This is one of the five cron-class skills. You are not buying a SaaS. You are not paying a VA. You are installing a cron on your own machine that publishes your daily MLS-driven content across 4 social platforms per brand, every day, before you sit down.

Same pattern Victoria runs at 6:15 AM. Same one-master-cron orchestrating 10 sub-workflows. Same Metricool fan-out hub that 18 of her scripts push into.

## When to use this skill

You have a daily MLS pull. You have a blog you write (or your blog router writes for you). You want both to fan out to Instagram + Facebook + Pinterest + LinkedIn per brand every day — without you logging into Metricool four times to upload, schedule, and re-check.

You are an agent comfortable with a terminal. You can run a Python script. You know what a cron entry looks like (or you can copy-paste one).

## What this cron does

```
6:15 AM (the master cron fires)
  └── run_daily.sh
       ├── batch_content_submit.py --collect     (pulls overnight Gemini Batch API drafts)
       ├── main.py --target-date $TOMORROW       (LLM-drafts 3-brand content)
       │    └── publisher.py                     (routes drafts to correct WP + Metricool)
       │         ├── greenville_blog_router.py   (LIG priorities)
       │         ├── prosperity_blog_router.py   (Prosperity)
       │         └── victoria_blog_router.py     (Victoria author)
       ├── post_mining_sheet.py
       ├── mining_agent.py
       └── daily_brief.py                        (email summary at sunrise)

8:30 AM + 4 PM (twice-daily image-sweeper safety net)
  └── metricool_image_sweeper.py --apply
       (normalizes images Metricool held due to size or aspect-ratio)
```

By the time you sit down with coffee, the entire next day is scheduled in Metricool. By breakfast, the daily brief email has hit your inbox with what ran, what failed, and what needs your eyes.

## Where Metricool fits

Metricool is your central fan-out hub. One blog post fans out to:

- Instagram (square 1080×1080)
- Facebook (landscape 1200×630)
- Pinterest (vertical pin 1000×1500)
- LinkedIn (rectangle 1200×627)

Per brand. Same hub.

In Victoria's stack, 18+ scripts push into Metricool every day — `publisher.py`, `heygen_check_completed.py`, `schedule_5pm_shorts.py`, `schedule_prosperity_5pm.py`, `youtube_social_share.py`, `pinterest_youtube_catchup.py`, `schedule_linkedin_*.py`, `reckless_campaign.py`, `secret_enemy_campaign.py`, `render_pitt_carousel_gamma.py`, `metricool_image_sweeper.py`. Each one calls into `metricool_dedup.py` so slots don't bunch.

You are building a tiny version of that. One brand, one fan-out, one cron line.

## Prerequisites

1. A machine that runs cron (Mac with crontab or a small Linux box).
2. Python 3.11+ installed.
3. Metricool Pro account + API token (free plan does not include API).
4. WordPress site with Rank Math installed (LIG uses Rank Math, NOT Yoast — POST `/wp-json/rankmath/v1/updateMeta` is the endpoint).
5. Your MLS data source (NCRMLS CSV export or whichever export your MLS gives you).
6. `fair-housing-overlay` skill loaded so every generated post is FH-clean.
7. `canva-real-estate-lifecycle` skill loaded for design briefs.
8. `mls-to-social` skill loaded for the 5-platform caption generation.

## The build (4 files + 1 cron line)

You build four Python files. Each is small. The whole stack is under 400 lines.

### File 1 — `mls_to_metricool.py` (the main scheduler)

```python
#!/usr/bin/env python3
"""
mls_to_metricool.py — daily MLS pull → 4-platform Metricool schedule.
Runs at 6:15 AM from run_daily.sh, schedules TOMORROW's posts.
Loads fair-housing-overlay + mls-to-social skills before generating.
"""
import os, sys, datetime as dt
from pathlib import Path

import metricool_dedup       # YOUR module — prevents slot-bunching
import metricool_client       # YOUR module — wraps Metricool API
import skill_loader           # YOUR module — loads claude skills programmatically

def main(target_date):
    # 1. Pull today's MLS data (CSV export or NCRMLS API)
    mls = load_mls_export(target_date - dt.timedelta(days=1))  # last close

    # 2. Generate 5 platform posts via mls-to-social skill
    posts = skill_loader.run(
        skill="mls-to-social",
        with_overlay="fair-housing-overlay",
        inputs={"mls_data": mls, "city": "Greenville", "audience": "first-time"},
    )

    # 3. Dedup check before scheduling
    slots = metricool_dedup.next_open_slots(target_date, n=4)

    # 4. Push to Metricool, 4 platforms, target_date
    for platform, post, slot in zip(["instagram", "facebook", "pinterest", "linkedin"], posts, slots):
        metricool_client.schedule(platform=platform, content=post, when=slot)

    print(f"[{dt.datetime.now()}] Scheduled 4 platforms for {target_date}")

if __name__ == "__main__":
    tomorrow = dt.date.today() + dt.timedelta(days=1)
    main(tomorrow)
```

### File 2 — `metricool_dedup.py` (the slot-bunching prevention)

This is the single most important module in the stack. Without it, two posts can land in the same 30-minute slot and Facebook downranks them both.

```python
#!/usr/bin/env python3
"""
metricool_dedup.py — returns next N open Metricool slots, spaced ≥90 min apart.
Imported by every scheduler in the stack.
"""
import datetime as dt
import metricool_client

def next_open_slots(target_date, n=4, min_gap_minutes=90):
    # Pull already-scheduled posts for target_date from Metricool API
    scheduled = metricool_client.list_scheduled(target_date)
    occupied = {s.timestamp for s in scheduled}

    # Time slots: 9 AM, 11 AM, 1 PM, 3 PM, 5 PM (anchored to America/New_York)
    candidates = [
        dt.datetime.combine(target_date, dt.time(h, 0))
        for h in (9, 11, 13, 15, 17)
    ]

    # Filter out occupied + adjacent
    open_slots = []
    for c in candidates:
        if not any(abs((c - o).total_seconds()) < min_gap_minutes * 60 for o in occupied):
            open_slots.append(c)
        if len(open_slots) == n:
            break

    return open_slots
```

### File 3 — `metricool_image_sweeper.py` (twice-daily safety net)

Runs at 8:30 AM and 4 PM. If Metricool held a post because the image dimensions didn't match the platform spec, this sweeps it.

```python
#!/usr/bin/env python3
"""
metricool_image_sweeper.py --apply
Normalizes images Metricool held due to size/aspect-ratio issues.
Runs twice daily (8:30 + 16:00) as a safety net.
"""
import sys
import metricool_client
import image_utils  # YOUR thin PIL wrapper

def sweep(apply=False):
    held = metricool_client.list_held()  # API call returns held-due-to-image posts
    for post in held:
        normalized = image_utils.normalize_to_platform(post.image, post.platform)
        if apply:
            metricool_client.replace_image(post.id, normalized)
            print(f"Normalized {post.id}")
        else:
            print(f"[dry-run] Would normalize {post.id}")

if __name__ == "__main__":
    sweep(apply="--apply" in sys.argv)
```

### File 4 — `run_daily.sh` (the master orchestrator)

```bash
#!/bin/bash
# run_daily.sh — fires at 6:15 AM. Orchestrates the morning fan-out.
# Source the .env so MLS_API_KEY, METRICOOL_API_TOKEN, etc. are in scope.
set -a
. ~/content-agent/.env
set +a

cd ~/content-agent

TOMORROW=$(date -v+1d "+%Y-%m-%d")   # Mac. Use date -d "tomorrow" on Linux.

echo "[$(date)] === RUN_DAILY START — target=$TOMORROW ==="

# 1. Collect overnight Gemini Batch API drafts (cheaper than live)
python3 batch_content_submit.py --collect

# 2. LLM-draft 3-brand content for tomorrow
python3 main.py --target-date "$TOMORROW"

# 3. Fan out today's MLS → 4 platforms
python3 mls_to_metricool.py

# 4. Email the daily brief
python3 daily_brief.py

echo "[$(date)] === RUN_DAILY FINISH ==="
```

`chmod +x run_daily.sh` and you are done with the build.

## The cron line

See `cron.example` in this folder for the exact format. Four entries total:

```cron
# Master morning fan-out
15 6 * * * cd ~/content-agent && bash run_daily.sh >> ~/content-agent/logs/cron.log 2>&1

# Twice-daily image-sweeper safety net
30 8 * * * cd ~/content-agent && python3 metricool_image_sweeper.py --apply >> ~/content-agent/logs/sweeper.log 2>&1
30 16 * * * cd ~/content-agent && python3 metricool_image_sweeper.py --apply >> ~/content-agent/logs/sweeper.log 2>&1

# Optional: HeyGen overnight render check (if you're running daily avatar videos)
0 6 * * * cd ~/content-agent && python3 heygen_check_completed.py >> ~/content-agent/logs/heygen.log 2>&1
0 10,14,18 * * * cd ~/content-agent && python3 heygen_check_completed.py >> ~/content-agent/logs/heygen.log 2>&1
```

Paste these into `crontab -e`. Save. Done.

## Day-ahead scheduling — read this carefully

Notice `--target-date $TOMORROW` in `run_daily.sh`. You are scheduling tomorrow's posts at 6:15 AM today, not today's posts. This is the day-ahead pattern (see `build-your-cron-day-ahead-pattern` skill for the full explanation).

Why this matters: if you schedule today's posts at 6:15 AM today, your 9 AM IG post is being drafted 2 hours and 45 minutes before it fires. You will be tempted to refine, edit, second-guess. The day-ahead pattern gives every post 24+ hours of marination — by the time it fires, it's been through overnight Gemini Batch API drafting (50% cheaper), your morning review, and the dedup gate.

## What this saves you

| Manual cost (per day) | Cron cost (per day) |
|---|---|
| 30-60 min logging into Metricool to upload + schedule | 0 min (you read the daily brief email) |
| 15-20 min rewriting captions per platform | 0 min (mls-to-social drafts all 5) |
| 5-10 min re-cropping Canva exports | 0 min (image-sweeper handles aspect ratios) |
| 5 min checking for missed posts at 5 PM | 0 min (dedup keeps slots spaced) |

Total: ~60-90 min of daily friction reduced to a 30-second daily-brief read.

## Compliance — what runs alongside this cron

`fair-housing-overlay` is loaded by every skill in the chain. `compliance_supervisor.py` runs at 11 AM and verifies every Metricool-bound post passed 14 checks (see `build-your-cron-compliance-supervisor` skill). `supervisor.supervisor` runs every 2 hours and confirms the morning fan-out actually landed.

You are not running this on faith. You have three independent verifiers watching the pipeline.

## Companion content

YouTube Episode 10: `Build Your Cron — Daily MLS to Metricool Social Pack` — Al + Victoria walk through their actual `run_daily.sh` on camera, including the day Metricool's API broke and the image-sweeper saved the morning post.

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
