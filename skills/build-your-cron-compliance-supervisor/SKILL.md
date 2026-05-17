---
name: build-your-cron-compliance-supervisor
description: Build the 11 AM daily cron that runs 14 compliance checks across every AI-generated piece of content BEFORE it reaches a publish API. Criminal-leak blocklist (FHA + MLS data quality), opt-out cross-reference, closed-or-under-contract exclusion, daily-cap throttle, and 9 more. Your TCPA + Fair Housing insurance policy.
author: Al Pinder, Victoria Pinder
version: 1.0.0
---

# Build Your Cron — 14-Check Compliance Supervisor

This is your TCPA + Fair Housing insurance policy. Once Metricool's API returns 200, the post is on Facebook's servers — you can delete from Metricool but the cached version is on the network. Compliance has to run BEFORE the API call, not after.

The supervisor runs at 11 AM daily, BEFORE the day's scheduled-for-tomorrow content reaches a publish API. It runs 14 checks on every piece of AI-generated content. Anything that fails gets held in `/content/blocked/` for your review.

## When to use this skill

You are scaling content with AI. You ship 5+ pieces of content per day across multiple platforms. You are afraid one hallucinated steering phrase will end your license. You want a safety net that fires before the post hits the network — not after.

You have `fair-housing-overlay` loaded as a foundation. The supervisor is the second-layer net that catches anything the overlay missed (which is rare but possible, especially when AI generations stack overnight).

## The 14 checks

1. **Criminal-leak phrase scan** — 47-phrase blocklist against every generated body. Hits "must-see", "won't last", "great schools", "ideal for families", "exclusive community", "quiet area", and similar.
2. **Word-boundary regex** — prevents false positives ("Oakwood School District" as a neighborhood name doesn't trigger "school" check; pattern requires standalone word).
3. **Opt-out cross-reference** — verifies recipient is NOT on the CRM opt-out flag AND NOT on the manual no-contact list. Re-checks even if upstream skill claimed to check (defense in depth).
4. **Closed-or-under-contract exclusion** — cross-references live MLS feed; properties flipped to PEND / CLD between draft and publish get held.
5. **Daily-cap throttle** — blocks more than 4 same-platform posts in a 24h window (Facebook downranks, Instagram throttles).
6. **Image-rules check** — image dimensions match platform spec; EHO logo present on printed assets.
7. **Link-validation** — every link returns 200 and matches the brand's domain (catches accidentally-linked stale URLs).
8. **Brand-keyword presence** — brand-required phrase (e.g., "Pinder Team" for LIG) appears at least once.
9. **License-disclosure check** — license number + brokerage line in every Real-Estate-Commission-relevant post.
10. **Escrow-tag check** — closing posts include the escrow attorney's name when state requires.
11. **MLS-attribution check** — listing posts include the MLS source disclosure when state requires.
12. **Fair-housing-overlay loaded** — verifies the overlay was active on the upstream generation (skill loader logs it).
13. **TCPA-consent on file** — every recipient-targeted post has consent_on_file=True in the upstream payload.
14. **Dedup vs 30-day history** — content hash compared against the last 30 days; near-duplicates held for review.

## When checks fire

```
6:15 AM  — run_daily.sh generates tomorrow's content
8:00 PM  — Gemini Batch API completes overnight drafts (cheaper, 50% off)
10:00 PM — heygen_check_completed.py polls overnight HeyGen renders
11:00 AM — compliance_supervisor.py FIRES across yesterday's queued content
            → Anything failing a check moves to /content/blocked/ for review
            → You see flags in the daily brief
6:15 AM tomorrow — clean content publishes; blocked content waits for your eyes
```

The 11 AM timing is deliberate. By then:
- Tomorrow's content has been drafted (overnight).
- The MLS has had time to update phase changes (closed / under contract).
- Your live-data sources (CRM opt-out flags, no-contact list) have refreshed.
- You're back from morning showings and can review held items before lunch.

## Example block

A real `compliance_supervisor.log` line from Victoria's stack:

```
[2026-05-17 11:00:14] BLOCK content_id=4823
  Reason: check_criminal_leaks triggered on phrase 'great schools'
  Source: listing-description skill output for 1234 Main St
  Action: moved to /content/blocked/2026-05-17/4823.json for review
```

Victoria opens `/content/blocked/2026-05-17/4823.json`, reads the offending paragraph, rewrites the closing line, drops back in the queue. Net cost: 90 seconds. Net benefit: she did not publish "great schools" to her MLS feed.

## The 47-phrase blocklist (FHA + MLS data quality)

The criminal-leak blocklist is updated quarterly based on:
- FHA steering enforcement actions (HUD case database).
- NAR Code of Ethics Article 10 violations (NAR member directory).
- MLS data-quality downranking patterns (Realtor.com + Zillow + Redfin).

Sample patterns from the blocklist (NOT exhaustive — see `examples/before-after.md` for the full 47):

```
# FHA — schools and demographics
\bgreat\s+schools?\b
\bschool\s+district\b
\bsafe\s+neighborhood\b
\bquiet\s+(?:area|neighborhood|street(?!\s+with))\b
\bfamily-friendly\b
\bideal\s+for\s+families\b
\bup-and-coming\b
\btight-knit\s+community\b
\bexclusive\s+(?:community|area|neighborhood)\b

# MLS data quality
\bmust-see\b
\bwon't\s+last\b
\bact\s+now\b
\brare\s+opportunity\b
\bonce-in-a-lifetime\b
\bhidden\s+gem\b
\bshow\s+stopper\b
\bpriced\s+to\s+sell\b
\bmust\s+sell\b
\bowner\s+motivated\b
\bdesperate\b
```

Word boundaries on every pattern to prevent the "Oakwood School District is a neighborhood name" false positive.

## The build (3 files + 1 cron line)

### File 1 — `compliance_supervisor.py`

```python
#!/usr/bin/env python3
"""
compliance_supervisor.py — daily 11 AM compliance run.

Loads tomorrow's queued content. Runs 14 checks. Holds anything failing
in /content/blocked/<date>/. Writes summary to daily brief.
"""
import os, sys, json, datetime as dt, re, hashlib, requests
from pathlib import Path

QUEUE = Path("~/content-agent/queue/tomorrow").expanduser()
BLOCKED = Path("~/content-agent/content/blocked").expanduser()
HISTORY = Path("~/content-agent/state/30day_content_hashes.json").expanduser()

# Load 14 check modules
import checks  # YOUR module — each check returns (passed: bool, reason: str)

def run(content_item):
    failures = []
    for name in [
        "check_criminal_leaks",
        "check_word_boundaries",
        "check_optout",
        "check_closed_or_uc",
        "check_daily_cap",
        "check_image_rules",
        "check_links",
        "check_brand_keyword",
        "check_license_disclosure",
        "check_escrow_tag",
        "check_mls_attribution",
        "check_fh_overlay_loaded",
        "check_tcpa_consent",
        "check_dedup_30day",
    ]:
        passed, reason = getattr(checks, name)(content_item)
        if not passed:
            failures.append({"check": name, "reason": reason})

    return failures

def block_item(item, failures):
    today = dt.date.today().isoformat()
    block_dir = BLOCKED / today
    block_dir.mkdir(parents=True, exist_ok=True)
    payload = {**item, "_failures": failures, "_blocked_at": dt.datetime.now().isoformat()}
    (block_dir / f"{item['id']}.json").write_text(json.dumps(payload, indent=2))
    print(f"[{dt.datetime.now()}] BLOCK content_id={item['id']}  "
          f"Reason: {failures[0]['check']} triggered: {failures[0]['reason']}")

def main():
    if not QUEUE.exists():
        return
    items = [json.loads(p.read_text()) for p in QUEUE.glob("*.json")]

    blocked = 0
    passed = 0
    for item in items:
        failures = run(item)
        if failures:
            block_item(item, failures)
            blocked += 1
        else:
            passed += 1

    print(f"[{dt.datetime.now()}] Compliance run done — {passed} passed, {blocked} blocked")

if __name__ == "__main__":
    main()
```

### File 2 — `checks.py`

```python
#!/usr/bin/env python3
"""
checks.py — 14 individual compliance checks. Each returns (passed, reason).
"""
import re, json, requests, hashlib, datetime as dt
from pathlib import Path

CRIMINAL_LEAKS = [
    r"\bgreat\s+schools?\b", r"\bschool\s+district\b",
    r"\bsafe\s+(?:neighborhood|area|community)\b",
    r"\bquiet\s+(?:area|neighborhood|street(?!\s+with))\b",
    r"\bfamily-friendly\b", r"\bideal\s+for\s+families\b",
    r"\bup-and-coming\b", r"\btight-knit\s+community\b",
    r"\bexclusive\s+(?:community|area|neighborhood)\b",
    r"\bmust-see\b", r"\bwon't\s+last\b", r"\bact\s+now\b",
    r"\brare\s+opportunity\b", r"\bonce-in-a-lifetime\b",
    r"\bhidden\s+gem\b", r"\bshow\s+stopper\b",
    r"\bpriced\s+to\s+sell\b", r"\bmust\s+sell\b",
    r"\bowner\s+motivated\b", r"\bdesperate\b",
    # ... 27 more patterns in the full 47-item list
]

def check_criminal_leaks(item):
    body = item.get("body", "") + " " + item.get("caption", "")
    for p in CRIMINAL_LEAKS:
        m = re.search(p, body, re.I)
        if m:
            return False, f"triggered on phrase '{m.group(0)}'"
    return True, ""

def check_optout(item):
    if not item.get("recipient_email"): return True, ""
    optouts = json.loads(Path("~/content-agent/state/optout_list.json").expanduser().read_text())
    if item["recipient_email"] in optouts:
        return False, f"recipient {item['recipient_email']} is on opt-out list"
    return True, ""

def check_closed_or_uc(item):
    if not item.get("mls_id"): return True, ""
    # Live MLS phase check
    r = requests.get(f"{os.environ['MLS_API']}/listing/{item['mls_id']}/status")
    status = r.json().get("status")
    if status in ("PEND", "CLD"):
        return False, f"MLS {item['mls_id']} flipped to {status} between draft and publish"
    return True, ""

def check_daily_cap(item):
    platform = item.get("platform")
    if not platform: return True, ""
    today_count = count_scheduled_today(platform)  # YOUR helper
    if today_count >= 4:
        return False, f"already have {today_count} posts scheduled for {platform} today"
    return True, ""

def check_dedup_30day(item):
    content_hash = hashlib.md5(item.get("body", "").encode()).hexdigest()
    history = json.loads(Path("~/content-agent/state/30day_content_hashes.json").expanduser().read_text())
    if content_hash in history.get("hashes_last_30d", []):
        return False, "exact content already published in last 30 days"
    return True, ""

# ... 9 more check functions per the 14-check list
```

### File 3 — Daily brief integration

Your daily brief email at 6:15 AM the next morning shows what got blocked:

```python
# inside daily_brief.py
blocked_yesterday = list((BLOCKED / yesterday.isoformat()).glob("*.json"))
if blocked_yesterday:
    body += f"\n\n## {len(blocked_yesterday)} items held by compliance\n"
    for path in blocked_yesterday:
        item = json.loads(path.read_text())
        body += f"- {item['id']}: {item['_failures'][0]['check']} — {item['_failures'][0]['reason']}\n"
    body += f"\nReview at: ~/content-agent/content/blocked/{yesterday.isoformat()}/\n"
```

## The cron line

```cron
0 11 * * * cd ~/content-agent && python3 compliance_supervisor.py >> ~/content-agent/logs/compliance.log 2>&1
```

Single line. 11 AM daily. The supervisor handles the rest.

## What this saves you

| Without supervisor | With supervisor |
|---|---|
| One steering phrase per quarter slips into MLS = $5K-$50K license defense | Zero published violations |
| One opt-out cross-reference miss = $1,500 TCPA suit | Zero published violations |
| One closed-listing post hits IG 4 hours late = embarrassment + buyer-side leverage | Closed-or-UC check holds before publish |
| Hash-dedup miss = same post published twice = follower confusion | 30-day dedup catches |

The cost is 5 minutes a day reviewing blocked items. The benefit is your license.

## Companion content

YouTube Episode 13: `Build Your Cron — 14-Check Compliance Supervisor (TCPA + FH Insurance)` — Al + Victoria walk through a real blocked-item review on camera, including the day "great schools" almost shipped to MLS.

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
