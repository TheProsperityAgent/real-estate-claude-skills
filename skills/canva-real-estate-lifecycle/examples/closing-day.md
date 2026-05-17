# Stage 6 — Closing Day (example run)

The closing-table moment. Three quick assets: the IG square post, a branded photo overlay you can keep for your own portfolio even if the client opts out of public mention, and a printed 5×7 thank-you card.

## The permission gate (same rule as Just Sold)

Default: NO first name + NO sale price + NO closing-table photo unless explicit client OK. The skill refuses to bridge over the gate even if your prompt forgets to mention it. Sale price is private financial data in some states. Client photos require explicit consent under most state real-estate-license laws.

## The prompt

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
- DEFAULT to NO first names + NO photos + NO sale price unless explicit opt-in
- NEVER references school district / demographics / "smart move"
- ALWAYS positive about property + neighborhood

OUTPUT — three pieces per the spec in SKILL.md.
```

## A real run — buyer-side, permission for first name + closing photo

**Inputs:**
- 1234 Honeysuckle Lane, Greenville NC
- Buyer-side
- Closed May 14
- Client first name: Sarah (permission=yes)
- Closing-table photo permission: yes
- Photo: family at closing table, keys visible

**Output — Instagram square caption:**

> Welcome home, Sarah. We just handed over the keys on 1234 Honeysuckle Lane in Greenville — four-bedroom on a fenced lot with the screened porch we kept talking about during showings. Thirty-eight days from contract to keys, smooth lender on the file, and a closing table that ran exactly on time. We are quietly happy for her family today.
>
> #ClosedToday #Greenville #NorthCarolina #ScreenedPorch #PinderTeam

**Output — Canva brief (CD-Square-Photo-Overlay, 1080×1080):**

- HERO PHOTO: closing-table image
- TOP OVERLAY: `Greenville · May 14`
- BOTTOM OVERLAY: `Welcome home, Sarah`
- License + brokerage footer
- EHO logo small bottom-corner

**Output — Canva brief (CD-Branded-Photo-Frame, any aspect ratio):**

Edge frame with brand colors + agent name + neighborhood + date. Useful even when the caption mention is anonymous — gives you a portfolio asset.

**Output — Canva brief (CD-Card-Letterpress, 5×7 printed thank-you):**

- HEADLINE: `Welcome Home`
- HANDWRITTEN-STYLE BODY (4 sentences):
  > Sarah —
  > Thank you for trusting us with the house hunt. We loved walking through Brook Valley with you and we are so glad Honeysuckle Lane is yours. Drop us a line anytime — recommendations on the lawn guy, the HVAC tune-up, or just to say hi.
  > — Al & Victoria
- SIGNATURE: handwritten-font agent name
- BACK: agent contact + license + EHO logo + `Thank you for trusting us`

## Variant — anonymous (permission=no)

If permission for first name + photo is no, you flip the flags and the output becomes:

**IG caption (anonymous):**
> We just handed over the keys on a Greenville closing today. Four-bedroom on a fenced lot, screened porch off the back. Thirty-eight days from contract to closing — smooth lender on the file. Quietly happy for the family today.
>
> #ClosedToday #Greenville #NorthCarolina #ScreenedPorch #PinderTeam

**Canva brief:**
- Use `CD-Branded-Photo-Frame` overlay on the property exterior (not the closing table).
- No "Welcome home, [name]" line.

**Card:**
- HEADLINE: `Welcome Home`
- BODY skips the first name, opens with `Thank you for trusting us with the house hunt`.

The card still goes in the mail — the client knows you, the card is one-to-one, no FH issue.

## Before / After — the closing-table photo trap

**Before this skill (the way most agents post):**

> Big day! Sarah and Tom Smith closed on their dream home today for $385K! Such a smart family — we knew this was perfect for them from the first showing! They couldn't be happier!

What just happened:
- Full name without explicit OK to publish.
- Sale price without explicit OK — state law issue in some states.
- "Dream home" — invented framing, NAR Code-of-Ethics concern.
- "Smart family" — coded demographic implication.
- "Perfect for them" — steering implication.
- "Couldn't be happier" — invented quote.

The skill's anonymous output (the variant above) gets the same engagement, respects privacy by default, and never invents words you'd have to defend.

## Time saved

From-scratch: 30 minutes for IG post + Canva overlay + printed card.
With the skill: 5 minutes.

The printed card is the differentiator. Most agents skip it because writing a personal 4-sentence note 12 times a month is friction. The skill writes the warm-specific draft you can copy onto the card by hand in 90 seconds.
