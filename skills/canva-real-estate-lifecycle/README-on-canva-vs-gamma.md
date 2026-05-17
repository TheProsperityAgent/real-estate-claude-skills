# Canva vs Gamma — read this once, never confuse them again

Two design tools, two different jobs. The split matters because most agents try to use one for both and the result is always worse than picking the right tool per job.

## Canva — the listing-marketing tool

Use Canva for everything in this lifecycle skill:

- Just Listed posts (IG square, IG story, flyer)
- Open House (flyer, yard rider, IG/FB/TikTok)
- Just Sold (square, story)
- Coming Soon (square, story)
- Closing Day (post, branded photo overlay, 5×7 card)
- Price Reduced (square, story)
- Quarterly Postcard (front + back, 6.5×9 EDDM)

Why Canva for these: they are visual-heavy, social-first, and they ship to feed algorithms that prefer fixed aspect ratios. Canva is built for that. You make one template, you swap the photo and text, you export. Fast.

## Gamma — the seller-meeting tool

Use Gamma instead of Canva for:

- Listing presentations (delegated to `listing-presentation-builder` skill)
- CMA covers + CMA write-up (delegated to `cma-narrative` skill)
- Pre-listing valuations (CMA variant)
- Seller meeting decks (kitchen-table read)

Why Gamma for these: they are text-heavy, narrative-driven, and the seller usually reads them on their own — sometimes on a phone, sometimes on a laptop. Gamma renders responsively, gives you a shareable URL, and the seller can scroll through it without you sitting next to them. Canva slide decks shared as PDF lose the kitchen-table read every time.

## Quick decision rule

Ask: "Is this a social post (algorithm-driven) or a seller-read deck (human-driven)?"

- Social post → Canva.
- Seller deck → Gamma.

## Why the older repo had Canva listing-presentation + Canva CMA cover skills

In an earlier draft of this repo, we had `canva/listing-presentation` and `canva/cma-cover`. We dropped them on 2026-05-17. Victoria runs all her real listing presentations and CMAs out of Gamma now — it converts better in the kitchen-table moment and lets the seller open the deck from their phone after you leave.

If you see references to those older Canva skills in old YouTube episode descriptions or older blogs, ignore them. The current canon is `listing-presentation-builder` → Gamma and `cma-narrative` → Gamma.

## Both tools, same Brand Kit

The Brand Kit you set up in the foundation step (logo variants, palette, fonts, footer block, EHO logo) gets uploaded to Gamma's brand settings too. Same hex codes, same fonts, same footer. The visual identity survives the move between tools.
