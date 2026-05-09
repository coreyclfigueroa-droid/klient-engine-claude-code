---
name: offer-engine
description: Extract offer-building frameworks from any creator's public content (YouTube channels, individual videos, podcasts) and use them to construct a polished, validated offer for the user's business. Use this skill whenever the user wants to build, rewrite, rate, or validate an offer for their product or service, whenever they mention studying or learning from a specific creator/expert/marketer to build their offer, whenever they reference scraping a YouTube channel or podcast to extract marketing or sales frameworks, or whenever they say anything about a "framework library," "offer sheet," "value stack," or "grand slam offer." Also trigger when the user wants to build an offer by studying competitors in their space. Outputs a polished offer one-pager to Notion.
---

# Offer Engine

Build offers by extracting structural frameworks from any creator's public content, then applying those frameworks to the user's business. Ships empty — every framework in the user's library is generated from their own scrapes, logged to their own Notion workspace.

## What this skill does

1. Scrapes public content (YouTube channels, single videos, podcast RSS feeds) via Apify
2. Extracts **structural patterns and frameworks** (not verbatim content) from transcripts
3. Logs frameworks to the user's Notion "Framework Library" database with source attribution
4. Interviews the user about their business (ICP, product, dream outcome, competitors, constraints)
5. Synthesizes a polished offer one-pager in Notion using the extracted frameworks + business inputs

## When to run what

- **First time using this skill** → run `onboarding.md` workflow. It self-destructs after successful completion.
- **User wants to scrape a creator** → `/scrape-creator [url]` → see `scrapers/` folder, pick the right scraper based on URL type
- **User wants to build an offer** → `/build-offer` → pulls from Framework Library + runs `prompts/business-discovery.md` → outputs via `outputs/notion-offer-template.md`

## Core workflow

### Step 1: Onboarding (first run only)
Read `onboarding.md` and walk the user through setup. Delete `onboarding.md` after successful first run.

### Step 2: Scrape content
Determine URL type and route to correct scraper:
- YouTube channel URL (`youtube.com/@handle` or `youtube.com/channel/...`) → `scrapers/youtube-channel.md`
- YouTube single video (`youtube.com/watch?v=...` or `youtu.be/...`) → `scrapers/youtube-single-video.md`
- Podcast RSS feed URL → `scrapers/podcast-rss.md`

Run the pre-scrape cost gate from that scraper's file before launching. Require explicit user confirmation.

### Step 3: Extract frameworks
After scrape returns transcripts, read `prompts/framework-extraction.md` and apply it to each transcript. Write results to the user's Notion "Framework Library" database. **Extract patterns and structures only — never paste verbatim source content into Notion.** See the extraction prompt for the distinction.

### Step 4: Build offer (when user is ready)
Read `prompts/business-discovery.md` and ask the user the discovery questions. Then read `prompts/offer-synthesis.md` to combine their answers with the Framework Library. Output the final offer using `outputs/notion-offer-template.md` structure to the "Offer Drafts" database.

## Legal and IP rules (non-negotiable)

This skill operates on publicly available content for the user's personal learning and offer-building use. To stay on the right side of copyright:

- **Extract patterns, not content.** Framework entries describe *structural approaches* ("three-part value stack pattern," "risk-reversal guarantee structure") — not verbatim quotes, direct paraphrases, or near-copies of the source creator's language.
- **Never store long transcript chunks in Notion.** Transcripts are working memory during extraction only. Only extracted patterns persist.
- **Always include source attribution** on every framework entry (source URL, creator handle, timestamp if available) — this is standard citation practice, not endorsement.
- **Never reproduce named proprietary frameworks verbatim.** If a creator has a branded framework, the extraction describes the underlying pattern in generic terms; it does not reproduce the branded name or exact definitional language.
- **Offer outputs are the user's original work.** The skill assembles the user's business inputs using extracted structural patterns — the resulting offer copy is written fresh for the user's business, not borrowed phrasing.

If the user asks the skill to dump raw transcripts, paste verbatim content, or produce something that would redistribute a creator's copyrighted expression: decline and explain the pattern-only approach.

## Folder map

<details>
<summary>code</summary>

```
offer-engine/
├── SKILL.md                         ← you are here
├── onboarding.md                    ← first-run setup (self-destructs)
├── scrapers/
│   ├── youtube-channel.md           ← bulk channel scrape via Apify
│   ├── youtube-single-video.md      ← single video scrape
│   └── podcast-rss.md               ← podcast episode scrape
├── prompts/
│   ├── framework-extraction.md      ← meta-prompt for pattern identification
│   ├── business-discovery.md        ← interview questions for the user
│   └── offer-synthesis.md           ← combines library + business → offer
├── outputs/
│   └── notion-offer-template.md     ← structure of final offer one-pager
├── frameworks/                      ← empty; user's library lives in their Notion
└── examples/
    └── sample-offer-output.md       ← reference example of finished offer
```

</details>

## Infrastructure notes

- **Scrape runs** use Apify's `streamers/youtube-scraper` actor (YouTube) and a podcast RSS fetch for podcasts. API key provided during onboarding.
- **Async fallback**: any scrape that takes >60 seconds returns an Apify run ID. The skill surfaces `/resume-scrape [run-id]` to the user so they can continue in a new session.
- **Notion setup**: first run auto-creates three databases in a parent page the user picks:
  - Framework Library (from scrapes)
  - Market Intel (populated by companion skill `competitor-intel`, if installed)
  - Offer Drafts (output from `/build-offer`)
- **Cost gate**: every scrape shows estimated Apify compute units and runtime before executing. No scrape runs without explicit user confirmation.
- **Logging**: skill usage (scrape type, source, duration, cost) optionally logs to the user's Airtable if they provided a key during onboarding.

## Commands reference

| Command | What it does |
|---|---|
| `/scrape-creator [url]` | Route URL to correct scraper, run cost gate, scrape, extract, write to Framework Library |
| `/build-offer` | Run business discovery, synthesize offer from Library, output to Offer Drafts |
| `/resume-scrape [run-id]` | Fetch results from a previously-async Apify run and continue extraction |
| `/list-frameworks` | Query Notion Framework Library, return summary of what's been extracted so far |

## Before you start a session

Check whether `onboarding.md` still exists in this skill folder. If yes → run it. If no → proceed directly to the command the user requested.
