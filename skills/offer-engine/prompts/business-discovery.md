# Business Discovery Interview

Use this when the user runs `/build-offer`. Your job is to collect enough about their business to synthesize a sharp, specific offer. Ask questions one at a time, not in a wall. Wait for each answer before asking the next.

## Rules of engagement

- **One question per turn.** Never dump all questions at once.
- **Accept short answers.** If the user says "skip" or gives a one-liner, move on. Don't interrogate.
- **Don't over-ask.** If their earlier answers already implied something, don't re-ask. Acknowledge what you inferred and confirm.
- **Don't be cute.** No "great question!" or "love that!" — just work the interview.
- **Minimum viable set.** You need answers to Q1–Q7 to proceed. Q8–Q12 are nice-to-have; skip any they push back on.

## The questions (in order)

### Q1. What do you sell?
"In one sentence: what's the product or service?"

Looking for: the core deliverable. If vague ("I help people with marketing"), push once for specificity ("okay — specifically what do you do for them and what's the format? coaching, done-for-you, a course, software?").

### Q2. Who specifically is this for?
"Who's your ideal customer? Be as specific as you can — industry, role, revenue stage, or situation."

Looking for: an ICP sharp enough to name. Generic ("business owners") → push once for specificity ("which kind? ecom brands under $1M? local service businesses? agency owners?").

### Q3. What's the dream outcome for them?
"If they use this successfully, what's the transformation? What does their life or business look like 90 days later?"

Looking for: a concrete outcome, not a feature. "They learn more" is weak. "They go from $0 to $10K/mo in 60 days" is strong.

### Q4. What's it cost today?
"What are you charging for it, if anything yet?"

Looking for: current price. If they haven't set one, ask what range they're considering. This anchors the value stack later.

### Q5. What's the #1 objection or fear that stops people from buying?
"When someone almost buys and doesn't, what's the reason?"

Looking for: the dominant objection. Common ones: doesn't trust it'll work for them, too expensive, doesn't have time to implement, already tried similar and failed. This drives the guarantee.

### Q6. What are you better at than competitors?
"What's one thing you do meaningfully better than the alternatives?"

Looking for: a real differentiator, not a generic claim. "Better results" → push for why/how.

### Q7. What results can you prove?
"Any case studies, testimonials, numbers you can cite? Even one strong result matters more than five weak ones."

Looking for: specific proof. If they have none → note it; the offer will lean more on guarantee-as-proof. Not a dealbreaker.

---

Stop here if the user is getting tired or wants to just build. Q8–Q12 are enhancements, not requirements.

---

### Q8. What's included in the core deliverable?
"List the pieces of what they actually get. Modules, calls, templates, software access, community, whatever."

Looking for: the raw material for the value stack. Capture as a bullet list.

### Q9. What could you throw in as bonuses that would tip someone over the edge?
"What do you have lying around — templates, past recordings, a second offer, access to you — that would feel like a 'hell yes' addition?"

Looking for: bonus inventory. These get stacked in the offer to increase perceived value without increasing fulfillment load.

### Q10. What's your risk tolerance on a guarantee?
"How bold can you be on a guarantee? Money-back? Results-based? Something in between?"

Looking for: what kind of guarantee they'd actually honor. This determines the strength of the risk reversal in the final offer.

### Q11. Do you have a timeframe pressure?
"Is there a reason someone should buy now vs. next month? A cohort start date, a price increase, a bonus expiring, a launch window?"

Looking for: urgency mechanism. If none exists, the offer will need one constructed.

### Q12. Anything specific you want to borrow from the Framework Library?
"I'll pull from every framework you've scraped, but if there's a specific creator or pattern you want me to weight heavier, tell me now."

Looking for: optional weighting. If they don't care, default to pulling the highest-confidence patterns across all categories.

## Handoff to synthesis

When you have Q1–Q7 at minimum, summarize what you heard back to the user in ~5 lines:

<details>
<summary>code</summary>

```
Got it. Here's what I'm building from:

  Product:       <Q1>
  ICP:           <Q2>
  Outcome:       <Q3>
  Price point:   <Q4>
  Main blocker:  <Q5>
  Edge:          <Q6>
  Proof:         <Q7>

Building the offer now. This takes about 30–60 seconds.
```

</details>

Confirm anything unclear, then move to `offer-synthesis.md`.
