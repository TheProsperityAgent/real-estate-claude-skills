# Failure modes — listing-description

## 1. Word count drifts to 180+

**Symptom:** Output is 175 words instead of 145.

**Why:** Claude soft-targets word counts. With property data that has 5+ standout features, it expands.

**Fix:** Re-emphasize in the prompt: `145 words EXACTLY. If you can't fit, cut features — never expand past 145.` Also: pick 3 standout features only, not 5. The skill is built around 3.

## 2. Closing line defaults to schools

**Symptom:** Body is clean, but the closing line says "Located in a desirable Greenville school district."

**Why:** No anchor passed in the prompt. Claude defaulted to schools because schools are the most common geographic anchor in real estate copy.

**Fix:** Always pass `Major nearby employer / park / amenity for the close: [SPECIFIC ANCHOR]`. Examples:
- `ECU Health Medical Center` (12 min)
- `Wildwood Park` (quarter-mile walk)
- `Five Points downtown` (six blocks)
- `Crystal Coast` (90 min)

Pick whichever is most relevant to the listing's likely buyer profile.

## 3. Skill returns description with a banned cliché

**Symptom:** Output contains "must-see" or "hidden gem".

**Why:** `fair-housing-overlay` not loaded alongside.

**Fix:** Always load both:
```
/skill load fair-housing-overlay
/skill load listing-description
```

The overlay is the FH/MLS-quality floor. The listing-description skill is the writer. Load them together.

## 4. Luxury variant produces hype-y output

**Symptom:** You set the luxury variant and the output reads "STUNNING LUXURY ESTATE in an EXCLUSIVE Greenville enclave!"

**Why:** Luxury tone drifted to hype.

**Fix:** The luxury variant template specifies: `Tone shifts to specific-detail-rich, longer sentences, restraint over hype.` If the output reads hype-y, re-emphasize: `RESTRAINT. Specific details. NEVER "stunning", NEVER "exclusive", NEVER "luxury" as an adjective for the home itself.`

Luxury buyers prefer restraint. Hype reads as desperation at $1M+.

## 5. Investor variant returns a residential close

**Symptom:** You set the investor variant. Body is investor-friendly, but the closing line says "Wonderful family-friendly neighborhood."

**Why:** Variant template wasn't fully applied to the closing line.

**Fix:** The investor variant specifies: `Replace city-life close with cap rate (if known), monthly rent (if known), or 1-mile rental comp range.` Pass the data:
```
Cap rate (if known): 7.2%
Monthly rent comp range: $1,950-$2,150
```

Claude will use those numbers in the close. If you don't have them, do a rental-comp pull first, then pass.

## 6. Run on commercial or land property

**Symptom:** Output reads weird because the skill is residential-trained.

**Why:** Commercial and land valuation logic differs significantly from residential. The 145-word residential cap and the closing-line localization don't fit commercial.

**Fix:** Don't use this skill for commercial or land. Hand-write the description, use this repo's `fair-housing-overlay` for compliance, but don't run `listing-description` itself.

## 7. Description contradicts a feature in the photo

**Symptom:** Description says "screened back porch" but the listing photos show a covered (not screened) porch.

**Why:** You typo'd or mis-stated the feature in the prompt input.

**Fix:** Always proof the output against the listing photos before pasting into MLS. The skill writes from what you tell it; if you tell it wrong, it writes wrong.

## 8. Description mentions the seller by name

**Symptom:** Body includes "The Smith family's beautifully maintained home" or similar.

**Why:** You passed the seller's name in the prompt and Claude included it.

**Fix:** Never pass the seller's name. Listing descriptions are for the property, not the seller. The seller's privacy and the buyer's anonymity are both protected by depersonalizing the description.

## 9. The "single-story living right there if you want it" voice creeps in everywhere

**Symptom:** Your second, third, fifteenth listing description all use the same construction: "X is right there if you want it."

**Why:** Claude latches onto a pattern and over-uses it across runs.

**Fix:** Rotate phrasings in your prompt. Add: `Vary your sentence constructions across listings — never use the same hook pattern (e.g., "X is right there if you want it") more than once a week.`

Or maintain a "patterns used recently" list and pass to the prompt as a "do not use again" block.

## 10. Output reads as AI-generated to a sharp listing agent

**Symptom:** Another agent in your office reads your description and says "this is AI." Subtle tells.

**Why:** AI-generated copy has tells: over-balanced sentence lengths, "moreover/furthermore" connectors, perfectly even bullet rhythm, lack of small specificity ("the screened porch ceiling fan that the prior owner left").

**Fix:** Add a tiny human-specific detail to every description before pasting:
- "The ceiling fan in the primary suite stays."
- "The kitchen pendants are from Mitchell Lighting in Wilson."
- "The 50-year-old oak in the side yard."

One small specific detail per description and the AI signal drops to zero.
