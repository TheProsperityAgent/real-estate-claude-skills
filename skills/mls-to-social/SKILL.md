---
name: mls-to-social
description: Takes today's MLS export (CSV or paste) and turns it into 5 platform-ready social posts (IG / FB / TikTok / X / LinkedIn). FH-clean by default. Pairs with canva-just-listed for hero-listing visuals.
author: Al Pinder, Victoria Pinder
version: 1.0.0
---

# MLS to Social

Every morning your MLS sends you a "new listings" email or you pull a daily export. This skill turns that data into 5 social posts in 90 seconds — one for each major platform, each respecting that platform's algorithm + format.

## When to use this skill

It's 8 AM. You just pulled today's new listings under your buyers' price band. You want to post about today's market activity on all your channels. You don't want to write 5 different versions yourself.

## Prompt

```
I'm a real estate agent. Turn today's MLS data into 5 social posts.

MLS DATA (paste or CSV path):
[Paste today's new listings — for each: address, price, beds/baths/sqft,
city, days on market]

TARGETING:
- City/area: [CITY]
- Price band focus: [BAND]
- Audience: [first-time buyers / move-up buyers / investors / luxury]

REQUIRED COMPLIANCE (load fair-housing-overlay):
- NO school references
- NO demographic targeting in framing
- NO scarcity ("won't last", "act now")
- ALWAYS positive about every neighborhood
- Each post must include a real number (price, sqft, lot, DOM) — never
  vague "in a great area"

OUTPUT — 5 posts:

1. INSTAGRAM CAPTION (≤2200 chars)
   - Hook (line 1): a question or surprising stat from today's data
   - Body: 3-4 listings highlighted with specifics
   - Close: invite to DM for full list
   - 5 hashtags: 1 city, 1 state, 2 listing-attribute, 1 brand
   - NO link (IG kills reach for link-in-caption)

2. FACEBOOK POST (≤700 chars)
   - Conversational, ends with question to drive engagement
   - 1-2 hashtags max (FB ranks differently)
   - Link to website OK if soft-CTA

3. TIKTOK CAPTION (≤300 chars + script outline)
   - Caption: punchy, 5 hashtags
   - Script outline: 3-shot 15-second video walking through top 3 listings
     (no agent face-time required — voiceover over MLS photos works)

4. X / TWITTER POST (≤280 chars)
   - Single most surprising stat from today's data
   - Link OK
   - 2 hashtags max

5. LINKEDIN POST (300-500 words)
   - Professional tone — talk to other agents + relocating professionals
   - Lead with 1-line industry insight (not promotional)
   - Body: 3 specific listings with WHY each is interesting
   - Close: market commentary (positive, never doom)
   - No hashtag spam — 3 max

Write all 5.
```

## What good output looks like

5 posts that each leverage their platform's algorithm:
- IG: hooks + visual-friendly + 5 hashtags
- FB: conversational + question-based engagement
- TikTok: punchy + script outline
- X: single insight + link
- LinkedIn: long-form + professional + market analysis

## Variants

**Specific buyer cohort (e.g., relocating to Greenville for ECU Health):**
Add: `Frame all 5 posts around the buyer cohort. Use commute times to ECU Health, not generic city names. Tone shifts to relocator-helpful.`

**Just-sold variant:**
Replace "today's new listings" with "this week's closings." Skill rewrites all 5 posts as celebration content. Use [`canva/just-sold`](../canva/just-sold/) for the visual half.

**Saturday market-recap:**
Add: `This is a weekly recap, not daily. Show 7 days of activity: how many new, how many sold, median DOM, median sold price. Same 5-platform output, weekly framing.`

## Companion content

YouTube Episode 8: [Today's MLS Pull → 5 Social Posts](https://youtube.com/@theprosperityagents)

## Compliance notes

Loads [`fair-housing-overlay`](../fair-housing-overlay/). Social-specific rules:
- Every post mentions OBJECTIVE property facts (price, sqft, DOM, year)
- No post implies neighborhood quality by demographic
- LinkedIn post is the highest-FH-risk because longer-form invites editorial framing — extra strict on never knocking competing brokerages
- TikTok script outline never includes agent on camera making quality judgments about buyers

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
