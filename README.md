# Real Estate Claude Skills

**Open-source production-grade Claude skills for real estate agents.** Built by [Al & Victoria Pinder](https://theprosperityagent.com), ICON agents at eXp Realty in Greenville, NC.

Every skill is **Fair Housing compliant**, **TCPA-aware**, and **MLS-rule respecting** by default. We never knock a town, builder, neighborhood, competing platform, or competing agent.

15 skills across 3 seasons. Each skill ships with the production layer — an actual runner script, a paste-ready cron entry, real before / after examples, and the failure modes you'll hit in the wild with the fix for each.

## Quick start

1. Install Claude Code: https://claude.ai/download
2. Clone this repo locally: `git clone https://github.com/theprosperityagent/real-estate-claude-skills.git`
3. Always load `fair-housing-overlay` alongside any other skill — it's the compliance foundation every other skill builds on.
4. Start with `quick-start-3-wins` (Season 1 E1) for three workflows running today.

**Each skill folder contains:**

```
skills/<skill-name>/
├── SKILL.md           ← the actual skill — prompt, rules, variants
├── PRODUCTION.md      ← how Victoria runs this in production day-to-day
├── examples/
│   ├── before-after.md   ← side-by-side on a real Greenville listing
│   └── failure-modes.md  ← the breaks Victoria has seen + fixes
└── cron.example       ← paste-ready cron entry (where applicable)
```

This is the layer most "AI for realtors" skills miss. A prompt is not a workflow — you need the prompt + how to run it + what breaks + how to recover.

## Roadmap

### Season 1 — Foundation (E1-E9, weekly Thursdays)

The 9 always-load skills every agent installs first. Built around `fair-housing-overlay` as the compliance floor.

| Status | # | Episode | Skill |
|--------|---|---------|-------|
| ✅ | E1 | Claude Code in 15 Minutes — 3 Wins | [`quick-start-3-wins`](skills/quick-start-3-wins/) |
| ✅ | E2 | Why Claude Skills Change Real Estate (thesis) | [`fair-housing-overlay`](skills/fair-housing-overlay/) |
| ✅ | E3 | Listing Descriptions in 10 Minutes | [`listing-description`](skills/listing-description/) |
| 📋 | E4 | Counter Every Offer Like a 20-Year Pro | [`offer-counter`](skills/offer-counter/) |
| 📋 | E5 | Your Lead Nurture Just Wrote Itself | [`lead-nurture-cadence`](skills/lead-nurture-cadence/) |
| 📋 | E6 | CMA Narratives in 60 Seconds | [`cma-narrative`](skills/cma-narrative/) |
| 📋 | E7 | Claude → BoldTrail/kvCore Integration | [`claude-boldtrail-bridge`](skills/claude-boldtrail-bridge/) |
| 📋 | E8 | Today's MLS Pull → 5 Social Posts | [`mls-to-social`](skills/mls-to-social/) |
| 📋 | E9 | Listing Presentation — From Comps to Brief | [`listing-presentation-builder`](skills/listing-presentation-builder/) |

### Season 2 — Canva Stack for Realtors (E10-E11)

One consolidated lifecycle skill that walks every active listing through Coming Soon → Just Listed → Open House → Price Reduced → Just Sold → Closing Day, plus the Quarterly Postcard for geographic farming.

**Listing presentations + CMA covers moved to Gamma** (see `canva-real-estate-lifecycle/README-on-canva-vs-gamma.md` for the split rule).

| Status | # | Episode | Skill |
|--------|---|---------|-------|
| 📋 | E10 | Canva Real Estate Lifecycle (foundation + 7 stages) | [`canva-real-estate-lifecycle`](skills/canva-real-estate-lifecycle/) |
| 📋 | E11 | Why Listing Decks Live in Gamma, Not Canva | (uses canva-real-estate-lifecycle + listing-presentation-builder) |

### Season 3 — Cron Classes (E12-E16) — the differentiator

This is the season most "AI for realtors" content skips. You don't get a SaaS recommendation. You get the architecture, the script, the cron entry, and the supervisor pattern — same as what runs on Victoria's machine at 6:15 AM every morning.

Each cron-class skill teaches you to BUILD the cron on your own machine. Files, env vars, failure modes, supervisor.

| Status | # | Episode | Skill |
|--------|---|---------|-------|
| 📋 | E12 | Build Your Cron — Daily MLS to Metricool | [`build-your-cron-daily-mls-to-metricool`](skills/build-your-cron-daily-mls-to-metricool/) |
| 📋 | E13 | Build Your Cron — One Engine, Three Brand Voices | [`build-your-cron-3-brand-publisher`](skills/build-your-cron-3-brand-publisher/) |
| 📋 | E14 | Build Your Cron — 5-Minute Speed-to-Lead (391% Lift) | [`build-your-cron-speed-to-lead`](skills/build-your-cron-speed-to-lead/) |
| 📋 | E15 | Build Your Cron — 14-Check Compliance Supervisor | [`build-your-cron-compliance-supervisor`](skills/build-your-cron-compliance-supervisor/) |
| 📋 | E16 | Build Your Cron — Day-Ahead Orchestration Pattern | [`build-your-cron-day-ahead-pattern`](skills/build-your-cron-day-ahead-pattern/) |

**Legend:** ✅ = YouTube episode aired · 📋 = skill ready, episode upcoming Thursday.

## Cron Classes — what makes Season 3 different

Most "AI for realtors" content is chat-tool tutorials. Claude Code does different work — it runs autonomously on a schedule, with real tool access (files + browser + MCP connectors + your CRM API), and posts to your contact note timelines while you're at a showing.

Season 3 teaches you to build that. Five cron classes, each one a workflow Victoria runs every day:

- **`build-your-cron-day-ahead-pattern`** — the substrate. Eve prep drafts overnight (50% cheaper via Gemini Batch API), morning collect at 6:15 AM schedules tomorrow's content, supervisor.supervisor every 2 hours catches silent failures.
- **`build-your-cron-daily-mls-to-metricool`** — daily MLS pull → 4-platform Metricool fan-out per brand. Includes the `metricool_dedup.py` slot-bunching prevention and the twice-daily `metricool_image_sweeper.py` safety net.
- **`build-your-cron-3-brand-publisher`** — one content generator, three brand routers, three WordPress sites, three Metricool brand IDs. Same engine, three voices. Brand-aware dedup ledgers prevent cross-brand drift.
- **`build-your-cron-speed-to-lead`** — every-5-min fresh-lead catcher. Catches BoldTrail leads within 5-15 min instead of 23 hours. 391% conversion lift. TCPA consent gate baked in.
- **`build-your-cron-compliance-supervisor`** — 11 AM daily 14-check run before any AI-generated content reaches a publish API. Criminal-leak blocklist, opt-out cross-reference, closed-or-UC exclusion, daily-cap throttle, and 9 more. Your TCPA + FH insurance policy.

You install these on your own machine. No SaaS. No monthly fee. Open-source, customize freely.

## Compliance baked in

Every skill in this repo is built to be:

- **Fair Housing compliant** — no school references, no demographic targeting, no language that steers buyers by perception of community.
- **TCPA-aware** — never produces unsolicited outreach content; only owned-channel content. Consent gate is hard, not soft.
- **MLS-rule respecting** — no "must-see", "won't last", or scarcity language that hurts MLS data quality.
- **Always positive** — never knocks a town, builder, neighborhood, competing platform, competing brokerage, or competing agent.

The `fair-housing-overlay` skill (E2) is the foundation. Every other skill in this repo loads it. When TCPA/FH rules change, you update one place.

## Why we built this

Real estate agents do the same 30 workflows every week — listing descriptions, offer replies, lead follow-up, social posts, CMAs, presentations, closing graphics. Most "AI for realtors" tools either ignore Fair Housing entirely or bury it in fine print.

We're realtors. We want a tool stack we can defend in front of the broker. So we built one and made it free.

## How to contribute

See `CONTRIBUTING.md`. Pull requests welcome — especially:

- State-specific FHA extensions to `fair-housing-overlay`.
- New cron classes for workflows you've automated on your machine.
- Failure modes you've hit with proposed fixes.

## License

MIT. Use it, fork it, modify it, ship it. If you build something on top, tag us at [@theprosperityagents on YouTube](https://youtube.com/@theprosperityagents).

## Built by

**Al & Victoria Pinder** · ICON Agents · eXp Realty · Greenville, NC
[theprosperityagent.com](https://theprosperityagent.com) · [livingingreenvillenc.com](https://livingingreenvillenc.com)

Series companion to the [Claude Code for Realtors](https://youtube.com/@theprosperityagents) YouTube series — one new skill ships every Thursday.
