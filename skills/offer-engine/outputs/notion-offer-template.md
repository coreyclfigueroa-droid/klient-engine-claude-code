# Notion Offer Page Template

This is the block structure to build when writing a finished offer to the Offer Drafts database. Use Notion's API blocks endpoint to append each block to the newly-created page.

## Block sequence

<details>
<summary>code</summary>

```
heading_1          → <Headline>
paragraph (italic) → <Subheadline>
divider

heading_2          → Who this is for
bulleted_list_item → <qualifier 1>
bulleted_list_item → <qualifier 2>
bulleted_list_item → <qualifier 3>

heading_2          → This is NOT for you if
bulleted_list_item → <disqualifier 1>
bulleted_list_item → <disqualifier 2>
divider

heading_2          → What you get
table              → Value stack (3 columns: Deliverable | Outcome | Value)
                     + final row: Total value | | $X
callout            → Your investment today: $Y  (You save $Z)
divider

heading_2          → Bonuses (only if bonuses exist)
table              → Bonus stack (same columns)
divider

heading_2          → Our guarantee
callout (icon 🛡)  → <guarantee paragraph>
divider

heading_2          → Results from past clients   (only if proof exists)
paragraph          → <proof content>
divider

heading_2          → Why now
paragraph          → <urgency paragraph>
divider

heading_1          → <CTA line>
paragraph          → Button: <button text>  |  <link placeholder>
divider

heading_3          → Frameworks applied
bulleted_list_item → <framework name> — <source creator> — <source URL>
bulleted_list_item → <framework name> — <source creator> — <source URL>
...
```

</details>

## Notion API notes

- Use the `/v1/blocks/{page_id}/children` endpoint with a PATCH request
- Batch all blocks in a single request (up to 100 blocks per call — offer one-pagers fit easily)
- Set `Notion-Version: 2022-06-28` header
- For the table: use `table` block type with `table_width` matching column count and `has_column_header: true`
- For the callout: set `icon.emoji` per section (🛡 for guarantee, 💰 for investment line, ⏰ for urgency)

## Tone and formatting defaults

- Headlines: plain text, no emoji, no markdown decoration
- Paragraphs: short. 2 sentences max per paragraph unless it's the guarantee or urgency section
- Bullets: start with a verb or concrete noun, not a filler word
- Prices: formatted with $ and comma separators ($1,997 not $1997)
- Timeframes: exact when possible ("in 60 days" not "quickly")

## What this template does NOT do

- Does not include any headers like "Value Equation" or branded framework names from specific creators
- Does not paste source material — only the user's own business content, structured by patterns
- Does not generate images, graphics, or icons beyond the section emojis
- Does not set up payment integration — the CTA is a placeholder link the user wires up separately
