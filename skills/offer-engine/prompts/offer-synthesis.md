# Offer Synthesis

Take the user's business discovery answers + their Framework Library and produce a polished offer one-pager in Notion.

## Inputs you have

1. **Business discovery answers** (Q1–Q12 from `business-discovery.md`)
2. **Framework Library** — query the Notion database, pull all rows with `Confidence = high` or `Confidence = medium`. Group by `Category`.
3. **Market Intel** — if the companion `competitor-intel` skill has populated this database, include those rows. If the database is empty, skip.

## Framework selection logic

For each section of the offer, select patterns from the Library by category:

| Offer section | Pull from category |
|---|---|
| Headline | Headline, Hook |
| Dream outcome framing | Offer Structure, Sales Psychology |
| Value stack composition | Value Stack, Offer Structure |
| Pricing approach | Pricing |
| Guarantee | Guarantee |
| Bonuses | Bonus |
| Objection preempts | Objection Handling |
| CTA and urgency | Sales Psychology, Hook |

If a category has multiple patterns, prefer **high confidence** over medium, and prefer patterns that appear across **multiple source creators** (cross-creator validation = more likely to be real, transferable signal).

If a category is empty in the Library, proceed using general best practices for that section and flag it in the handoff: "Your library doesn't have Guarantee patterns yet — I used a standard money-back structure. Scrape a creator known for risk reversal to upgrade this section."

## Writing rules

- **Write fresh copy for the user's business.** Do not use phrasing from the Library entries directly — the Library describes structural patterns; your job is to instantiate those patterns with the user's specific product, ICP, outcome, and proof.
- **Specific over clever.** "Go from $0 to $10K/mo selling on Amazon in 60 days" beats "Transform your business." Lean on the concrete inputs from discovery.
- **ICP language.** Use words the user's ICP uses. If Market Intel is populated, pull verbatim customer phrases for pain-point framing. If not, use the user's Q5 answer as the closest approximation of their customer's voice.
- **No borrowed slogans.** If a Library entry describes a "four-variable value perception pattern," you implement it as a value stack — you do not write "Our Value Equation is..." using the source creator's exact framing.

## The offer one-pager structure

Write the offer as Notion page content (not database property values — the long-form goes in the page body). Use these sections in this order:

### 1. Headline (H1)
One line. The core promise. Should name the ICP, the outcome, and the timeframe. No cleverness, no metaphors — just what they get and when.

Example structure (replace with user's specifics):
> How [ICP] go from [current state] to [dream outcome] in [timeframe] — without [biggest objection/fear].

### 2. Subheadline (italic paragraph, 1–2 sentences)
Sharpens the promise. Adds specificity. Hints at the mechanism.

### 3. Who this is for / Who this is NOT for (two short lists)
**This is for you if:** 3 specific bullet points matching the ICP
**This is NOT for you if:** 2 specific bullet points (disqualifiers — makes the qualifiers feel more credible)

### 4. The value stack (structured table or bulleted list)
List each deliverable in the offer as a row. For each row:
- **What it is** (one line)
- **What it does for them** (one line — outcome, not feature)
- **Standalone value** ($X)

After the list: **Total standalone value: $[sum]**
Then: **Your investment today: $[user's price]**
Then: **You save: $[difference]**

### 5. Bonuses (if user provided them in Q9)
Same structure as value stack — each bonus is a row with name, outcome, standalone value. Adds to the total.

### 6. The guarantee
One paragraph. Structured to address the Q5 objection directly. Uses the guarantee pattern selected from the Library.

### 7. Proof (if user provided in Q7)
Pull Q7 answers in cleanly. Case study names, testimonial snippets, specific numbers. If empty, skip the section entirely — don't fabricate.

### 8. Why now (urgency)
One paragraph. Uses Q11 if provided. If Q11 was empty, construct a reasonable urgency mechanism: cohort limits, bonus expiry, price increase schedule, or seasonal relevance.

### 9. CTA
One clear line + the literal button text. No ambiguity.

### 10. Frameworks applied (metadata, for user reference)
A final section listing which Framework Library entries informed this draft, with source URLs. This is for the user's own tracking — so they can see what worked and what to scrape more of.

## Notion API: write the draft

1. Create a new page in the Offer Drafts database with properties:
   - `Name` = the offer's headline or a user-friendly label
   - `Business` = user's Q1 product name
   - `Status` = Draft
   - `Built Date` = today
   - `Frameworks Used` = comma-separated list of framework names from the Library

2. The page content itself is the full one-pager, written using Notion blocks (heading_1, heading_2, paragraph, bulleted_list_item, callout for the guarantee, table for the value stack).

3. After the page is created, surface the page URL to the user:

<details>
<summary>code</summary>

```
Offer draft complete.

  Name:      <offer headline>
  Frameworks applied: <n>
  Sections:  Headline, Value Stack, Guarantee, Bonuses, Proof, Urgency, CTA

  Open in Notion: <url>

Want me to iterate on any section, or should we validate this against your Market Intel (if you've run competitor-intel) for language-fit?
```

</details>

## Iteration

If the user asks to rework a section:
- Re-pull alternative patterns from that category in the Library (confidence: medium or by different source creator)
- Rewrite just that section, update the Notion page
- Don't rebuild the whole offer — only what they asked to change

If the user asks to try "a different creator's style":
- Filter Library by `Source Creator`, re-run synthesis using only that subset
- Save as a new row in Offer Drafts (don't overwrite — they may want to compare)
