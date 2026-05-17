---
name: build-your-cron-3-brand-publisher
description: Build the cron that runs one content generator and routes the output to three different brand WordPress sites + three Metricool brand IDs — same engine, three distinct voices, brand-aware dedup ledger per router. Same pattern Victoria uses for LIG + Prosperity + Victoria Author. Includes Rank Math meta routing + brand-separation guardrails.
author: Al Pinder, Victoria Pinder
version: 1.0.0
---

# Build Your Cron — One Engine, Three Brand Voices

You run more than one brand. Maybe a buyer-facing local site, an agent-recruiting site, and a personal site. Maybe a residential brand and a commercial brand. Most agents writing for multiple brands give up by Wednesday — three CMS logins, three calendars, three voice guides that drift.

This cron solves that. One Python content generator, three brand routers, three WordPress installs, three Metricool brand IDs. Same engine, different voices. Same compliance rules, different audiences.

## When to use this skill

You run 2+ brand websites and want each to ship daily content without the per-brand context-switch tax. You are comfortable enough with Python to wire three routers to one engine. You have separate Metricool brand IDs for each brand (free plan does not support multi-brand — Metricool Pro does).

## The architecture

```
run_daily.sh (6:15 AM fires)
  └── content_generator.py        (one LLM engine, brand-voice gem per brand)
       ├── greenville_blog_router.py    (LIG = livingingreenvillenc.com)
       ├── prosperity_blog_router.py    (Prosperity = theprosperityagent.com)
       └── victoria_blog_router.py      (Victoria = victoriapinder.com)

  └── publisher.py                 (routes drafts to correct WP install + Metricool)
       ├── WP_LIG          → Rank Math meta + IG/FB/Pinterest fan-out
       ├── WP_PROSPERITY   → Rank Math meta + LinkedIn fan-out (agent audience)
       └── WP_VICTORIA     → Rank Math meta + Pinterest/IG fan-out (reader audience)
```

Each router has its own dedup ledger, its own audience rule, and its own commercial intent. Same generator, same compliance overlay, three independent outputs.

## Brand separation — the locked rule (read this carefully)

In Victoria's stack, LIG and Prosperity NEVER cross-promote. LIG = Pinder Team local identity (residential RE, buyer + seller audience). Prosperity = eXp recruiting (agent audience). Cross-promotion damages both brands.

This is hard-coded in the routers:

- `greenville_blog_router.py` blocks any blog content that mentions "join eXp" or "recruit" or pivots to agent-side framing.
- `prosperity_blog_router.py` has `PROSPERITY_AUDIENCE_RULE` baked in: every blog converts agents to eXp through the Pinder Team. Competitor mentions (Compass, KW, Redfin, Real, Side, Anywhere, RE/MAX) always pivot to "why agents are moving to eXp through the Pinder Team."

You will set the same kind of rule for your brands. The router is the place to enforce it — by the time content reaches `publisher.py`, the audience pivot is locked.

## Voice gems — one per brand

Victoria's stack uses Gemini Voice Gems (trained on YouTube transcripts) for the Prosperity brand voice. Voice gem ID is `16f902e3a96d`, cached so subsequent calls don't re-train. You can use Claude system prompts, Gemini gems, or any equivalent — the key is each brand has ONE voice spec and the router pulls the right gem before the LLM call.

Voice gem structure (Prosperity example):

```
Audience: real estate agents thinking about brokerage choice.
Tone: confident, second-person, never knocks competitors by name.
Always pivots to: eXp Realty through the Pinder Team.
Bans: cold-call language, fear framing, "Claude vs ChatGPT" framing,
      any mention of internal-only tools.
Locks: NAR Code Article 16 on solicitation of competing-brokerage agents.
```

LIG voice gem looks completely different — talks to active home buyers + sellers, second-person, neighborhood-positive, Pitt-County-anchored.

## Per-brand dedup ledger

This is critical. If you share one dedup ledger across three brands, an idea on LIG blocks the same idea on Prosperity even though they're separate audiences. You'd publish 33% of what you could.

Each router has its own ledger:

```
~/content-agent/state/
  ├── lig_blog_history.json          (30-day rolling, per LIG)
  ├── prosperity_blog_history.json   (30-day rolling, per Prosperity)
  └── victoria_blog_history.json     (30-day rolling, per Victoria)
```

The router checks its own ledger before accepting a draft. Same topic can run on two brands on the same day — different audiences, different angle, no collision.

## Rank Math meta routing — the trap most agents hit

All three WP installs run Rank Math (NOT Yoast). Yoast meta keys silently rejected. You MUST use Rank Math's REST endpoint:

```
POST /wp-json/rankmath/v1/updateMeta
Body: {
  "objectType": "post",
  "objectID": <post_id>,
  "meta": {
    "rank_math_title": "<60-char title>",
    "rank_math_description": "<155-char description>"
  }
}
```

Title cap: 60 chars across ALL brands. SERP truncation hits ~60. The publisher rejects titles over 59 before push.

Always verify with a live HTML fetch — REST returns 200 even when Rank Math dropped the keys.

## The build (5 files + 1 cron line)

### File 1 — `content_generator.py` (one engine)

Drafts blog content. Loads `fair-housing-overlay`. Accepts a brand parameter. Pulls the brand's voice gem. Returns structured output.

```python
#!/usr/bin/env python3
"""
content_generator.py — one LLM engine, three brand outputs.
"""
import anthropic, os
from skill_loader import load

def generate(brand, topic, audience_rule, voice_gem):
    client = anthropic.Anthropic()
    overlay = load("fair-housing-overlay")

    sys_prompt = f"""
{overlay}

You are writing a {brand} blog post for {audience_rule}.

Voice spec:
{voice_gem}

Constraints:
- Title ≤59 chars (SERP truncation)
- Open with the lead, not a throat-clear
- Always positive
- Second-person — talk TO the reader, never ABOUT them
- Cite a real number wherever possible
"""

    resp = client.messages.create(
        model="claude-opus-4-7",
        max_tokens=2000,
        system=sys_prompt,
        messages=[{"role": "user", "content": f"Write a blog post on: {topic}"}],
    )
    return resp.content[0].text
```

### File 2 — `greenville_blog_router.py` (LIG = residential RE)

Picks today's topic, calls content_generator with the LIG voice gem, runs LIG-specific compliance check, pushes via publisher.

```python
#!/usr/bin/env python3
"""
greenville_blog_router.py — LIG (livingingreenvillenc.com).
Audience: active home buyers + sellers in Pitt County.
Daily priorities: breaking news → SEO refresh → events → 12-employer queue.
"""
import json, dt
from content_generator import generate
from publisher import push_to_wp

LIG_AUDIENCE_RULE = """
Active home buyers and sellers in Pitt County, North Carolina.
Talk TO them (second-person), never ABOUT realtors.
Pinder Team local identity — never "eXp Branch", always "Pinder Team".
"""

LIG_VOICE_GEM = "..."   # your trained gem ID or system-prompt block

def pick_today_topic():
    # Priority: breaking news → SEO refresh → events → employer queue
    return TopicPicker().lig_priority_today()

def main():
    topic = pick_today_topic()

    if blocked_by_dedup(topic, ledger="lig_blog_history.json"):
        return f"DEDUP — skipped {topic}"

    draft = generate(brand="LIG", topic=topic,
                     audience_rule=LIG_AUDIENCE_RULE,
                     voice_gem=LIG_VOICE_GEM)

    push_to_wp(brand="LIG", draft=draft)
    record_in_ledger(topic, ledger="lig_blog_history.json")
```

### File 3 — `prosperity_blog_router.py` (Prosperity = eXp recruiting)

Same shape, different audience rule and dedup ledger.

```python
PROSPERITY_AUDIENCE_RULE = """
Real estate agents considering brokerage choice.
Every blog converts agents to eXp through the Pinder Team.
Competitor mentions (Compass, KW, Redfin, Real, Side, Anywhere, RE/MAX)
always pivot to: "why agents are moving to eXp through the Pinder Team".
Never neutral comparisons — those benefit competitor SEO.
"""
```

### File 4 — `victoria_blog_router.py` (Victoria = author)

Same shape, audience = romance readers. Different topic-picker (book-launch calendar instead of MLS news).

### File 5 — `publisher.py` (the WP + Metricool fan-out)

```python
#!/usr/bin/env python3
"""
publisher.py — route drafts to correct WP + Metricool brand ID.
"""
import os, requests

WP_CONFIG = {
    "LIG": {
        "url": os.environ["WP_LIG_URL"],
        "user": os.environ["WP_LIG_USER"],
        "pass": os.environ["WP_LIG_APP_PASS"],
        "metricool_brand_id": os.environ["METRICOOL_LIG_BRAND_ID"],
    },
    "PROSPERITY": {
        "url": os.environ["WP_PROSPERITY_URL"],
        # ... etc
    },
    "VICTORIA": {
        "url": os.environ["WP_VICTORIA_URL"],
        # ... etc
    },
}

def push_to_wp(brand, draft):
    cfg = WP_CONFIG[brand]

    # 1. Title cap check (60-char hard rule)
    assert len(draft["title"]) <= 59, f"Title too long: {draft['title']}"

    # 2. Create post
    post = requests.post(
        f"{cfg['url']}/wp-json/wp/v2/posts",
        auth=(cfg["user"], cfg["pass"]),
        json={"title": draft["title"], "content": draft["body"], "status": "draft"},
    ).json()

    # 3. Set Rank Math meta
    requests.post(
        f"{cfg['url']}/wp-json/rankmath/v1/updateMeta",
        auth=(cfg["user"], cfg["pass"]),
        json={
            "objectType": "post",
            "objectID": post["id"],
            "meta": {
                "rank_math_title": draft["seo_title"],
                "rank_math_description": draft["seo_desc"],
            },
        },
    )

    # 4. Verify meta saved (REST 200 lies, fetch HTML to confirm)
    live_html = requests.get(post["link"]).text
    assert draft["seo_desc"] in live_html, "Rank Math meta dropped"

    # 5. Schedule social fan-out via Metricool
    schedule_social(brand=brand, post=post, brand_id=cfg["metricool_brand_id"])

    return post
```

## The cron line

See `cron.example` — a single line inside `run_daily.sh`.

```cron
15 6 * * * cd ~/content-agent && bash run_daily.sh >> ~/content-agent/logs/cron.log 2>&1
```

`run_daily.sh` calls `main.py` which calls all three routers in parallel.

## What this saves you

| Cost (3 brands, manual) | Cost (3 brands, this cron) |
|---|---|
| 3 × 60 min/day drafting = 180 min/day | 0 min/day (cron drafts overnight) |
| 3 × 20 min/day WP push + meta = 60 min/day | 0 min/day |
| 3 × 15 min/day social fan-out = 45 min/day | 0 min/day |
| Brand-voice drift on whichever brand you skipped this week | Zero drift (voice gems lock per-brand voice) |

Total: ~4-5 hours/day of brand-juggling reduced to a daily-brief read.

## What you can't fudge

The voice gems. If you skip building the per-brand voice spec and just prompt "write a Prosperity blog", the output reads like generic marketing copy. The gems are what make three brands sound like three different humans.

Build the gems once, refresh quarterly. Trained on transcripts from your real conversations (YouTube uploads, podcast episodes, client meetings), not on aspirational copy.

## Companion content

YouTube Episode 11: `Build Your Cron — One Engine, Three Brand Voices` — Al + Victoria walk through their actual three-router stack live, including the Prosperity audience-rule lock and the LIG dedup-ledger separation.

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
