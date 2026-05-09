# Podcast RSS Scraper

Use when the user provides a podcast RSS feed URL, an Apple Podcasts URL, or a Spotify podcast URL.

## Source resolution

Different podcast URLs need different handling:

- **Direct RSS feed** (`.rss`, `.xml`, or returns `application/rss+xml`) → use directly
- **Apple Podcasts URL** (`podcasts.apple.com/...`) → resolve to RSS feed via iTunes Lookup API:
  <details>
<summary>code</summary>

```
  https://itunes.apple.com/lookup?id=<podcast_id>&entity=podcast
  ```

</details>
  The returned JSON includes `feedUrl`.
- **Spotify podcast URL** → Spotify does not expose RSS feeds publicly. Tell the user: "Spotify podcast URLs don't expose RSS feeds. Find the same podcast on Apple Podcasts or the show's website, paste that URL instead."

## Pre-scrape cost gate (REQUIRED)

<details>
<summary>code</summary>

```
────────────────────────────────────────
  Podcast Scrape — Cost Preview
────────────────────────────────────────
  Source:           <feed URL>
  Podcast:          <name from RSS>
  Episodes to pull: <number, default 10 most recent>
  Transcription:    Required (podcasts rarely ship transcripts)

  Estimated cost:   ~$0.15–$0.40
  Estimated runtime: 2–5 minutes
────────────────────────────────────────

Proceed? (yes / no / change episode count)
```

</details>

## Implementation

Podcasts don't have a one-shot Apify actor equivalent to YouTube — most podcasts don't publish transcripts in the RSS feed. Two options, pick based on what's available:

### Option A: Feed has transcript links (ideal, rare)

Some RSS feeds include `<podcast:transcript>` tags (Podcast 2.0 spec) pointing to SRT/VTT/JSON transcript files. If present:
1. Parse feed XML, extract episode metadata + transcript URLs
2. Fetch each transcript directly via HTTP
3. Skip Apify entirely

### Option B: Feed only has audio (common)

For each episode MP3 URL:
1. Download audio
2. Transcribe via OpenAI Whisper API or Deepgram (user provides API key in onboarding step — if neither configured, tell user: "This podcast doesn't publish transcripts. To extract frameworks, you need to add an OpenAI or Deepgram API key to your offer-engine config. Want me to walk you through that now?")
3. Feed transcripts into extraction

**Default:** prompt the user during onboarding for a transcription API key. If they skipped it, surface the gap when they try to scrape a transcript-less podcast, and offer to add it on the fly.

### Feed parsing

<details>
<summary>bash</summary>

```bash
curl -s "<feed URL>" | xmllint --xpath "//item" -
```

</details>

Extract per episode:
- `title`
- `enclosure/@url` (audio file URL)
- `pubDate`
- `itunes:duration`
- `link` (episode page URL, use as `Source URL`)
- `podcast:transcript/@url` (if exists)

## After transcripts are ready

Pass each episode's transcript to `../prompts/framework-extraction.md`. Log each extracted pattern to Framework Library with:
- `Source URL` = episode page URL
- `Source Creator` = `<podcast name> — <host names>` as it appears in the feed
- `Category` = determined by extraction prompt

## Summary to user

<details>
<summary>code</summary>

```
Scrape complete.

  Podcast:            <name>
  Episodes processed: <n>
  Frameworks logged:  <k> entries → Framework Library
  Transcription API:  <whisper|deepgram|feed-provided>
  Runtime:            <mm:ss>
  Total cost:         ~$<0.XX>
```

</details>

## Failure modes

- **Feed returns 404/403** → surface error, ask user to verify URL
- **Feed is malformed XML** → attempt lenient parse; if that fails, tell user and ask for a different source
- **Episode MP3 fails to download** → skip that episode, note in summary
- **Transcription API quota exceeded** → stop after quota hit, write what's done, surface the remaining episodes as `pending_transcription` in the pending runs file
