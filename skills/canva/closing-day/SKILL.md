---
name: canva-closing-day
description: Closing-day post + branded photo overlay + thank-you card. Three quick assets for the closing-table moment. Permission-aware — defaults to no-name, no-photo, no-price unless explicit client OK.
author: Al Pinder, Victoria Pinder
version: 1.0.0
---

# Canva Closing Day

Every closing is a chance to celebrate the client AND grow your social presence. This skill produces the post + the branded photo overlay + the printed thank-you card in one pass — all permission-aware.

## When to use this skill

A deal closed today. You took (or have permission for) a closing-table photo. You want to:
- Post the celebration on social
- Send a printed thank-you to the client (mail or hand-delivered)
- Add a branded overlay to the closing photo for your own portfolio

## Prerequisite

Run [`canva-foundation`](../foundation/) first. Assumes folder `08-Closing-Day/` with templates:
- `CD-Square-Photo-Overlay` (1080×1080)
- `CD-Card-Letterpress` (5×7 printed thank-you)
- `CD-Branded-Photo-Frame` (overlay frame for any aspect ratio)

## Prompt

```
I'm a real estate agent. Closing day. Build the closing-day package.

CLOSING DATA:
- Property address: [ADDRESS]
- Side I represented: [buyer / seller / both]
- Closing date: [DATE]
- Client first name (only if permission): [NAME or "no permission"]
- Closing-table photo permission: [yes / no]
- Photo URL (if available): [URL]

REQUIRED COMPLIANCE (load fair-housing-overlay):
- DEFAULT to NO first names + NO photos + NO sale price unless explicit
  client opt-in
- NEVER references school district / demographics / "smart move"
- ALWAYS positive about property + neighborhood

OUTPUT — three pieces:

1. INSTAGRAM SQUARE CAPTION (≤2200 chars)
   - If permission=yes for first name + photo: warm celebration mentioning
     [NAME] and the property
   - If permission=no: anonymous celebration ("we just closed in [CITY] on
     a [TYPE] home that...")
   - 5 hashtags: 1 city, 1 #ClosedToday, 1 listing-attribute, 1 brand,
     1 buy-side or sell-side
   - License + brokerage footer line

2. CANVA BRIEFS:
   a) CD-Square-Photo-Overlay (if permission=yes for photo)
      - Photo: closing-table image
      - Top overlay: "[CITY] · [DATE]"
      - Bottom overlay: "Welcome home, [NAME]" (only with permission)
      - License + brokerage footer
      - EHO logo small in corner
   b) CD-Branded-Photo-Frame (always available, overlays any photo)
      - Edge frame with brand colors + agent name + neighborhood + date
      - Useful even when client opts out of caption mention

3. CD-CARD-LETTERPRESS (5×7 printed thank-you):
   - HEADLINE: "Welcome Home" or "Congratulations" (pick one based on
     buyer-side / seller-side)
   - HANDWRITTEN-STYLE BODY: 4 sentences personal (Claude generates a
     warm, specific note based on the client context)
   - SIGNATURE: agent name (handwritten font)
   - BACK: agent contact + license + EHO logo + "Thank you for trusting us"

Write all three.
```

## What good output looks like

Three assets that respect the client's privacy by default + give you real options when permission is granted. The post drives engagement; the photo overlay grows your portfolio; the card builds the relationship for future referrals.

## Variants

**Investor closing:**
Add: `Skip the closing-table photo. Replace post + card with "ROI insight" framing — what made this deal work. Keep tone educational, not celebratory.`

**Difficult closing (long DOM, multiple back-on-market events):**
Add: `Frame as perseverance — "we worked through it together" without disclosing what "it" was. Never imply the buyer/seller/other agents were difficult.`

**Multi-deal day (2+ closings same day):**
Add: `Combine into a single post: "Big day in [CITY] — closed on [N] homes." Each gets a quick anonymized stat. Permission still per-client for any names/photos.`

## Companion content

YouTube Episode 19: [Closing Day Card + Branded Photo Overlay](https://youtube.com/@theprosperityagents)

## Compliance notes

Loads [`fair-housing-overlay`](../../fair-housing-overlay/). Closing-day-specific rules:
- Sale price is PRIVATE FINANCIAL DATA in some states — never displays unless explicit permission
- Client photos require explicit consent under most state real-estate-license laws
- Never frames closing as a "win" against another buyer/seller (NAR Code violation)
- Thank-you card body never references protected-class language ("nice family", "young couple")

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
