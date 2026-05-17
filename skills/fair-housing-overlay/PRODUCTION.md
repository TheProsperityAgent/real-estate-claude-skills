# PRODUCTION — fair-housing-overlay

The foundational skill that every other skill in this repo loads on top of. This file is the practical "how you actually run this in production every day" walkthrough.

## Where this fits

Every other skill in this repo references `fair-housing-overlay`. It is the compliance floor. Every prompt is FH-clean from the first character because this overlay is loaded alongside.

In Victoria's stack, the overlay is referenced by:

- `listing-description` (Episode 3) — blocks "great schools" + "must-see" + demographic targeting at generation.
- `lead-nurture-cadence` (Episode 5) — refuses cold-list re-engagement; enforces STOP-to-opt-out on every SMS.
- `cma-narrative` (Episode 4) — keeps market context neutral, blocks "buyer's market" / "seller's market" framing.
- `mls-to-social` (Episode 8) — strict on LinkedIn editorial framing.
- `compliance_supervisor.py` (cron, 11 AM) — uses the same 47-phrase blocklist as the overlay's Rule Set 1.
- Every Canva lifecycle stage — blocks "family-friendly", "back-to-school", "private community" patterns.

You never load this skill alone. It's always paired with the skill that's doing the actual generation work. The overlay does compliance; the other skill does content.

## When the overlay catches versus when the supervisor catches

| Layer | When it fires | What it catches |
|---|---|---|
| `fair-housing-overlay` | At generation time (live) | The 80% of common patterns ("must-see", "great schools", demographic targeting) |
| `compliance_supervisor.py` | 11 AM daily | The 20% that slipped through — edge cases, plural variants, hyphenated forms, brand-new patterns |

The overlay is your primary defense. The supervisor is the floor that catches what the overlay missed.

## How to load this in Claude Code

```
/skill load fair-housing-overlay
/skill load <whatever-skill-you-actually-want-to-run>
```

Or paste the four rule sets from `SKILL.md` as a system-message preamble before any prompt.

## How to load this in Python (programmatic skill loader)

```python
from skill_loader import load_skill

overlay_text = load_skill("fair-housing-overlay")
# Include overlay_text as part of the system prompt or as a preamble
system = f"{overlay_text}\n\n{your_actual_task_prompt}"

response = anthropic_client.messages.create(
    model="claude-opus-4-7",
    max_tokens=1500,
    system=system,
    messages=[{"role": "user", "content": user_input}],
)
```

## Edge cases the overlay was hardened against

These are real cases Victoria's stack encountered and fixed:

1. **"Great school districts"** (plural with "districts" appended) — initially missed by `\bgreat\s+schools\b`. Now caught by `\bgreat\s+schools?\s+district\b` AND `\bschool\s+district\b`.
2. **"Quiet street with traffic-calming bumps"** — should NOT be blocked. The pattern uses negative lookahead: `\bquiet\s+(?:area|neighborhood|street(?!\s+with))\b`.
3. **"Oakwood School District" as a subdivision name** — should NOT be blocked. Pre-mask known proper nouns before pattern match.
4. **"Family-friendly"** (hyphenated) vs "family friendly" (space) — both caught.
5. **"Back to school" in Q3 marketing** — added as a specific FHA familial-status pattern (`\bback\s+to\s+school\b`).
6. **"Tight-knit community"** vs "tight knit community" — both caught.

## What the overlay does NOT catch

- Typos and spelling errors.
- Factually incorrect numbers (you said $385K when the listing is $358K).
- Tonal misjudgments within the bounds of compliance.
- Image selection issues.

These are human-judgment territory. The overlay handles the regulatory floor.

## When to update the overlay

The overlay should be reviewed quarterly. Add patterns when:

1. A new FHA enforcement action surfaces a pattern (check HUD's case database).
2. A new MLS aggregator blocks a phrase (check Realtor.com / Zillow public guidelines).
3. NAR publishes a new Code of Ethics interpretation.
4. Your state's RE Commission issues a new advisory.

The overlay is a living document. The four rule sets are stable; the blocklists inside them grow.

## Companion content

YouTube Episode 2: `Why Claude Skills Change Real Estate Forever` — walks the four rule sets with real-world before / after examples.

## See also

`examples/before-after.md` and `examples/failure-modes.md` in this folder for live cases.

## Built by

Al & Victoria Pinder · ICON Agents · eXp Realty · Greenville, NC
