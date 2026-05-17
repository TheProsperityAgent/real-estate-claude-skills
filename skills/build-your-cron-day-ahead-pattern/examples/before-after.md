# Before / After — day-ahead orchestration

## The 7 AM scramble — before this pattern

```
6:50 AM — Wake up. Check phone. No content scheduled.
7:00 AM — Open laptop. Open Claude / Gemini / OpenAI. Start drafting today's blog.
7:30 AM — Family wants breakfast. Laptop stays open. Tension.
8:00 AM — Blog drafted, not great. Open WordPress. Push. Open Metricool.
          Schedule 4 platforms.
9:00 AM — Try to switch to actual real-estate work (showings, calls).
          Brain is fried.
```

The pattern is the same every day for years. Family resents the laptop. You resent the laptop. The content is rushed.

## The 5:45 AM coffee — after this pattern

```
5:45 AM — Sit down. Coffee. The daily brief email is already in your inbox
          (it landed at 6:15 AM yesterday).
5:50 AM — Read the brief: "Yesterday's batch processed 12 drafts.
          3 brand blogs scheduled for today + tomorrow.
          4 platform posts × 3 brands queued in Metricool.
          1 item held by compliance — review at /content/blocked/."
5:55 AM — Open the blocked item. It's a listing description with "great
          schools" in the closing line. Rewrite to "12-min commute to
          ECU Health." Drop back in queue.
6:00 AM — Close laptop.
6:15 AM — run_daily.sh fires in the background. Tomorrow's drafts
          submit to Gemini Batch API. You don't open the laptop.
7:00 AM — Breakfast with family.
9:00 AM — On a showing in Winterville. Phone buzzes — the supervisor
          ran at 8:00 AM, everything green.
```

5 minutes of "content work" in the morning. The rest of the day is real-estate work + family time.

## What changed structurally

1. **Heavy LLM work moved to evening.** Gemini Batch API submits at 7:45 PM, completes overnight, costs 50% less.
2. **Morning is collect + schedule + spot-check.** Not generate. Generate is what made the morning take 60 min.
3. **`supervisor.supervisor` removed the "did it work?" anxiety.** Every 2 hours, it confirms output. If silent, all crons are healthy.
4. **Day-ahead scheduling.** Tomorrow's content is queued by 6:30 AM today. Every post gets 24+ hours of marination instead of 90 min of stressed edit-pass.
5. **Family-time window protected.** 7:00-7:30 AM is non-negotiable family time. The cron schedule respects it.

## The cost math

| Live API only | Day-ahead with batch |
|---|---|
| 300 LLM calls/day @ $0.003 = $0.90/day | 30 live @ $0.003 + 270 batch @ $0.0015 = $0.49/day |
| $328/year | $179/year |
| Morning generation = 60-90 min/day of agent time | Morning collect + spot-check = 5-10 min |
| Agent time @ $50/hr × 75 min/day × 365 = $22,800/year | Agent time @ $50/hr × 7.5 min/day × 365 = $2,280/year |

The infra savings ($149/year) are nice. The agent-time savings (~$20K/year) are why the pattern wins.

## The 2-hour supervisor catch — real story

One Wednesday, Victoria's Metricool API token silently expired (90-day rotation she missed). At 8:00 AM, `supervisor.supervisor` ran across all crons. It checked the Metricool API on behalf of `publisher.py` and got a 401.

Email alert hit her inbox at 8:01 AM: `⚠ supervisor: publisher.py — Metricool API returned 401 (token expired?)`.

At 8:15 AM, while making breakfast, she opened Metricool web UI, regenerated the token, updated `.env`, restarted nothing (cron picks up `.env` on next tick). At 8:30 AM the `metricool_image_sweeper.py` cron tick succeeded.

Total downtime: 30 minutes. Without the supervisor: 7-10 days of silent failure before she noticed the missing posts.

The supervisor's value is the catch on the 1 day in 90 that something breaks.

## What about Anthropic vs Gemini for batch?

Anthropic doesn't currently have a batch tier with the same 50% discount. If you're Anthropic-only, you can:

1. Use Anthropic live for everything (cost: full).
2. Migrate non-time-sensitive drafts to Gemini Batch (mixed-stack: Gemini for batch, Anthropic for live).
3. Wait for Anthropic to offer a batch tier.

Victoria's stack is mixed: Gemini for content drafts (batch), Anthropic for higher-quality client-facing generation (live, Opus). The mix gets her the cost saving on volume work and the quality on client-facing work.

## When this pattern doesn't apply

- One-cron stacks. If you only run `speed_to_lead.py` and nothing else, you don't need the orchestration. Just run that one cron.
- Real-time-only workflows. Anything that has to respond within 5-15 minutes (speed-to-lead, live CMA, kitchen-table dispatch) stays on live API forever. Batch is overnight only.
- Sub-30-LLM-calls-per-day stacks. The infra savings ($150/year) are too small to justify the migration work. Stay on live until your volume crosses ~100 calls/day.

## What you can't fake

The discipline of writing down your morning window. The pattern only works if you actually treat 7 AM as the line. Once you start drifting to 7:15, then 7:30, then 8, the whole point of the pattern collapses and you're back to the morning scramble.

The supervisor.supervisor cron. Most agents skip it on first build because it feels redundant ("the other crons just ran, why do I need to check them?"). Then a token expires silently and they don't notice for 8 days. Install the supervisor on day 1.

## Where this skill connects to the others

This pattern is the substrate for:

- `build-your-cron-daily-mls-to-metricool` (lives inside `run_daily.sh`)
- `build-your-cron-3-brand-publisher` (lives inside `run_daily.sh`)
- `build-your-cron-speed-to-lead` (runs independently, every 5 min — supervisor watches it)
- `build-your-cron-compliance-supervisor` (11 AM check, supervisor watches it)

Build day-ahead first. The others slot in cleanly.
