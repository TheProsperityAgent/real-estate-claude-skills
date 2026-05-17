# Failure modes — cma-narrative

## 1. Output drifts to "buyer's market" / "seller's market" framing

**Symptom:** Section 3 (Market Context) says "we're firmly in a seller's market right now."

**Why:** Generic real estate copy training pushes toward those labels.

**Fix:** Re-emphasize in the prompt:
> NEUTRALLY FRAMED. Numbers only. NEVER "buyer's market" or "seller's market." Median sold, DOM trend, active inventory — that's it.

The skill enforces this by default. If output drifts, the prompt template was overridden somewhere.

## 2. Section 1 reads as a comp grid table

**Symptom:** Output has comps in a table format with columns. Reads dry.

**Why:** Claude defaulted to a grid because that's how CMA data is commonly structured.

**Fix:** Re-emphasize: `Section 1 is PROSE. Walk the reader through each comp narratively. No table, no bullets, no comp-grid format.`

The whole point is that the seller can read it at the kitchen table. Tables require explanation; prose flows.

## 3. Recommended price doesn't align to seller motivation

**Symptom:** Seller said "relocating with a 60-day deadline." Skill recommended $395K (aggressive). Listing sits, misses the deadline.

**Why:** Skill didn't slot the recommendation to motivation.

**Fix:** Always pass seller motivation explicitly:
```
seller_motivation: relocating with a deadline (must close within 60 days)
```

The skill uses motivation to anchor the recommended slot:
- No rush → recommended = high end (aggressive).
- Relocating with deadline → recommended = low end (conservative, speed prioritized).
- Financial pressure → recommended = middle.

If you don't pass motivation, Claude guesses — and guesses wrong.

## 4. Comp distance not specified

**Symptom:** Section 1 says "1234 Honeysuckle Lane is a comp" but the home is 8 miles from the subject in a different town.

**Why:** Skill accepted the comps you gave it without verifying proximity.

**Fix:** Filter comps before passing:
- Same town: prioritized.
- Within 2 miles: acceptable.
- 2-5 miles: only if no closer comps exist; mention the distance.
- 5+ miles: not a comp.

Pass distance for each:
```
Comp 1: 1234 Honeysuckle Lane, sold $385K, 0.4 mi from subject
```

The skill includes distance in its narrative when you give it. If you don't, the comp grid distance assumptions are sloppy.

## 5. Section 4 only outputs one number, not three

**Symptom:** Output says "I recommend $389K" instead of low/recommended/high.

**Why:** Default skill outputs 3 numbers with rationale — if you got only 1, your prompt overrode it.

**Fix:** Re-emphasize: `Section 4 outputs THREE numbers: low, recommended, high. Each with 2-3 sentence rationale. Always three numbers.`

Three numbers give the seller a range to react to. One number reads as overconfident.

## 6. Investor variant treated as residential

**Symptom:** You set the investor variant. Section 3 still talks about median sold price.

**Why:** Variant block not applied to all sections.

**Fix:** Investor variant rewrites Section 3 AND Section 4:
> Replace section 3 (market context) with rental-comp section: monthly rent range for similar properties, current rental yield, 1-year forward projection. Replace section 4 with cap-rate-based price recommendation.

If only Section 3 changed, the prompt didn't apply the full variant. Use the explicit Variant block from `SKILL.md`.

## 7. Pre-listing variant pushed too hard

**Symptom:** Seller is just exploring — said "we MIGHT list in 6 months." Skill outputs an aggressive recommendation.

**Why:** Default skill aims to close the listing. Pre-listing should be educational.

**Fix:** Use the pre-listing variant:
> Tone shifts to educational rather than persuasive. Present 3 scenarios (price-aggressive / price-comp / price-conservative) with expected DOM each. Don't push a single recommendation.

The pre-listing variant treats the seller as a researcher, not a buyer of your services. Builds trust for the eventual real listing.

## 8. Output blocked by FH overlay because of comp's town

**Symptom:** A comp is in a town with a school name in it ("Oakwood School District subdivision"). Output blocked.

**Why:** Pattern match on "school district" without proper-noun masking.

**Fix:** Pre-mask proper nouns before generation. Maintain `~/content-agent/state/proper_nouns.txt` with subdivision names that contain trigger words. Mask, generate, unmask.

## 9. Commercial/land run through this skill

**Symptom:** You ran a 30-acre land CMA. Output reads weird — uses residential framing.

**Why:** This skill is residential-trained.

**Fix:** Don't use this skill for commercial or land. Hand-write the narrative, use `fair-housing-overlay` for compliance, use Gamma for the layout — but don't generate via `cma-narrative`. Valuation methodology differs significantly.

## 10. Comps include a sold price the seller will fixate on

**Symptom:** Comp 3 sold at $410K (higher than your recommended range). Seller fixates and says "well comp 3 sold at $410K so we should list at $410K."

**Why:** Comp 3 was included because it was the closest physical comp — but it has features the subject doesn't have (full finished basement, lake view, etc.).

**Fix:** Section 1 + Section 2 must explicitly state what differentiates the high-priced comp. If comp 3 has a lake view, Section 1 names it; Section 2 explains the lake-view premium isn't transferable.

Or drop comp 3 entirely. Better 3 comps that frame the right range than 4 comps that include an outlier seller will misuse.
