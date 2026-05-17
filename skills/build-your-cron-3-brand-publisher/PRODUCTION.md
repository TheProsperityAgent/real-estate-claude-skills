# PRODUCTION — build-your-cron-3-brand-publisher

The walkthrough for wiring three brand routers to one content engine and shipping daily. Real flow, no theory.

## What you need before you start

- 2+ brand websites running WordPress + Rank Math (Yoast won't work — see the trap section in SKILL.md).
- Application Passwords enabled in WordPress (Users → your-user → Application Passwords). Generate one per brand.
- Metricool Pro with each brand set up as a distinct brand profile. Note each brand's ID — you'll need them.
- Anthropic API key OR Gemini API key (Gemini Batch API is 50% cheaper for non-time-sensitive overnight drafts — see `build-your-cron-day-ahead-pattern`).
- Voice spec per brand. If you have YouTube uploads or recorded calls, transcribe and train a Gemini Gem on them. If you're starting from scratch, write a 200-word voice doc per brand by hand — what audience, what tone, what banned phrases, what locked pivots.

## Step 1 — Map your brand stack

Write out your brand table before you write any code. This is what Victoria's looks like:

| Brand | Site | Audience | Locked pivot | Voice gem |
|---|---|---|---|---|
| LIG | livingingreenvillenc.com | Pitt County home buyers + sellers | Pinder Team identity (NEVER eXp Branch) | `lig_voice_v3` |
| Prosperity | theprosperityagent.com | Real estate agents | eXp Realty via the Pinder Team | `16f902e3a96d` |
| Victoria | victoriapinder.com | Romance readers | Series + book launches | `victoria_voice_v2` |

Your three brands will be different — but each row needs all five columns filled before you start coding the router. If any row has a blank, the router will leak.

## Step 2 — Build the voice gems

For each brand, build a 200-500 word voice spec that includes:

1. **Audience** — who they are, second-person address rules.
2. **Tone** — 3 adjectives + 1 example sentence.
3. **Locked pivots** — when X comes up, always land on Y.
4. **Banned phrases** — what the brand never says.
5. **Brand-specific FH/TCPA/NAR rules** — over and above `fair-housing-overlay`.

Example (Prosperity excerpt):

```
Audience: Real estate agents considering brokerage choice. Talk TO them
(second-person), never about agents in third-person.

Tone: confident, calm, never desperate. Three adjectives — direct, useful,
generous.

Locked pivots:
- Competitor mention (Compass / KW / Redfin / Real / Side / Anywhere / RE/MAX)
  → "why agents are moving to eXp through the Pinder Team".
- "Should I switch brokerages" → eXp ICON track + Pinder Team sponsorship.
- Industry news → eXp lens, Al's real-story angles.

Banned phrases:
- "Claude vs ChatGPT" framing — chat tools are great, Code does different work.
- "internal-only paid tool" — internal-only, never appears in public content.
- Cold-call language.
- Fear framing ("if you don't switch, you'll lose...").

NAR locks:
- Article 16 on solicitation — never name an agent at a competing brokerage
  as a recruiting target in public content.
```

Save each voice doc as `~/content-agent/voice/<brand>.md`. The router loads it before each generation call.

## Step 3 — Build one router as a template, copy to the others

Start with one brand's router (Victoria built LIG first). Get it working end-to-end:

1. Topic picker reads from the brand's queue file.
2. Dedup ledger blocks recent topics.
3. Content generator returns draft with `fair-housing-overlay` loaded.
4. Brand-specific compliance check (e.g., "if Prosperity, must land on eXp pivot").
5. Publisher pushes to the brand's WP install with Rank Math meta.
6. Social fan-out scheduled to brand's Metricool ID.

Once that single brand ships clean for 3 consecutive days, copy the router file as a template for brands 2 and 3. Change:
- The audience rule.
- The voice gem reference.
- The dedup ledger filename.
- The WP credentials reference.
- The Metricool brand ID.
- The compliance pivot rules.

The shape stays identical. Cloning a working router is 30 minutes; building each from scratch is days.

## Step 4 — Wire all three into `run_daily.sh`

```bash
#!/bin/bash
# run_daily.sh — daily 3-brand morning fan-out.
set -a
. ~/content-agent/.env
set +a
cd ~/content-agent

TOMORROW=$(date -v+1d "+%Y-%m-%d")

echo "[$(date)] === 3-BRAND PUBLISH START — target=$TOMORROW ==="

python3 greenville_blog_router.py --target-date "$TOMORROW" >> logs/lig.log 2>&1
python3 prosperity_blog_router.py --target-date "$TOMORROW" >> logs/prosperity.log 2>&1
python3 victoria_blog_router.py --target-date "$TOMORROW" >> logs/victoria.log 2>&1

python3 daily_brief.py >> logs/daily_brief.log 2>&1

echo "[$(date)] === 3-BRAND PUBLISH FINISH ==="
```

Three routers run sequentially. Each writes its own log. The daily brief at the end summarizes across all three.

Run by hand once: `bash run_daily.sh`. Verify all three log files have a clean run. Open each brand's WP admin and see the drafted post. Open each brand's Metricool and see the scheduled fan-out.

## Step 5 — Install the cron

```cron
15 6 * * * cd ~/content-agent && bash run_daily.sh >> ~/content-agent/logs/cron.log 2>&1
```

Single line. The script handles the three-brand routing internally.

## Cross-brand sanity checks (run weekly)

Once a week, run a script that diffs the three brands' published content for cross-promotion leaks:

```python
#!/usr/bin/env python3
# weekly_brand_separation_check.py
# Flags posts where LIG content mentions Prosperity, or vice versa.

import requests, re

LEAK_PATTERNS = {
    "lig_leaks_prosperity": [r"\beXp\s+Branch\b", r"join\s+exp", r"recruit"],
    "prosperity_leaks_lig": [r"\bLIG\b", r"livingingreenvillenc"],
}

for brand, patterns in LEAK_PATTERNS.items():
    posts = fetch_last_7_days(brand=brand.split("_")[0])
    for post in posts:
        for p in patterns:
            if re.search(p, post["content"], re.I):
                alert(f"LEAK: {brand} on post {post['id']} — matched /{p}/")
```

Run Fridays. Brand drift is gradual. The check catches it before it compounds.

## What can break in production (read fast)

1. **Voice gem returns garbage** — gem expired or context window blew. Refresh gem from training data.
2. **Two brands draft the same headline** — your topic picker fed the same priority to both. Differentiate by audience first.
3. **Rank Math meta lands on the wrong post ID** — publisher created the post but the meta call referenced a stale ID. Always use the post ID from the previous response in the same chain.
4. **Metricool brand ID swap** — you scheduled the LIG post to Prosperity's Metricool. Hard to detect because both succeed. Mitigation: log every Metricool API call with both the brand name AND the brand ID, alert if they mismatch the expected mapping.

## What you'll learn after 30 days

You'll find one brand naturally ships easier than the others. For Victoria that's LIG (residential RE has the biggest topic surface). For you it might be the one with the clearest audience.

The brand that's hardest to ship daily is usually the one with the weakest voice gem. Spend an afternoon strengthening that gem and the cron starts producing publishable drafts on the first try instead of needing rewrites.

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
