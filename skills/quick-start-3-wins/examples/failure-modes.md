# Failure modes — quick-start-3-wins

## 1. Loading the skill without `fair-housing-overlay`

**Symptom:** Output includes "must-see" or "great schools" or "family-friendly" — phrases the overlay would block.

**Why:** You loaded `quick-start-3-wins` alone. The 3 prompts have compliance-conscious phrasing in their templates, but the overlay is what catches edge cases.

**Fix:** Always load BOTH:
```
/skill load fair-housing-overlay
/skill load quick-start-3-wins
```
Make it muscle memory. The overlay is the floor.

## 2. Pasting MLS data with ALL-CAPS headers

**Symptom:** Output partially echoes ALL-CAPS — "Brand-new listing in GREENVILLE. FOUR-BEDROOM..."

**Why:** Claude sometimes mirrors the case it sees in inputs. Some MLS exports ship headers in ALL CAPS.

**Fix:** Clean the MLS data before pasting:
```python
import csv
with open("mls_export.csv") as f:
    rows = list(csv.DictReader(f))
for r in rows:
    print({k: v.title() if isinstance(v, str) and v.isupper() else v
           for k, v in r.items()})
```
Or just paste lowercase. Claude reads them all fine.

## 3. Word-count drift on Prompt 1

**Symptom:** Output is 200 words instead of 145.

**Why:** Claude soft-targets word counts. With a chatty input or an over-detailed property, it expands.

**Fix:**
- Re-emphasize the cap: `145 words EXACTLY. Strict. Cut filler before adding detail.`
- Or pass `--target-words 145 --hard-cap` if you've wrapped the prompt in your own script.
- 145 is the optimal MLS field length. Over 145 will get truncated by some MLS feeds.

## 4. Counter-offer email reads as too aggressive

**Symptom:** Prompt 2 output says "my buyer will walk if you don't accept" or "this is our final and best." The listing agent reads it as a threat.

**Why:** You added "I want to push hard" or "they're firm" to the prompt without specifying you also want collegial tone.

**Fix:** The default Prompt 2 includes `Tone: collegial, no pressure, respect their professionalism`. Don't remove that. If you want a harder posture, use the `Hard-no counter (we're walking)` variant in the SKILL.md file — that one has its own collegial-but-firm framing.

## 5. Social posts include emoji-spam

**Symptom:** IG caption has 8 emojis in the first line. Looks like AI-spam.

**Why:** Claude defaults to "engaging" social tone which can drift to emoji-heavy.

**Fix:** Add to the prompt: `Maximum 1 emoji per post. Plain prose, real numbers, no emoji spam.` Or strip emojis post-generation:
```python
import re
caption = re.sub(r"[^\x00-\x7F]+", "", caption)  # strips non-ASCII
```

## 6. Hashtag count drift

**Symptom:** IG caption has 12 hashtags instead of 5.

**Why:** Claude knows IG allows up to 30 hashtags. It pushes toward the max.

**Fix:** The default Prompt 3 specifies 5 hashtags exactly (1 city, 1 state, 2 listing-attribute, 1 brand). The 5-hashtag sweet spot is the 2026 algorithm-optimal count across IG + Facebook + TikTok. Don't override.

## 7. The listing description is great but the close line drifts back to schools

**Symptom:** Description body is clean, but the closing line says "Located in a desirable Greenville school district."

**Why:** Claude was trying to anchor the close geographically and defaulted to schools (which is the most common geographic anchor in real estate copy).

**Fix:** The Prompt 1 template specifies: `End with ONE line about [CITY] life that doesn't mention schools. Use commute time to a major employer, or a public park, or a downtown amenity, or a commute-to-coast time — all objective, all FH-clean.`

Pass an explicit anchor: `Major nearby employer / park / amenity for the close: ECU Health Medical Center`. Claude will use that instead of inventing one.

## 8. The buyer's name leaks into Prompt 2

**Symptom:** Counter-offer email mentions "John and Mary Smith" — your buyers — by name.

**Why:** You passed the names in the prompt and Claude included them.

**Fix:** Use anonymous references — "my buyer", "the buyer". Listing-side communication should reveal only what's needed. The buyer's identity is private until the contract is signed and even then is generally only shared as needed.

## 9. Trying to run this skill for a non-real-estate client

**Symptom:** You ask Prompt 3 to generate posts for a furniture business. The output is OK but generic — doesn't have the real-estate domain bias the skill was built for.

**Why:** This skill is real-estate-specific. The compliance rules (FHA, TCPA, MLS data quality) are real-estate context.

**Fix:** Use a general-purpose copywriting skill for non-real-estate work. This repo is purpose-built; don't bend it.

## 10. Compliance happened, but the resulting copy is bland

**Symptom:** The output is compliant — but it reads flat. No personality. No voice.

**Why:** The default skill output prioritizes compliance over voice. That's the right tradeoff for a "I just installed Claude" first day — compliant > stylish.

**Fix:** Once you're comfortable with the compliance floor, layer your brand voice on top:
1. Run Prompt 1 to get the FH-clean structure.
2. Add a 2-sentence "rewrite in [your brand]'s voice" pass.
3. Your voice + compliance floor. The skill isn't fighting your style — it's giving you a clean substrate.

Or graduate to `listing-description` (skill 3 in the season) which has variant prompts for luxury, new construction, and investor-targeted that build in tone.
