---
name: canva-just-sold
description: Generates the Just Sold post package + a testimonial-request DM/email for the buyer or seller. Three captions (IG/FB/TikTok) + Canva briefs for square + story formats. Permission-aware.
author: Al Pinder, Victoria Pinder
version: 1.0.0
---

# Canva Just Sold

Every closing deserves a post. This skill writes three platform captions + Canva briefs for both square and story formats, then drafts the testimonial request that follows up 7-14 days post-close.

## When to use this skill

A deal closed today. You want to celebrate the client + grow your social presence + open the door for a testimonial request. You've confirmed (or not) whether the client is OK with public mention.

## Prerequisite

Run [`canva-foundation`](../foundation/) first. Assumes folder `03-Just-Sold/` exists with templates `JS-Square-Photo-Forward` (1080×1080) and `JS-Story-Photo-Forward` (1080×1920).

## Prompt

```
I'm a real estate agent. A deal just closed. Build the Just Sold package.

CLOSING DATA:
- Property address: [ADDRESS]
- Side I represented: [buyer / seller / both]
- Closing date: [DATE]
- Days under contract: [N]
- Sale price: [$X — INCLUDE ONLY IF CLIENT GAVE EXPLICIT PERMISSION]
- Property type: [single-family / condo / townhouse / land]
- Cover photo URL: [URL or "use closing-table photo"]
- Closing-table photo permission: [client OK'd photo + first name? yes / no]

CLIENT FIRST NAME (only use if permission=yes): [NAME]

REQUIRED COMPLIANCE (load fair-housing-overlay):
- DEFAULT to NO first names + NO sale price unless explicit permission
- NEVER reference school district / demographics in the celebration copy
- NEVER imply the client made a "smart" or "savvy" decision (could be
  read as steering)
- ALWAYS positive about the property + neighborhood

OUTPUT — five pieces:

1. INSTAGRAM SQUARE CAPTION (≤2200 chars)
   - Celebrates closing without disclosing private financial details
   - Mentions client first name only if permission=yes
   - 5 hashtags: 1 city, 1 state, 1 #JustSold, 1 listing-attribute, 1 brand

2. FACEBOOK POST (≤700 chars, ends with congratulations)

3. TIKTOK CAPTION (≤300 chars, 5 hashtags)
   - Pairs with a 15-sec video showing closing-day photo or property exterior

4. CANVA BRIEFS:
   a) JS-Square-Photo-Forward
      - HEADLINE: "Just Sold in [CITY]"
      - SUBHEAD: [B] bed [B] bath / [PRICE if permission=yes else "Sold"]
      - Photo: [closing-table OR property exterior — cite the right one]
      - License + brokerage line: auto from Brand Kit
   b) JS-Story-Photo-Forward
      - Same content, vertical layout
      - Add "Swipe up" CTA → testimonial request flow

5. TESTIMONIAL-REQUEST DM (post-close, 3-5 sentences)
   Sent 7-14 days after closing. Asks ONE specific question (not generic
   "leave a review"). Includes a Google review link AND a Zillow link.

Write all five.
```

## What good output looks like

A celebration post that respects the client's privacy by default + a follow-up that actually generates testimonials because it asks specifically.

## Variants

**Co-listed (multi-agent representation):**
Add: `Tag the co-listing agent in caption. Photo includes both agents if permission given. Compliance: never disparage their contribution.`

**Difficult deal (long DOM, multiple back-on-market events):**
Add: `Frame the closing as a perseverance moment. Use specific "we worked through X, Y, Z" framing without disclosing what those issues were. Never imply seller, buyer, or other agents were difficult.`

**Investor closing:**
Add: `Caption shifts to ROI/cap-rate language. Replace celebration angle with educational ("here's what made this deal work for the investor"). Don't post closing-table photos — investor relationships favor privacy.`

## Companion content

YouTube Episode 14: [Just Sold + Testimonial Request](https://youtube.com/@theprosperityagents)

## Compliance notes

Loads [`fair-housing-overlay`](../../fair-housing-overlay/). Just-Sold-specific rules:
- Sale price is PRIVATE FINANCIAL DATA in some states — default to "sold" unless explicit permission
- Client photos require explicit consent under most state real-estate-license laws
- Testimonial request must NOT pressure the client (TCPA-adjacent for repeat-asks)
- Never frames the closing as a "win" against another buyer/seller (NAR Code violation)

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
