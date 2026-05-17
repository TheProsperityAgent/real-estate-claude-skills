# PRODUCTION — build-your-cron-daily-mls-to-metricool

The walkthrough for shipping this cron on your machine. Not a tutorial — a workflow you can paste in.

## What you need before you start

- Mac (or Linux) with crontab access. `crontab -l` should return without error.
- Python 3.11+. Verify with `python3 --version`.
- A Metricool Pro account + API token. Generate in Metricool → Settings → API.
- WordPress site running Rank Math (Rank Math SEO + Rank Math SEO PRO if you have both, but standard Rank Math is enough). Verify by visiting `yoursite.com/wp-json/rankmath/v1/` — you should see a JSON response listing endpoints. If you see 404, you are on Yoast or no SEO plugin and need to install Rank Math first.
- Your MLS export source. Most regional MLS systems offer a daily CSV export by email. Save it to `~/content-agent/mls/`.

## Step 1 — Pull this skills repo

```bash
git clone https://github.com/theprosperityagent/real-estate-claude-skills.git ~/real-estate-claude-skills
cd ~/real-estate-claude-skills
```

## Step 2 — Set up the `.env` file

```bash
mkdir -p ~/content-agent
cd ~/content-agent
touch .env
chmod 600 .env   # owner-only read/write
```

Inside `.env`:

```
ANTHROPIC_API_KEY=sk-ant-...
METRICOOL_API_TOKEN=...
METRICOOL_BLOG_ID=...
METRICOOL_BRAND_ID=...
WP_LIG_URL=https://livingingreenvillenc.com
WP_LIG_USER=al
WP_LIG_APP_PASS=...
RANKMATH_ENDPOINT=/wp-json/rankmath/v1/updateMeta
GEMINI_API_KEY=...   # optional, for Gemini Batch API 50% discount on overnight drafts
```

Never commit `.env`. Already in `.gitignore` of the parent repo.

## Step 3 — Build the four files

The four files (`mls_to_metricool.py`, `metricool_dedup.py`, `metricool_image_sweeper.py`, `run_daily.sh`) are in `SKILL.md`. Copy-paste them into `~/content-agent/`. Adjust the platform list, slot times, and brand IDs to your account.

Test each one in isolation before chaining:

```bash
cd ~/content-agent
python3 -c "import metricool_client; print(metricool_client.health_check())"
python3 metricool_dedup.py     # should print next 4 open slots for tomorrow
python3 metricool_image_sweeper.py    # dry-run by default
python3 mls_to_metricool.py    # should schedule 4 posts for tomorrow
```

## Step 4 — Test `run_daily.sh` once by hand

```bash
cd ~/content-agent
bash run_daily.sh
```

If it runs clean to "RUN_DAILY FINISH", you are ready for cron. If anything errors, fix the script and re-run by hand — never debug a script for the first time inside cron, you lose the error context.

## Step 5 — Install the cron entries

```bash
crontab -e
```

Paste the four entries from `cron.example`. Save and exit. Verify with:

```bash
crontab -l
```

You should see your entries. Cron will fire at 6:15 AM tomorrow.

## Step 6 — Wait 24 hours, then check

The morning after install:

1. Check `~/content-agent/logs/cron.log` — should have a `RUN_DAILY START` and `RUN_DAILY FINISH` line stamped at 6:15-6:30 AM.
2. Open Metricool. The next-day queue should have 4 scheduled posts (IG / FB / Pinterest / LinkedIn).
3. Read the `daily_brief.py` email that hit your inbox — should summarize what ran, what was scheduled, what failed.

If all three check out, the cron is live. You are done.

## Cron timing — the 5:45-7:30 AM window matters

Victoria's morning window is locked: 5:45 AM = sit down, 6:15 AM = `run_daily.sh` fires, 7:00 AM = breakfast. New crons fit INSIDE 5:45-7:30 AM OR fire before 5:45.

Why this is non-negotiable: she is a mom. Cron work that bleeds into 7-9 AM bleeds into family time. Your morning may be different — but pick a window and protect it. Once the cron is live, you should not be looking at it during the window. The whole point is that the cron does the work while you do family + coffee.

Acceptable substitutes if 6:15 doesn't work for you: 5:00 AM (early-riser), 6:30 AM (slightly-later but still pre-breakfast). Avoid 7-9 AM (work-time conflict) and avoid evening (the day-ahead pattern only works if drafting happens 12+ hours before publish).

## What to do when it breaks

The cron will break. The MLS export will be late, Metricool's API will throttle, Rank Math will reject a meta key, Gemini will rate-limit. This is normal.

Three things save you:

1. **`supervisor.supervisor` every 2 hours** — verifies the morning fan-out actually landed (`build-your-cron-day-ahead-pattern` covers this). If it didn't, you get a Gmail alert.
2. **`metricool_image_sweeper.py` twice daily** — catches images Metricool held due to dimension issues.
3. **`compliance_supervisor.py` 11 AM** — verifies every Metricool-bound post passed FH/TCPA/MLS checks. Holds bad posts in `/content/blocked/`.

If you only have time for one safety net, install the supervisor. If you have time for two, install the supervisor + the image-sweeper. Add the compliance supervisor as soon as you scale past 5 posts/day.

## The "I shipped this" milestone

The day you sit down at 6:30 AM with coffee, open Metricool, and see 4 posts already scheduled across 4 platforms — without you touching anything — that is the milestone. From here on, the work is in the captions you draft once and the templates you build once, not the daily click-click-upload.

Most agents who install this cron report ~10 hours/week back. Some report more.

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
