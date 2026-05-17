# Failure modes — canva-real-estate-lifecycle

The mistakes Victoria has seen agents make running this skill in the wild. Each one comes with a fix.

## 1. Running a stage prompt before the one-time foundation setup

**Symptom:** Canva brief references templates like `JL-Square-Photo-Forward` that do not exist in your Canva account. You open the named folder and there's nothing in it.

**Why:** The Brand Kit + folder structure + template-naming convention is a prerequisite. Every stage prompt assumes it's in place.

**Fix:** Run the foundation prompt from `SKILL.md` once. Build the 7 folders + 99-Brand-Kit. Build one template per stage following the naming convention. From here on, every stage prompt will land on real templates.

## 2. EHO logo missing on the printed flyer

**Symptom:** You ship a Just Listed flyer or an Open House flyer without the Equal Housing Opportunity logo. A broker or compliance officer flags the print run.

**Why:** Most agents put EHO on the listing description but skip it on the marketing collateral. FHA + most state license-board rules require it on every printed real estate marketing asset.

**Fix:** Add the EHO logo to your Canva Brand Kit's `99-Brand-Kit` folder as a transparent PNG. Every template should pull it in as a locked footer element. If a template you built is missing it, fix the template once — every future flyer inherits the fix.

## 3. Q3 quarterly postcard slipping into "back to school" framing

**Symptom:** You write the August/September quarterly postcard. The phrase "back to school in [NEIGHBORHOOD]" or "time for the kids to..." sneaks into the body. Compliance reviewer catches it.

**Why:** "Back to school" is a familial-status / age-of-child implication and a FHA concern on geographic-farm marketing. Most agents don't realize this is a violation because it feels innocuous.

**Fix:** Use the skill's enforced framing — "season transition", "mid-year", "fall pace". Never reference school timing, school year start, or "the kids". The Q3 example file (`examples/quarterly-postcard.md`) has the exact compliant phrasing.

## 4. Just Sold post with sale price + client first name on permission=no

**Symptom:** You post a Just Sold celebration with `Sarah and Tom closed for $385K!`. Either the client or another party in the deal flags it. State license board gets a complaint.

**Why:** Sale price is private financial data in some states. Client first names require explicit consent under most state real-estate-license laws. The skill defaults to NO unless you flip the permission flag to YES — you bypassed the default.

**Fix:** Default the prompt's permission flags to `no`. Only flip to `yes` after the client has explicitly OK'd it in writing (text message screenshot, signed waiver, or email reply). When in doubt, ship the anonymous variant — same engagement, zero risk.

## 5. Coming Soon teaser with the full street address visible

**Symptom:** You ship a Coming Soon flyer or IG post with `1234 Honeysuckle Lane — listing Sunday`. An MLS compliance officer notices the public marketing pre-MLS-go-live.

**Why:** NC, FL, NY, CA all have rules that listings cannot claim "available" status until they hit the MLS. Putting the full street address on a public pre-launch teaser is the most common way agents accidentally cross this rule.

**Fix:** Coming Soon stage outputs strip the street address by default — neighborhood-level only. Drive interested buyers to a launch-alert opt-in (comment SUNSET, subscribe at landing page) so when MLS goes live they get the alert through a compliant channel.

## 6. Price Reduced post discloses seller motivation

**Symptom:** You post `Owner motivated — must sell this week! Was $385K, now $375K.` A buyer-side agent screenshots it and uses it as leverage at the negotiation table.

**Why:** "Owner motivated" / "must sell" publicly discloses the seller's motivation. NAR Code of Ethics Article 11. Fiduciary issue. Also a buyer-side gift wrapped in a bow.

**Fix:** Use the skill's enforced framing — "Updated pricing", "Fresh number", "Same house, fresh opportunity". Body focuses on WHAT BUYERS GET at the new price, never on what changed about the seller. Re-read `examples/price-reduced.md` before posting any reduction.

## 7. Skipping the door-knocking script on the Open House stage

**Symptom:** You ship the OH flyer + IG + FB but skip the door-knocking 30-second script. Walk the neighborhood with the flyer cold and improvise — and the improvised pitch drifts off-compliance.

**Why:** Improvisation under pressure tends to default to scarcity language ("you don't want to miss it") or demographic assumptions ("perfect for a family"). Both violate.

**Fix:** Print the door-knocking script with the flyer. Keep it in your jacket pocket. The 30-second structure (10-sec opener + 10-sec invite + 10-sec close) is short enough to internalize after two doors.

## 8. Forgetting `metricool_dedup.py` when you ship 4 designs in one morning

**Symptom:** You ship Just Listed + Open House + Price Reduced + Just Sold posts in the same morning. Two land in the same Metricool time slot. Facebook downranks both because it reads them as bunched.

**Why:** Your scheduler imported `metricool_dedup.py` but you scheduled manually that morning and bypassed the dedup check.

**Fix:** Always schedule through a scheduler that imports `metricool_dedup.py`. Never paste posts directly into Metricool's UI on a day you're shipping 3+ designs. The dedup ledger is what spaces them across the day.

## 9. Canva exporting at the wrong dimensions and the post fails to schedule

**Symptom:** You design `JL-Square-Photo-Forward` but export at 800×800 instead of 1080×1080. Metricool holds the post because the dimensions don't match the platform spec.

**Why:** Canva's default export resolution is sometimes lower than the platform spec. Easy to miss.

**Fix:** `metricool_image_sweeper.py` runs twice daily (8:30 AM + 4 PM) and normalizes images Metricool held due to size or aspect-ratio issues. Either fix the Canva export OR trust the sweeper to catch it — but never both manually and the sweeper at the same time (you'll race the cron).

## 10. Trying to use Canva for the listing presentation or CMA cover

**Symptom:** You build a 12-slide Canva deck for the listing presentation. Seller opens it on their phone after the meeting and the layout breaks.

**Why:** Canva slide decks shared as PDF lose the kitchen-table read on mobile. They were built for visual posts, not narrative reads.

**Fix:** Move to Gamma. Run `listing-presentation-builder` for the brief, drop into Gamma. Same Brand Kit (you uploaded it to Gamma in the foundation step). The shareable URL renders on phone, tablet, and laptop. See `README-on-canva-vs-gamma.md` in this folder.
