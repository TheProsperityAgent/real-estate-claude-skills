# PRODUCTION — listing-description

The 145-word listing description skill, in production. This is the "Episode 3" workhorse — every new listing runs through it.

## Where this fits

You take a new listing. MLS field is waiting. Most agents write the description from a blank screen at 9 PM after a long day. This skill does the work in 90 seconds.

You always load `fair-housing-overlay` alongside. Most of Victoria's listings cycle through:

```
listing-description  (this skill)
  └── output → MLS field directly
  └── output → social fan-out via mls-to-social
  └── output → canva-real-estate-lifecycle for Just Listed assets
```

## The actual run on a real listing

```
1:30 PM — New listing signed at 1234 Honeysuckle Lane.
1:32 PM — Open Claude Code. Load fair-housing-overlay + listing-description.
1:33 PM — Paste the prompt with property data filled in.
1:34 PM — Skill returns a 145-word draft. Read once. Two small edits (changed
          "screened porch" to "screened back porch" because that's what the
          listing photos show).
1:35 PM — Copy → paste into NCRMLS MLS field. Save.
1:36 PM — Same draft fans out to social via mls-to-social.
```

5 minutes vs 25-30 from a blank screen. And the 145 words are exactly compliant — no "great schools", no "must-see", no demographic targeting.

## What this skill does NOT do

- It does NOT write the marketing description for your website (that's longer, different tone).
- It does NOT write the Just Listed social caption (that's `mls-to-social` or `canva-real-estate-lifecycle`).
- It does NOT write the CMA narrative (that's `cma-narrative`).
- It does NOT write the listing presentation (that's `listing-presentation-builder` → Gamma).

It writes the MLS field. That's it. One narrow job, done very well.

## Where the 145-word cap comes from

Most regional MLS systems ship descriptions with a soft cap around 1,200 characters or a hard cap at the field limit. 145 words at average English-prose word length = ~870 characters, which fits every MLS field we've tested (NCRMLS, MLSPIN, BrightMLS, Stellar, NTREIS, REcolorado).

Going over 145 risks:
- Field truncation by some MLS feeds (your closing line gets cut).
- Realtor.com / Zillow display truncation (your strongest closing line never appears on the public detail page).
- Reader fatigue (140-150 words is the sweet spot for kitchen-table readability).

Don't override the cap. The skill enforces it for a reason.

## The localized close — the one line that earns the skill

The closing line is what separates a clean MLS description from a clean MLS description that converts inquiries. The skill enforces a closing line about [CITY] life that doesn't mention schools. Options:

- Commute time to a major employer (`12 minutes to ECU Health Medical Center`).
- A public park (`A quarter-mile walk to Wildwood Park`).
- A downtown amenity (`Six blocks from Greenville's Five Points downtown`).
- A commute-to-coast time (`Ninety minutes to the Crystal Coast`).

All four are objective, all four are FH-clean. Pass the right anchor in the prompt and Claude uses it. Don't pass an anchor and Claude defaults to schools (the most common geographic anchor in real estate copy).

## Variants and when to use them

| Variant | When | What changes |
|---|---|---|
| Default | Resale, $200-700K, all property types | 145 words, warm + specific, employer-commute close |
| Luxury ($700K+) | $700K+ listings | Specific-detail-rich, longer sentences, restraint over hype |
| New construction | Builder-direct, recent build | Lead with build year + builder, warranty, delivery date |
| Investor-targeted | Rental property | Close with cap rate / monthly rent / 1-mile rental comp |

Each variant has a 1-line addition you append to the base prompt. See `SKILL.md` Variants section.

## See also

- `examples/before-after.md` for a side-by-side on a real Greenville $385K listing.
- `examples/failure-modes.md` for the breaks Victoria has seen.
- `cron.example` — on-dispatch, no cron.

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
