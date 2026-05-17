# PRODUCTION — mls-to-social

The "every morning the MLS email sits unread" skill. Takes today's MLS pull and produces 5 platform-ready posts in 90 seconds. Pair with `canva-real-estate-lifecycle` for the visual half.

## Where this fits in the stack

Daily MLS pull → this skill → 5 platform-specific captions → schedule via Metricool.

In Victoria's stack, this skill is called by `mls_to_metricool.py` (inside `run_daily.sh` at 6:15 AM) to produce tomorrow's social fan-out. See `build-your-cron-daily-mls-to-metricool` for the automation.

You can also run it on-dispatch — paste this morning's MLS export, get 5 posts in 90 seconds.

## The 5 platforms — why each matters

| Platform | Cap | Algorithm preference |
|---|---|---|
| Instagram | ≤2200 chars + 5 hashtags | Visual-first, NO link in caption (kills reach) |
| Facebook | ≤700 chars + 1-2 hashtags | Conversational + question-driven engagement |
| TikTok | ≤300 chars + 5 hashtags + script | Punchy + 3-shot 15-sec video script |
| X (Twitter) | ≤280 chars + 2 hashtags | Single stat + link OK |
| LinkedIn | 300-500 words + 3 max hashtags | Professional + long-form + market analysis |

Each post respects its platform's algorithm. Same MLS data, 5 different formats.

## The hashtag formula (Instagram)

5 hashtags exactly:
- 1 city
- 1 state
- 2 listing-attribute
- 1 brand

Example for a Greenville 4-bed:
`#Greenville #NorthCarolina #FourBedroom #ScreenedPorch #PinderTeam`

Why 5 (not 30): 2026 algorithm research shows 5 is the optimal count across IG + Facebook + TikTok. More hashtags signal "spam." Fewer signal "not optimized."

## The "no link in IG caption" rule

Instagram's algorithm DOWNRANKS posts with links in the caption. Verified across 2024-2026 research.

The skill never includes a link in IG captions. Traffic routes via:
- Bio link.
- "Comment HONEYSUCKLE for the full set" → DM auto-respond → link.
- Story link sticker (link goes here, NOT caption).

If you want a link in caption-equivalent placement, use Facebook (which doesn't downrank) or LinkedIn (which actually likes professional links).

## The LinkedIn FH-risk

LinkedIn is the highest-FH-risk platform because long-form invites editorial framing. Most agents accidentally cross compliance lines on LinkedIn by:

- Comparing brokerages ("unlike Compass...")
- Knocking competing platforms ("Zillow Zestimates are wrong because...")
- Editorializing about buyer demographics ("first-time buyers in this market...")

The skill is extra strict on LinkedIn. Output never knocks competing brokerages, never compares platforms negatively, never demographics-segments buyers.

## Variants

| Variant | Use case |
|---|---|
| Default | Daily MLS pull, mixed audience |
| Buyer cohort | Specific cohort (ECU Health relocators) — reframes all 5 around that |
| Just-sold | This week's closings — celebration framing |
| Saturday market-recap | Weekly recap — 7 days, how many new, sold, DOM trend |

## How this connects to the larger stack

```
6:15 AM run_daily.sh fires
  └── mls_to_metricool.py
       └── mls-to-social skill (5 platform captions)
       └── canva-real-estate-lifecycle (Canva briefs for IG square + story)
       └── metricool_dedup (slot picking)
       └── metricool_client.schedule (4 platforms)

11 AM compliance_supervisor.py
  └── verifies the 5 platform posts passed 14 checks before publishing

8:30 + 16:00 metricool_image_sweeper.py
  └── normalizes any image Metricool held

Tomorrow 9 AM-5 PM
  └── posts fire on their dedup-spaced slots
```

## See also

- `examples/before-after.md` — a real $385K Greenville 4-bed across all 5 platforms
- `examples/failure-modes.md` — the breaks
- `cron.example` — daily fan-out via run_daily.sh

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
