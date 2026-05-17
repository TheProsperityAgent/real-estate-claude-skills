# PRODUCTION — canva-real-estate-lifecycle

The day-in-the-life of running this skill on a real listing. Reads as the agent's actual workflow, not as documentation.

## Where this fits in Victoria's stack

Your Canva work is downstream of the MLS pull + the blog draft. The blog draft fans out across Metricool (4 platforms per brand) via `publisher.py` — that's the *organic content* lane. Canva is the *listing-marketing* lane, agent-driven per listing.

Both lanes share the same Brand Kit. Both lanes load `fair-housing-overlay` for compliance. Both lanes ship through Metricool (your Canva designs come out as PNG/JPG → Metricool slot → 4 platforms).

## One-time setup (do this once, ever)

1. Open Canva Pro. Settings → Brand Hub → New Brand Kit.
2. Run the foundation prompt from `SKILL.md`. Save the output to `~/Documents/canva-brand-kit.md` so you have it for next time.
3. Inside Canva, build the 7 folders: `01-Just-Listed`, `02-Open-House`, `03-Just-Sold`, `04-Coming-Soon`, `07-Quarterly-Postcards`, `08-Closing-Day`, `09-Price-Reduced`, plus `99-Brand-Kit`.
4. Build one template per stage following the dimensions in each example file. The naming convention from the foundation prompt is what the stage prompts reference back.
5. Drop your logo variants, headshots, palette, fonts, footer block, and EHO logo into `99-Brand-Kit`.

You will never do this again. Every subsequent stage prompt just references templates by name.

## Daily run — Just Listed (the highest-frequency stage)

Pulled from Victoria's actual day:

```
Morning, 8:45 AM. Just took a listing in Winterville.
MLS data is fresh. I want IG + FB + TikTok up by 9:30.
```

Walk:

1. Open Claude Code. Load `canva-real-estate-lifecycle` + `fair-housing-overlay`.
2. Paste the listing data into the Just Listed prompt (see `examples/just-listed.md` for the exact prompt).
3. Claude returns: IG caption (≤2200 chars + 5 hashtags), FB post (≤700 chars + question close), TikTok caption (≤300 chars), Canva brief for 3 templates with exact placeholder text + photo-selection guidance.
4. Open Canva → folder `01-Just-Listed` → template `JL-Square-Photo-Forward`. Replace placeholder text per the brief. Replace placeholder image with MLS cover photo. Export PNG.
5. Repeat for `JL-Story-Photo-Forward` (vertical, for IG/FB story + TikTok cover) and `JL-Flyer-Letter` (door-knocking handout).
6. Schedule via Metricool — IG square + IG story + FB landscape go in. TikTok gets the script outline that came back in step 3.
7. Print the flyer for door-knocking. Stack of 30 in your car.

Total time: 8 minutes from listing data to scheduled posts + printed flyer. The same workflow rebuilt from scratch every time would be 45-60 minutes.

## Cron entry — Saturday market recap (the only stage that fits a cron)

Most stages are agent-driven (you triggered them because a listing did something). The exception is the Quarterly Postcard mailing window and the Saturday market recap variant of MLS-to-social. The Saturday recap pulls last week's market data and fans out 5 platform posts.

See `cron.example` in this folder for the exact line. It runs at 8:00 AM Saturday and writes to `~/content-agent/logs/canva_lifecycle.log`. By the time you sit down with coffee, the captions are drafted and the Canva briefs are open in your queue.

## Failure modes

See `examples/failure-modes.md`. The big ones agents hit:

- Running a stage prompt before the one-time foundation setup → Canva brief references templates that don't exist.
- Forgetting the EHO logo placement on the printed flyer (FHA violation on printed RE marketing).
- Letting the Q3 quarterly postcard slip into "back to school" framing (FHA demographic-implication trap).
- Posting a Just Sold with sale price + client first name when permission was implicit-only — never explicit.

Each failure has a fix in the file.

## What to do AFTER the post ships

`metricool_image_sweeper.py` runs twice daily (8:30 AM + 4 PM) and normalizes any image Metricool held due to size or aspect-ratio issues. If a Canva export goes in at the wrong dimensions, the sweeper catches it before the scheduled slot fires. You will never lose a post to an aspect-ratio mismatch as long as the sweeper is in your crontab.

If you ship 4+ designs of the same type in 24 hours (e.g., five Just Listed posts because you closed on a builder portfolio), the daily-cap check inside `compliance_supervisor.py` (11 AM) will flag the run for review — FB downranks and IG throttles when the same type of post stacks too tight.

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
