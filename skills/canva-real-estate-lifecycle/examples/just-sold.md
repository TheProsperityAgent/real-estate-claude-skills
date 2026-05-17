# Stage 5 — Just Sold (example run)

Every closing deserves a post. This stage writes three platform captions + Canva briefs for square + story formats, then drafts the testimonial-request DM that follows up 7-14 days post-close. Permission-aware by default.

## The permission gate

Default behavior: NO first names, NO sale price, NO client photo unless you explicitly flip the permission flag to `yes`.

This is intentional. Sale price is private financial data in some states. Client first names and photos require explicit consent under most state real-estate-license laws. The skill refuses to bridge over the gate even if your prompt forgets to mention it.

## The prompt

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
- Closing-table photo permission: [yes / no]

CLIENT FIRST NAME (only use if permission=yes): [NAME]

REQUIRED COMPLIANCE (load fair-housing-overlay):
- DEFAULT to NO first names + NO sale price unless explicit permission
- NEVER reference school district / demographics in celebration copy
- NEVER imply the client made a "smart" or "savvy" decision
- ALWAYS positive about property + neighborhood

OUTPUT — five pieces per the spec in SKILL.md.
```

## A real run — buyer-side, anonymized

**Inputs:**
- Address: 1234 Honeysuckle Lane, Greenville NC
- Buyer-side
- Closed May 14
- 38 days under contract
- Sale price: withheld (permission=no)
- Single-family
- Cover photo: property exterior
- Closing-table photo permission: no
- Client first name: blank (permission=no)

**Output — Instagram square caption (anonymous celebration):**

> We just helped a buyer close in Greenville. Single-family on a fenced lot, screened porch off the back. 38 days from contract to closing — the financing piece went smoothly thanks to a calm lender on the file.
>
> Every closing is a real moment for the family that just got the keys. We do not take it lightly. Quietly happy for them today.
>
> #Greenville #NorthCarolina #JustSold #ScreenedPorch #PinderTeam

Notice: no first name, no sale price, no "smart move" framing.

**Output — Facebook post (anonymous):**

> One more closing in Greenville today. A buyer we have been working with for a few months got their keys to a 4-bedroom with a screened porch. The financing piece went smoothly thanks to a calm lender on the file. Quietly happy for them.

**Output — TikTok caption (anonymous, 15-second video):**

`Just helped a buyer close in Greenville. Quietly happy for them. #JustSold #Greenville #NorthCarolina #PinderTeam #ScreenedPorch`

Video script: 15-second pan of the property exterior with a soft caption overlay `Just Sold · Greenville`. No closing-table footage.

**Output — Canva brief (JS-Square-Photo-Forward, 1080×1080):**

- HEADLINE: `Just Sold in Greenville`
- SUBHEAD: `4 Bed · 3 Bath · Single-Family` (no price because permission=no)
- HERO PHOTO: property exterior (not closing-table because permission=no)
- FOOTER: License + brokerage + EHO logo

**Output — Canva brief (JS-Story-Photo-Forward, 1080×1920):**

Same content, vertical layout, SWIPE-UP-CTA: `See more Greenville closings`

**Output — Testimonial-request DM (sent 7-14 days after closing):**

> Hi — Al here. Hope settling in went smooth. One ask: would you spend two minutes leaving a review on Google? It really helps other Greenville buyers find us. Here's the link: [GOOGLE REVIEW LINK]. And on Zillow if you have a minute: [ZILLOW LINK]. Either is plenty — no pressure, and thanks again for trusting us with the house hunt.

Asks ONE specific question (not generic "leave a review"). Includes both Google and Zillow links. No follow-up press if they don't respond — TCPA-adjacent for repeat-asks.

## Variant — when permission IS given

If the client tells you `yes` on first name + sale price + closing-table photo, you flip the flags and the output becomes:

> We just helped Sarah close on her new place in Greenville. $385,000, 4 beds, screened porch, fenced yard. 38 days from contract to keys. Welcome home, Sarah.

Canva brief uses the closing-table photo with `Welcome home, Sarah` as the bottom overlay.

The skill never auto-promotes from `no` to `yes`. You have to flip the flag in the prompt.

## Before / After — the permission rule

**Before this skill (the way most agents post):**

> Just sold! Sarah and Tom closed on their new place at 1234 Honeysuckle for $385K — what a smart move in this market! They couldn't be happier!

What just happened:
- First names without explicit OK to publish.
- Sale price without explicit OK to publish — possible state law violation depending on your state.
- "Smart move" — could be read as steering ("smart" implies they were smarter than other buyers — coded demographic implication).
- "Couldn't be happier" — invented quote, NAR Code issue.

The skill default output for the same facts (without explicit permission) does not name them, does not disclose price, does not put words in their mouth, and still celebrates the closing.

## Time saved

From-scratch: 30 minutes to write 3 captions + design 2 Canva pieces + draft testimonial ask.
With the skill: 5 minutes start to ship.
