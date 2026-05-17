# Failure modes — mls-to-social

## 1. IG caption includes a link

**Symptom:** IG caption ends with `Tour info at https://yoursite.com/listings/honeysuckle`.

**Why:** Skill drifted from the no-link-in-IG-caption rule.

**Fix:** Re-emphasize: `Instagram caption: NEVER include a link. Algorithm downranks. Route traffic via bio link OR "Comment HONEYSUCKLE for the photo set".`

This is the single most impactful platform-specific rule. Links in IG captions tank reach.

## 2. Hashtag count drifts to 30 on Instagram

**Symptom:** IG caption has 15-25 hashtags.

**Why:** Claude knows IG allows up to 30. It pushes toward the cap.

**Fix:** The skill specifies 5 hashtags exactly. Re-emphasize: `5 hashtags total. Not more. Algorithm-optimal in 2026.`

If you absolutely must use more for a brand-coverage reason, max 10. Never go past.

## 3. LinkedIn post knocks a competitor

**Symptom:** LinkedIn post says "Unlike Zillow's iBuyer model..." or "Compass agents typically lack..."

**Why:** Long-form invites editorial framing. Claude defaulted to comparison.

**Fix:** The skill enforces always-positive on LinkedIn (highest-FH-risk platform). Re-emphasize: `Never knock competing brokerages, platforms, or agents. Constructive only — what we do, not what others don't.`

If the output drifts, the prompt template was overridden. Always-positive is non-negotiable on every platform but doubly enforced on LinkedIn.

## 4. TikTok script puts agent on camera making demographic comments

**Symptom:** TikTok script outline includes Al saying "Perfect for a young family looking to settle in Greenville!"

**Why:** Video scripts can drift to demographic targeting because that's how a lot of agent TikTok content is structured.

**Fix:** The skill enforces: `TikTok script outline NEVER includes agent on camera making quality judgments about buyers.`

Video focus: property features + voiceover anchored on objective facts. Agent on camera optional, but when present, NEVER comments on who the buyer should be.

## 5. Every post says "must-see"

**Symptom:** All 5 platform outputs include "must-see" or similar MLS scarcity cliché.

**Why:** `fair-housing-overlay` not loaded.

**Fix:** Always load alongside:
```
/skill load fair-housing-overlay
/skill load mls-to-social
```

The overlay catches "must-see" + "won't last" + "act now" + the rest of the MLS data-quality blocklist.

## 6. Post says "in a great area" with no real number

**Symptom:** Caption reads "Beautiful 4-bedroom in a great area of Greenville."

**Why:** No specific data anchor.

**Fix:** Every post MUST include a real number — price, sqft, lot, DOM, year built, commute time. The skill enforces this. If output drifts vague, pass cleaner MLS data so the skill has numbers to anchor on.

## 7. Just-sold variant uses present-tense "available now" framing

**Symptom:** You set just-sold variant. Output says "available now in Greenville" or "stop by this weekend."

**Why:** Variant block not fully applied.

**Fix:** Use the explicit just-sold variant block from `SKILL.md`:
> Replace "today's new listings" with "this week's closings." Skill rewrites all 5 posts as celebration content.

The tense + intent flip. If output is still active-listing tense, the variant block was missed.

## 8. Daily IG cap-bunching

**Symptom:** You ship a Just Listed via this skill at 9 AM. Two hours later, an Open House post fires. FB downranks both because they bunched.

**Why:** Two scheduling sources running independent of `metricool_dedup.py`.

**Fix:** Every scheduler imports `metricool_dedup.next_open_slots()`. The dedup module returns slots ≥90 min apart. If multiple skills are pushing to the same brand IG account, they all need to use the same dedup ledger.

## 9. Saturday market-recap variant runs as daily

**Symptom:** You meant to run weekly market-recap but didn't pass the variant. Output reads daily.

**Why:** Variant block missed.

**Fix:** Saturday market-recap variant from `SKILL.md`:
> This is a weekly recap, not daily. Show 7 days of activity: how many new, how many sold, median DOM, median sold price. Same 5-platform output, weekly framing.

Pass weekly data, not daily. Add the variant block.

## 10. Buyer-cohort variant produces generic posts

**Symptom:** You set buyer cohort = "ECU Health relocators." Output is just standard daily MLS posts.

**Why:** Variant not actually applied.

**Fix:** Buyer-cohort variant block from `SKILL.md`:
> Frame all 5 posts around the buyer cohort. Use commute times to ECU Health, not generic city names. Tone shifts to relocator-helpful.

Pass:
```
buyer_cohort: ECU Health relocators (medical professionals moving to Greenville)
buyer_cohort_anchor_employer: ECU Health Medical Center
```

The 5 posts should now all anchor on commute time to ECU Health, not generic Greenville framing. Test the IG caption first — should mention "12 min to ECU Health" prominently.
