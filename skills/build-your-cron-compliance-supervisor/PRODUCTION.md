# PRODUCTION — build-your-cron-compliance-supervisor

How to actually ship the 14-check supervisor on your machine. Real flow.

## What you need

- The four other crons in Season 3 running OR your own equivalent content pipeline.
- Live MLS API access (for `check_closed_or_uc`) — most MLS systems offer an export endpoint or RPR feed. If you cannot reach the MLS programmatically, fall back to a daily CSV import with `refresh_mls_data.py`.
- A CRM opt-out list export. BoldTrail can produce one; cache locally and refresh every 4 hours.
- Write access to `~/content-agent/queue/tomorrow/` (where pending content lives) and `~/content-agent/content/blocked/` (where blocked content moves).

## Step 1 — Start with the criminal-leak check

The single highest-ROI check is `check_criminal_leaks`. Build it first. Test it against the last 30 days of your already-published content (a backwards-audit).

```bash
cd ~/content-agent
python3 -c "
import json, glob
from checks import check_criminal_leaks
hits = 0
for f in glob.glob('queue/published/*.json'):
    item = json.load(open(f))
    ok, reason = check_criminal_leaks(item)
    if not ok:
        print(f'{f}: {reason}')
        hits += 1
print(f'Total: {hits} hits in published content')
"
```

If you find hits in your already-published content (most agents do — Victoria's first backwards-audit found 17 hits in 90 days), that's free intelligence — those are the patterns your AI defaults to that violate FH/MLS rules. Add the most common ones to your `fair-housing-overlay` blocklist so they don't generate in the first place.

## Step 2 — Build the other 13 checks incrementally

Don't try to ship all 14 on day 1. Ship in this order:

| Day | Check | Why this order |
|---|---|---|
| 1 | check_criminal_leaks | Highest-frequency violation |
| 1 | check_word_boundaries | Prevents false positives that would otherwise nuke trust |
| 2 | check_dedup_30day | Prevents content-repeat embarrassment |
| 3 | check_optout | TCPA dollar exposure |
| 4 | check_closed_or_uc | MLS-rule violation if you post a closed listing as active |
| 5 | check_daily_cap | Algorithm preservation |
| 6 | check_brand_keyword | Brand consistency |
| 7 | check_license_disclosure | RE Commission rule |
| 8 | check_image_rules | Aesthetic + EHO compliance |
| 9 | check_links | Catches stale URL refs |
| 10 | check_mls_attribution | Required by some state MLS rules |
| 11 | check_escrow_tag | Required by some state RE Commission rules |
| 12 | check_fh_overlay_loaded | Defense in depth |
| 13 | check_tcpa_consent | Defense in depth |

By day 14, all 14 checks are running.

## Step 3 — Wire the supervisor into the queue

`~/content-agent/queue/tomorrow/` is where pending content lives. The pipeline writes drafts here AFTER overnight generation but BEFORE publish.

The supervisor:
1. Reads every JSON file in `queue/tomorrow/`.
2. Runs 14 checks per item.
3. If all 14 pass, leaves the file in `queue/tomorrow/` (will be published by morning fan-out).
4. If any check fails, moves the file to `content/blocked/<date>/<id>.json` with the failure log appended.

Critically: the supervisor NEVER auto-fixes content. It blocks-or-passes. Auto-fix is reserved for the agent's review.

## Step 4 — Configure the daily brief email to include blocked items

This is the closing-the-loop step. Without it, blocked items pile up in `content/blocked/` and nobody reviews them.

```python
# inside daily_brief.py
def render_blocked_section():
    yesterday = (dt.date.today() - dt.timedelta(days=1)).isoformat()
    blocked_dir = Path(f"~/content-agent/content/blocked/{yesterday}").expanduser()
    if not blocked_dir.exists() or not list(blocked_dir.glob("*.json")):
        return ""

    items = [json.loads(p.read_text()) for p in blocked_dir.glob("*.json")]
    out = f"\n## {len(items)} items held by compliance — review at /content/blocked/{yesterday}/\n"
    for item in items:
        out += f"- {item['id']}: {item['_failures'][0]['check']} — {item['_failures'][0]['reason']}\n"
    return out
```

The brief lands at 6:15 AM. You read it with coffee. You walk through `/content/blocked/<yesterday>/` and decide:
- Fix the offending phrase, drop back in `queue/tomorrow/`. Will publish tomorrow morning.
- The block was a false positive. Add the pattern to `examples/false-positives.md`. Tune the regex.
- The block reflects a real issue with the upstream skill. Update the skill prompt or the `fair-housing-overlay` rules to prevent generation in the first place.

## Step 5 — Install the cron

```cron
0 11 * * * cd ~/content-agent && python3 compliance_supervisor.py >> ~/content-agent/logs/compliance.log 2>&1
```

11 AM daily. Anchor to local timezone explicitly:

```cron
0 11 * * * cd ~/content-agent && TZ=America/New_York python3 compliance_supervisor.py >> ~/content-agent/logs/compliance.log 2>&1
```

(Daily-cap counter rolls over at local midnight, not UTC midnight.)

## Step 6 — Run a 30-day shakedown

For the first 30 days, expect:

- Day 1: a few false positives. Tune regex word boundaries.
- Day 5-10: false-positive rate drops. True positives start surfacing (your upstream skills had blind spots).
- Day 15-20: the daily blocked count stabilizes. Most days zero, occasional days 1-2.
- Day 30: confidence baseline established. The supervisor is invisible most days and critical on the days it fires.

If the false-positive rate stays above 5% after 30 days, your patterns are too aggressive. Loosen.

If the true-positive rate stays above 5% after 30 days, your upstream `fair-housing-overlay` is leaking. Strengthen the overlay so the supervisor catches less.

## What can break in production

1. **Live MLS API down** — `check_closed_or_uc` fails to connect. Decision: fail open (allow publish) or fail closed (block)? Recommend fail-open with an alert; otherwise an MLS outage halts your entire publishing pipeline.

2. **Opt-out list cache stale** — list pulled 3 hours ago, lead unsubscribed 1 hour ago, supervisor approves a touch to them. Mitigation: refresh opt-out cache every 4 hours; for high-stakes touches, also do a real-time check at the moment of publish.

3. **Word boundary regex misses a Unicode variant** — "great schools" (non-breaking space) is not caught by `\bgreat\s+schools\b`. Mitigation: normalize whitespace before pattern match: `re.sub(r"\s+", " ", body)`.

4. **The supervisor crashes mid-run, half items processed** — partial state. Mitigation: each item is processed atomically (file moved to /blocked/ or left in /queue/). A crash leaves processed items correctly placed; unprocessed items remain in queue for the next run.

## When to disable the supervisor

Never. The supervisor is the safety net. If you disable it because "nothing has been blocked this month", that's the month you publish "great schools" to MLS.

If a check is producing too many false positives, tune the check — don't disable the supervisor.

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
