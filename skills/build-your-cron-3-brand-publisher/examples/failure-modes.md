# Failure modes — 3-brand publisher

The breakages and the fixes.

## 1. Cross-brand cross-promotion sneaks in

**Symptom:** LIG post ends with "if you're an agent thinking about a move, our team at eXp..." or Prosperity post links to a LIG buyer guide. Either way, brand integrity damaged.

**Why:** Voice gem too thin. The LLM filled the gap with general "agent stuff" because the gem didn't specify the audience boundary hard enough.

**Fix:** Add an explicit banned-patterns list to each voice gem. LIG voice gem must include `"if you're an agent" — banned`. Prosperity voice gem must include `"livingingreenvillenc" — banned`. Run the weekly `brand_separation_check.py` (cron entry included) to catch leaks early.

## 2. Same headline published on 2 brands the same week

**Symptom:** Tuesday LIG and Wednesday Prosperity both ship a post titled "Spring 2026 Greenville Market Update". Looks lazy.

**Why:** Shared topic queue. Or per-brand queues that picked the same priority.

**Fix:** Per-brand topic queues with no shared seeds. AND a cross-brand dedup checker that compares last-7-days titles across all three brands and flags duplicates BEFORE publish.

```python
def cross_brand_dedup(new_title, brand):
    for other_brand in ALL_BRANDS:
        if other_brand == brand: continue
        recent = load_ledger(other_brand, days=7)
        if any(fuzzy_match(new_title, t) > 0.7 for t in recent):
            raise BrandCollision(f"Title clashes with {other_brand}")
```

## 3. Rank Math meta dropped silently

**Symptom:** Post published. SEO title + description blank when you check the Rank Math meta box. Or live HTML `<meta name="description">` is empty.

**Why:** You sent Yoast keys to a Rank Math site. Or you sent Rank Math keys to a Yoast site. The receiving plugin silently dropped them.

**Fix:** `publisher.py` verifies meta saved by fetching the post URL after push and confirming the description string appears in the live HTML. If not, retry with the other plugin's key format OR alert.

## 4. Wrong Metricool brand ID

**Symptom:** Your LIG IG post shows up on Prosperity's IG account. Or vice versa. Subscribers confused.

**Why:** Metricool brand IDs are similar-looking numbers. You typo'd one in `.env` or you passed the wrong one in the publisher call.

**Fix:** Use a mapping dict in `publisher.py` keyed by brand-name, never by raw ID:

```python
METRICOOL_BRANDS = {
    "LIG": os.environ["METRICOOL_LIG_BRAND_ID"],
    "PROSPERITY": os.environ["METRICOOL_PROSPERITY_BRAND_ID"],
    "VICTORIA": os.environ["METRICOOL_VICTORIA_BRAND_ID"],
}
```

The function takes `brand="LIG"`, not `brand_id=12345`. Less typo-able.

## 5. Voice gem stale and the brand drifts

**Symptom:** Six months in, the Prosperity blog starts sounding generic. Audience engagement drops.

**Why:** Voice gem was trained on transcripts from 2024. By 2026 the brand has evolved — new positioning, new locked pivots — but the gem hasn't been refreshed.

**Fix:** Refresh voice gems quarterly. Pull recent YouTube transcripts, podcast episodes, client meetings. Re-train. The voice gem is a living document, not a one-time setup.

## 6. One brand fails, the other two get blocked

**Symptom:** `prosperity_blog_router.py` errors out (Anthropic API rate-limited). `run_daily.sh` exits before LIG and Victoria run. All three brands dark for the day.

**Why:** Bash script using `set -e` (or implicit exit on error).

**Fix:** Each router runs independently. If one fails, the others continue:

```bash
python3 greenville_blog_router.py || echo "LIG failed, continuing"
python3 prosperity_blog_router.py || echo "Prosperity failed, continuing"
python3 victoria_blog_router.py || echo "Victoria failed, continuing"
```

Daily brief includes which routers succeeded and which failed.

## 7. Title over 60 chars published

**Symptom:** SERP shows your title truncated mid-word. Click-through drops.

**Why:** Title validator missing or returns warning instead of hard fail.

**Fix:** `publisher.py` does an `assert len(title) <= 59`. Hard fail. The router catches and either retries with a shortened title or alerts.

## 8. Mentioning a tool you weren't supposed to mention

**Symptom:** A blog post mentions an internal-only paid tool whose ToS prohibits automation.

**Why:** The voice gem didn't include the banned-tool list.

**Fix:** Every voice gem includes the brand's banned-mention list. AND `compliance_supervisor.py` (see `build-your-cron-compliance-supervisor`) has a hard blocklist of banned tool names that fires across ALL brands.

Internal-only tools are internal-only across all blogs/pages/social/ads/courses. Once a tool name leaks publicly, the tool can revoke your account and that whole workflow dies.

## 9. Forgetting the Prosperity audience-rule pivot

**Symptom:** Prosperity blog ends up neutral on a competitor brokerage. Reads as a comparison. Helps competitor SEO.

**Why:** Prompt drift. The `PROSPERITY_AUDIENCE_RULE` in the voice gem didn't activate because the topic seed didn't trigger it.

**Fix:** Make the pivot rule a HARD post-generation check, not just a prompt instruction:

```python
def prosperity_pivot_check(draft):
    competitors = ["Compass", "KW", "Keller Williams", "Redfin", "Real",
                   "Side", "Anywhere", "RE/MAX", "ReMax"]
    if any(c in draft["body"] for c in competitors):
        assert "eXp" in draft["body"] and "Pinder Team" in draft["body"], \
            "Competitor mentioned without eXp pivot"
```

If the check fails, the router either regenerates with stronger pivot instruction OR pulls the draft for review.

## 10. The brand-separation check disabled because "it never alerts"

**Symptom:** Six months in, you disable the weekly brand-separation cron because it hasn't alerted. Two weeks later, a cross-promotion slipped through. The check would have caught it.

**Why:** "Never alerts" feels like dead weight.

**Fix:** Keep silent supervisors silent. The whole value of `brand_separation_check.py` is that it has nothing to say 51 weeks of the year. The 1 week it does have something to say, it saved you. Don't disable.

Same pattern as `metricool_image_sweeper.py` (twice-daily safety net) and `supervisor.supervisor` (every-2-hour verifier). Silent crons are the gold standard — they earn their keep by catching the rare-but-expensive thing.
