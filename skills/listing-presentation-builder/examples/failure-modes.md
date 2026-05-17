# Failure modes — listing-presentation-builder

## 1. Section 8 (Why Us) knocks a competitor

**Symptom:** "Unlike Compass, we actually return phone calls" or "Most agents in Greenville skip the social media step."

**Why:** Comparative framing slipped past the always-positive rule.

**Fix:** The skill enforces `"What we do" → "What that means for you"` format — POSITIVE statements only. Re-emphasize:
> Section 8 NEVER says "unlike", "most agents", "competitors", or "others don't". Frame as "Here's what we do that's specific" — not "Here's what others don't do."

NAR Code of Ethics Article 12 violation if a competing agent is named or implied.

## 2. Section 2 (Market Context) drifts to "seller's market" framing

**Symptom:** "Greenville is firmly in a seller's market" or "buyers should expect bidding wars."

**Why:** Generic real-estate copy training pushes toward those labels.

**Fix:** The skill enforces neutral framing. Re-emphasize:
> Section 2 NEVER uses "buyer's market" or "seller's market" or "bidding wars" or "hot market." Numbers only — median sold, DOM trend, active inventory.

If the market is moving fast, the data shows it. The label adds risk without value.

## 3. Cover slide missing license number or EHO logo

**Symptom:** Cover slide has your name + brokerage but no license # or EHO logo.

**Why:** Brand Kit wasn't fully built in the foundation step. License + brokerage line + EHO logo are non-optional on every printed/digital real-estate marketing asset.

**Fix:** Re-run the foundation step from `canva-real-estate-lifecycle`. Build the Brand Kit with:
- License number block (e.g., `NC #334543`)
- Brokerage block (e.g., `eXp Realty LLC`)
- EHO logo as a transparent PNG

Upload to Gamma's brand settings so every listing-presentation deck inherits them.

## 4. Pricing recommendation doesn't align to seller motivation

**Symptom:** Seller said "we have to be done in 60 days." Skill recommended high-end price aggressively.

**Why:** Motivation not passed to Section 6.

**Fix:** Pass motivation explicitly:
```
seller_motivation: must close within 60 days (relocation)
```

Section 6's recommended slot adjusts:
- "No rush" → recommended = high end (aggressive).
- "Relocating with deadline" → recommended = low end (conservative, speed prioritized).
- "Financial pressure" → recommended = middle.

## 5. Section 5 (Marketing Plan) references templates that don't exist

**Symptom:** Marketing plan says "we'll use template JL-Square-Photo-Forward" but you don't have that template in your Canva.

**Why:** You haven't run the `canva-real-estate-lifecycle` foundation step yet.

**Fix:** Run the foundation step first. Build the 7 folders + templates. Then Section 5's marketing plan references map to real templates.

If you don't use Canva at all (some agents prefer Photoshop or Figma), customize Section 5 to reference YOUR templates. Don't ship a deck that names templates you don't have.

## 6. Comp summary delegated but cma-narrative not run

**Symptom:** Section 3 is empty or generic because the prompt expected `cma-narrative` output to be passed in.

**Why:** Skill chaining requires you to run `cma-narrative` first AND pass its output as Section 3.

**Fix:**
1. Run `cma-narrative` with the 3-6 comps.
2. Take Section 1 (Comp Summary) of the cma-narrative output.
3. Paste it as Section 3 of the listing-presentation-builder prompt.

Or use Claude's chained-skill feature to call cma-narrative inline from listing-presentation-builder.

## 7. Luxury variant produces hype-y output

**Symptom:** You set luxury variant. Section 2 says "STUNNING LUXURY MARKET in EXCLUSIVE Greenville enclaves!"

**Why:** Luxury tone drifted to hype.

**Fix:** Luxury variant template specifies: `Tone shifts to specific-detail-rich, longer sentences, restraint over hype.` Re-emphasize: `RESTRAINT. Specific details. NEVER "stunning", NEVER "exclusive", NEVER hype adjectives.`

Luxury buyers prefer restraint. Hype reads as desperation at $1M+.

## 8. Section 7 (Timeline) is generic

**Symptom:** Timeline says "Week 1: list. Week 2: open house. Week 3: marketing. Week 4: review."

**Why:** Generic template defaults.

**Fix:** Re-emphasize: `Week-by-week with SPECIFIC actions. "Photos Tuesday. Listing live Thursday. Just Listed social fires Friday morning. Door-knocking Saturday." Not "list" — "Tuesday: photos shoot at 2 PM with [photographer name]."`

Specifics build the seller's confidence. Generic loses to whichever competing agent's timeline is specific.

## 9. Pre-listing CMA variant outputs 8 sections instead of 4

**Symptom:** You set the pre-listing variant. Output is still 8 sections.

**Why:** Variant block missed.

**Fix:** Pre-listing variant from `SKILL.md`:
> Drop sections 5, 6, 7 (marketing, recommendation, timeline). Output sections 1, 2, 3, 4 only as a CMA write-up.

Pre-listing is for sellers who are exploring, not signing. Less push, more education. Sections 5-7 are presumptive for a non-committed seller.

## 10. Gamma renders poorly because text was pasted with formatting

**Symptom:** Gamma deck looks broken — fonts inconsistent, line breaks wrong.

**Why:** You pasted with rich-text formatting from a doc instead of pasting plain text.

**Fix:** Always paste as plain text (Cmd+Shift+V on Mac) into Gamma. The deck's brand fonts/colors auto-apply. Rich-text formatting from Claude's output conflicts with Gamma's templates.

For repeat reliability, save the section content to a markdown file first, then paste from there:
```bash
echo "$section_text" > ~/Desktop/section_2.md
# Open in plain text editor, copy, paste into Gamma
```
