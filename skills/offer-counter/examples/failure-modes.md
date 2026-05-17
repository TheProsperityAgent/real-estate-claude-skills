# Failure modes — offer-counter

## 1. Reply leaks buyer's financing ceiling

**Symptom:** Output says "buyer is approved up to $400K but..." or "their max is $385K."

**Why:** You passed the buyer's pre-approval ceiling in the prompt as context, and Claude included it.

**Fix:** Never pass financing ceilings in the prompt. The skill needs only the buyer's CURRENT offer and the seller's COUNTER. Financing context is internal-only.

## 2. Reply contains "young couple" or other demographic shorthand

**Symptom:** Output describes the buyer as "young", "first-time", "growing family", etc.

**Why:** You passed buyer demographics in the prompt and Claude included them. (Or, less commonly, Claude inferred from context.)

**Fix:** Never describe the buyer in demographic terms. Pass property + offer terms only. Buyer's intent is "to acquire this home at this price" — anything else is private.

## 3. Reply reads aggressive when you wanted firm

**Symptom:** Output says "my buyer will walk if you don't accept" or "this is our final and best."

**Why:** Default skill is collegial. If you added "I want this strong" or "they're firm" to the prompt without specifying collegial tone, Claude amplified to hostile.

**Fix:** The default Prompt template specifies `Tone: collegial, no pressure, respect the listing agent's professionalism`. Don't strip that line. If you want firmer, use the "Hard-no counter" variant — that one is firm AND collegial.

## 4. Reply uses "we" when it should be "my buyer"

**Symptom:** Output blurs agent and buyer: "we are not raising."

**Why:** Sloppy attribution. Mixed.

**Fix:** The skill should always frame as "my buyer is going to hold at..." not "we are firm at..." The agent is the conduit, not the principal. The principal is the buyer. Listing agents read this distinction.

If outputs are blurring, add to prompt: `Always attribute positions to "my buyer", never "we" or "us".`

## 5. Reply offers a flex the buyer doesn't actually want to give

**Symptom:** Output offers to leave the appliances. Buyer never said they would.

**Why:** You didn't specify the flex options correctly in the prompt.

**Fix:** The prompt's flex section uses checkboxes:
```
- Signals flexibility on these terms (pick the ones that don't matter to my buyer):
  [ ] Close date
  [ ] Appliances (which appliances we'll let go)
  [ ] Minor inspection items (under $[THRESHOLD])
  [ ] Earnest money increase
  [ ] Settlement attorney choice
```

Check ONLY the flexes your buyer has explicitly agreed to. If the buyer hasn't decided about appliances, leave that box unchecked.

## 6. Reply mentions other offers when there aren't any

**Symptom:** Output says "given the other offers on this property..." or "in this competitive market..."

**Why:** Claude assumed multiple-offer context.

**Fix:** Specify offer count explicitly: `Single offer (not multiple-offer)`. Or use the explicit Multiple-offer variant if there are.

## 7. Sign-off is too casual or too formal

**Symptom:** Output ends with "Cheers" (too casual for a contract negotiation) or "Sincerely yours" (too formal for a small-market peer).

**Why:** Default sign-off drifts based on prompt context.

**Fix:** Specify: `Sign-off: warm but professional. "— Al" with em dash. Never "Cheers", never "Sincerely yours".`

Small-market peer convention: first name + em dash. Big-firm convention: full name + title.

## 8. Reply mentions a deadline you didn't set

**Symptom:** Output ends with "Please respond by 5 PM tomorrow."

**Why:** Claude defaults to including a deadline because the contract framework expects one.

**Fix:** Specify if you want a deadline OR not. If you don't, add: `No deadline — leave the timing open.` If you do, add: `Deadline: 5 PM tomorrow, but phrase it gently ("would appreciate a response by EOD tomorrow if possible").`

## 9. Reply is too long — buyer-side agent reads it as desperate

**Symptom:** Output is 12 sentences. Listing agent reads it as "Al is selling too hard."

**Why:** Default skill template says 4-6 sentences max, but you padded the prompt with too much context.

**Fix:** Trim the prompt. Pass the essentials:
- Buyer's offer.
- Seller's counter.
- Listing agent's name (if known).
- Your buyer's position (hold price).
- Flex options (3 max).

Strip prep notes, internal deliberations, anything else. Let the skill draft tight.

## 10. The skill produces a reply for the wrong side

**Symptom:** You're on the listing side, the skill drafted a buyer-side reply.

**Why:** Default skill is buyer-side. Listing-side is a variant.

**Fix:** Use the "Seller-side" variant from `SKILL.md`:
> Reverse the prompt — I'm the listing agent receiving a counter to my counter. Write the reply from the seller's side: hold price, identify which seller-side flex points to offer.

The shape of the negotiation flips. Add the variant block to your prompt.
