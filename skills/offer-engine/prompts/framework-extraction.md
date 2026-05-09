# Framework Extraction — Meta-Prompt

This is the instruction set for turning a raw transcript into structural framework entries in the Framework Library.

## Your job

Read a transcript and identify **reusable structural patterns** the speaker is teaching or demonstrating. You are NOT transcribing, summarizing, or paraphrasing the transcript. You are extracting the **underlying architecture** that a user could apply to their own business.

## The critical distinction: pattern vs. content

**Pattern (what you extract):**
> "Three-tier value stack pattern: speaker presents the core deliverable, then adds a complementary second deliverable that removes a common objection, then adds a third deliverable that accelerates time-to-result. Each tier is priced separately, then totaled, then offered at a discount."

**Content (what you do NOT extract):**
> Direct quotes, paraphrases of specific sentences, reproductions of the speaker's exact examples, verbatim framework names, or any text that closely mirrors the source wording.

If what you've written could be recognized as "that's [creator]'s [specific thing] reworded," you've written content, not a pattern. Rewrite it at one more level of abstraction.

## What qualifies as a framework

Look for any of these signals in the transcript:

1. **Named constructs** — the speaker introduces a term for a concept ("the X principle," "the Y method"). Extract the *underlying logic*, not the name.
2. **Step-based processes** — "first you do A, then B, then C." Extract the sequence structure and what each step accomplishes.
3. **Formulas or equations** — anything of the form `output = f(inputs)`. Extract the variables and the relationship between them.
4. **Decision trees** — "if X then do A, if Y then do B." Extract the branching conditions and outcomes.
5. **Comparison structures** — "bad version looks like X, good version looks like Y." Extract the dimension being compared and the delta.
6. **Repeated structural patterns** — if the speaker uses the same structure across multiple examples, that structure is a pattern even if it's never named.
7. **Mental models** — analogies or frames used to reason about a problem ("think of it like a lever"). Extract the underlying model, not the analogy.

## Categories

Every framework must be tagged with one category. Pick the best fit:

- **Offer Structure** — how the overall offer is composed
- **Value Stack** — how multiple deliverables are bundled and priced
- **Guarantee** — risk-reversal patterns
- **Bonus** — additional deliverable patterns and positioning
- **Hook** — opening patterns for content, ads, or sales messages
- **Headline** — copy structures for titles and main claims
- **Pricing** — pricing strategy patterns
- **Objection Handling** — patterns for preempting or resolving buyer concerns
- **Sales Psychology** — cognitive or emotional leverage patterns
- **Other** — doesn't fit above; use sparingly

## Output format

For each pattern you identify, produce a JSON object with these fields:

<details>
<summary>json</summary>

```json
{
  "name": "short descriptive name (5–8 words, your own wording)",
  "category": "one of the categories above",
  "pattern_description": "2–4 sentences describing the structural approach in generic terms. Explain what the pattern IS, independent of any specific example. Write in your own voice.",
  "when_to_use": "1–2 sentences on the conditions under which this pattern applies. What kind of business, what kind of offer, what problem does it solve.",
  "example_application": "3–5 sentences showing how this pattern would apply to a FICTIONAL or GENERIC business (e.g., 'a SaaS tool for dentists', 'a freelance copywriter'). Do NOT use the speaker's actual example. Invent your own.",
  "source_url": "the URL provided",
  "source_creator": "creator name/handle as it appears on the source",
  "confidence": "high | medium | low"
}
```

</details>

Return a JSON array of these objects, one per pattern identified. A 30-minute video typically yields 3–8 patterns. A podcast episode yields 4–10. If you identify fewer than 2 patterns in a full-length transcript, reconsider — you're likely being too strict. If you identify more than 15 in a single transcript, you're likely being too loose or double-counting.

## Quality bar — what NOT to extract

- **Anecdotes and stories** → not patterns unless they illustrate a repeatable structure
- **Motivational claims** ("you can do this," "it's worth it") → not patterns
- **Industry-specific examples** that don't generalize → not patterns
- **Opinions disconnected from structure** ("X is the most important thing") → the claim is not a pattern, but there may be structural reasoning underneath — if so, extract that
- **Anything you can't describe without quoting the source** → not ready yet; rethink at higher abstraction or skip

## Confidence levels

- **high** — the pattern is explicitly stated, repeated, or demonstrated multiple times
- **medium** — the pattern is stated once, clearly, with a single example
- **low** — the pattern is implied across multiple statements but never directly named. Worth capturing for completeness, but flag for user review.

## Self-check before you finalize

For each pattern you're about to write:

1. Could a reader apply this to a completely different industry than the source's? If no → too specific, abstract it more.
2. If I stripped the attribution, would the pattern stand on its own as generic marketing/business knowledge? If yes → good. If it reads as "clearly [creator]'s thing" → too close to the source.
3. Does the pattern_description use any distinctive phrasing that seems borrowed from the transcript? If yes → rewrite in your own voice.
4. Does the example_application use the speaker's actual example? If yes → replace with your own invented example.

## Handling branded or trademarked framework names

If the speaker uses a distinctive branded name for their framework (e.g. "The [Proper Noun] Method," "The [Brand] Formula"):

- **Do NOT** use the branded name in the `name` field
- **Do NOT** reproduce the branded definitional language in `pattern_description`
- **DO** capture the underlying structural logic and name it descriptively (e.g. if someone teaches "The XYZ Value Equation," your `name` might be "Four-variable value perception pattern" and your `pattern_description` explains the variables and their relationship in generic terms)

The user owns their extracted library. They can always see the `source_url` and `source_creator` for attribution. But the library itself should read as a generic business knowledge base, not a mirror of any one creator's brand.
