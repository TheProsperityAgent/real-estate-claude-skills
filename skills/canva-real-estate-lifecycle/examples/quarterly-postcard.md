# Stage 7 — Quarterly Postcard (example run)

Geographic farming is the slow compounding strategy. One mailing per quarter to 200-500 addresses for 3-5 years builds you the dominant agent in a neighborhood. This stage produces all 4 seasonal variants in one pass so you have the whole year's mailings drafted up front.

## The 4 seasonal variants

| Variant | Mail-drop window | Headline framing |
|---|---|---|
| Q1 Year-End Recap | Jan / Feb | `[NEIGHBORHOOD] in [PRIOR YEAR]` |
| Q2 Spring Snapshot | Apr / May | `Spring 2026 in [NEIGHBORHOOD]` |
| Q3 Season Transition | Aug / Sep | `[NEIGHBORHOOD] Mid-Year` |
| Q4 Year-To-Date | Nov / Dec | `[NEIGHBORHOOD] 2026 So Far` |

## The Q3 trap — read this carefully

The Q3 mailing window (Aug/Sep) is the highest-FH-risk mailing of the year. Most agents accidentally violate by framing it as "Back to school in [NEIGHBORHOOD]" — that is a demographic / age-of-child implication and a FHA familial-status concern.

The skill enforces "season transition" framing only. No "back to school", no "the kids are back", no "school year starts soon".

## What the skill blocks

- School names, school districts, school rankings, "great schools".
- "Family-friendly", "great for families", any demographic targeting.
- "Safe", "quiet" — only allowed with objective backing.
- Scarcity (won't last, etc.) on geographic-farm mailings.
- Any line that knocks a competing agent, neighborhood, or brokerage.

## The prompt

```
I'm a real estate agent. Build the quarterly postcard package for a
target neighborhood.

NEIGHBORHOOD DATA:
- Neighborhood name: [NAME]
- City: [CITY] [STATE]
- Approximate address count: [N]
- Last quarter's market data:
  - Sold count: [X]
  - Median sold price: $[Y]
  - Median DOM: [Z] days
  - Active inventory: [N]
  - Source: [MLS / RPR / etc.]
- Standout neighborhood objective amenity: [park / commute time /
  something all residents experience the same — NEVER schools]

REQUIRED COMPLIANCE (load fair-housing-overlay) — STRICT EDDM MODE:
- NEVER reference schools / "back to school" / "good schools"
- NEVER use "family-friendly" / "great for families" / demographic targeting
- NEVER use "safe", "quiet" without objective backing
- NEVER use scarcity
- ALWAYS positive — never knocks competing agents
- License + brokerage line + EHO logo on every postcard

OUTPUT — 4 quarterly briefs (front + back each = 8 designs total)
per the spec in SKILL.md.
```

## A real run — Brook Valley, Greenville NC

**Inputs:**
- Neighborhood: Brook Valley
- City: Greenville, NC
- Address count: ~280 homes
- Prior-quarter market data:
  - Sold: 12 homes
  - Median sold: $352,000
  - Median DOM: 21 days
  - Active inventory: 4
  - Source: MLS
- Standout amenity: 0.4-mile walk to Wildwood Park (objective, every resident benefits)

**Output — Q1 Year-End Recap (Jan/Feb mail-drop):**

FRONT:
- HEADLINE: `Brook Valley in 2025`
- 3 STAT TILES:
  - `47 homes sold`
  - `Median $339,000`
  - `19 day average DOM`
- AGENT BLOCK: headshot + `Al & Victoria Pinder · NC #334543 · eXp Realty LLC · 252-XXX-XXXX`

BACK:
- 1-paragraph narrative:
  > Brook Valley closed 2025 with steady, healthy turnover. 47 homes sold across the year at a median of $339,000, and the median home spent 19 days on market — close to last year's pace. Active inventory is sitting at four right now, which is on the leaner side for the spring runway. If you have been thinking about a move in 2026, the early-spring market in Brook Valley is going to reward a well-prepared listing.
- 1 recently-sold tease (anonymized):
  > Most recent closing: a 4-bed traditional on a half-acre, 38 days from contract to close.
- CTA: `Curious what your home would sell for in 2026? Reply or text us at 252-XXX-XXXX.`
- EHO logo + license footer.

**Output — Q2 Spring Snapshot (Apr/May mail-drop):**

FRONT:
- HEADLINE: `Spring 2026 in Brook Valley`
- 3 STAT TILES: Q1 numbers (12 sold / $352K median / 21-day DOM)
- AGENT BLOCK

BACK:
- 1-paragraph narrative:
  > The first quarter in Brook Valley closed with 12 homes sold at a $352,000 median — a slight tick over last year's same-quarter number. Median days on market held at 21, which is fast for our market. With four active listings in the neighborhood right now, well-prepared spring listings are getting attention.
- 1 buyer tease:
  > We're currently working with three pre-approved buyers actively looking in Brook Valley.
- CTA: `Wondering what your home would price at this spring? Reply or text 252-XXX-XXXX.`
- Footer.

**Output — Q3 Season Transition (Aug/Sep mail-drop) — the FH-risk one:**

FRONT:
- HEADLINE: `Brook Valley Mid-Year` (NEVER "Back to School in Brook Valley")
- 3 STAT TILES: H1 numbers
- AGENT BLOCK

BACK:
- 1-paragraph narrative:
  > Brook Valley closed the first half of the year with [X] homes sold at a [$Y] median. The neighborhood has held its character through the seasonal shift — Wildwood Park is a quick walk from most of the block and the streets stay quietly active through the fall pace.
- Objective neighborhood fact:
  > 0.4-mile walk to Wildwood Park.
- CTA: `If you've been thinking about a move into the fall season, we'd love to walk through what's working.`
- Footer.

Notice the framing: "seasonal shift", "fall pace", "Mid-Year". Zero school references.

**Output — Q4 Year-To-Date (Nov/Dec mail-drop):**

FRONT:
- HEADLINE: `Brook Valley 2026 So Far`
- 3 STAT TILES: year-to-date
- AGENT BLOCK

BACK:
- 1-paragraph narrative:
  > Brook Valley has closed [X] homes year-to-date at a [$Y] median. The neighborhood continues to be a steady performer in our Greenville market. As 2027 starts to come into view, well-positioned listings are going to do the work in the spring.
- Holiday framing:
  > Thinking about a move next year? Let's start the conversation now — the earlier we begin, the smoother spring runs.
- CTA + footer.

## Before / After — the Q3 trap

**Before (the way most agents write Q3):**

> Back to school in Brook Valley! Time for the kids to head back — and time for families to think about whether their home still fits. Great schools nearby and a wonderful family-friendly community. Won't last at these prices!

What just happened — line by line:
- "Back to school" → familial-status implication (FHA).
- "Time for the kids" → demographic targeting.
- "Time for families to think" → familial-status targeting.
- "Great schools nearby" → school reference, FHA.
- "Wonderful family-friendly community" → FHA, coded targeting.
- "Won't last at these prices" → MLS scarcity.

The skill's Q3 output above gets the same seasonal hook (mid-year reflection) without any of those.

## Time saved

From-scratch: 4 hours to write 4 quarterly variants front + back + verify each one for FH.
With the skill: 20 minutes input + review.

The compounding play: build the 4 once, drop into Canva once, USPS EDDM the same 280 addresses every 90 days for 5 years. After year 3, you are the agent the neighborhood thinks of first when someone is moving. The skill makes the once-up-front investment cheap enough that you'll actually do it.
