# PRODUCTION — offer-counter

The "9 PM after a long day, kitchen-table negotiation in progress" skill. The skill writes the email-reply half of every counter-offer in 40 seconds.

## Where this fits

You represent a buyer. The buyer made an offer. The listing agent countered. You need to reply.

The reply email is half of the negotiation. Most agents under-write it because they're tired. This skill writes it like a 20-year agent — collegial, no pressure, holds price, flexes on terms the buyer doesn't care about.

In Victoria's stack, the skill runs on-dispatch (per counter), not on a cron. Pair with `fair-housing-overlay` always.

## The actual run

```
8:47 PM — Listing agent sent the counter at 8:32 PM. Buyer offered $385K
          on a $405K list. Seller countered $395K with no other terms.
8:48 PM — Open Claude Code. Load fair-housing-overlay + offer-counter.
8:49 PM — Paste prompt with my buyer's position, the counter, and the
          terms-flex list (close date, appliances).
8:50 PM — Draft returns. 5 sentences. Collegial. Holds price, flexes
          close date. Leaves room for one more counter.
8:51 PM — Two small edits (added "by the way, my buyer can do all-cash
          if it helps the seller's certainty").
8:52 PM — Send via Gmail. Set follow-up for tomorrow morning if no reply.
```

5 minutes from "counter received" to "reply sent." The blank-screen version is 25-35 minutes (and the reply is usually weaker).

## Why the small market matters

The default prompt includes: `This is a small market where I work with [NAME] regularly. I don't want to bluff or make threats. I want to leave room for one more counter.`

This is non-negotiable in Victoria's stack. In a small market (Greenville-Winterville-Ayden is small) you work with the same 50 listing agents and 5 builders for 20 years. A counter-reply that burns a bridge today costs you for two decades.

The skill enforces collegial tone by default. If you want a harder posture, use the "Hard-no counter" variant — but even that one is collegial in tone, just firmer in position.

## When to use which variant

| Variant | When |
|---|---|
| Default | Standard buyer-side counter — hold price, flex on terms |
| Hard-no counter | Your buyer is firm at original offer, willing to walk |
| Multiple-offer | Listing agent hinted highest-and-best, [N] other offers |
| Seller-side | You're the listing agent, replying to a counter to your counter |

Each variant has a 1-line addition you append to the base prompt.

## How this connects to the rest of the stack

When your buyer's offer becomes a contract:
- `claude-boldtrail-bridge` (Episode 7) posts the under-contract note to BT.
- `listing_phase_scanner.py` (cron, 6:55 AM) tags the contact's CRM stage.
- `lead-nurture-cadence` (Episode 5) shifts to "under contract" stage cadence.

The counter-reply is one moment in the transaction. The rest of the stack handles the tax of the surrounding workflow.

## See also

- `examples/before-after.md` for the actual $385K example.
- `examples/failure-modes.md` for the breaks.
- `cron.example` — on-dispatch, no cron.

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
