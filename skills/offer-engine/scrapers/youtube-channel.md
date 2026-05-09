# YouTube Channel Scraper

Use when the user provides a YouTube channel URL (formats: `youtube.com/@handle`, `youtube.com/c/name`, `youtube.com/channel/UC...`, `youtube.com/user/name`).

## Actor

**Apify actor:** `streamers/youtube-scraper`
**Docs:** https://apify.com/streamers/youtube-scraper

## Pre-scrape cost gate (REQUIRED)

Before launching any scrape, show the user exactly this, filled in with their inputs:

<details>
<summary>code</summary>

```
────────────────────────────────────────
  YouTube Channel Scrape — Cost Preview
────────────────────────────────────────
  Source:        <channel URL>
  Max videos:    <number, default 50>
  Transcripts:   Yes (required for extraction)

  Estimated compute: ~0.3–0.8 Apify credits
  Estimated runtime: 3–8 minutes
  Cost at current Apify pricing: ~$0.30–$0.80

  Async fallback: If this run takes >60s, I'll return
  a run ID so you can resume with /resume-scrape [id]
────────────────────────────────────────

Proceed? (yes / no / change max videos)
```

</details>

Do NOT run the scrape until the user replies `yes` or equivalent affirmative. If they want to change the video cap, re-show the preview with the new number.

## Actor input schema

<details>
<summary>json</summary>

```json
{
  "startUrls": [
    { "url": "<user-provided channel URL>" }
  ],
  "maxResults": 50,
  "maxResultsShorts": 0,
  "maxResultStreams": 0,
  "subtitlesLanguage": "en",
  "subtitlesFormat": "srt",
  "downloadSubtitles": true,
  "saveSubsToKVS": false
}
```

</details>

Adjust `maxResults` based on user's requested cap. Keep `maxResultsShorts` and `maxResultStreams` at 0 unless the user specifically wants them.

## Run the actor

Use the Apify sync-with-dataset endpoint so results come back inline if fast enough:

<details>
<summary>bash</summary>

```bash
curl -X POST \
  "https://api.apify.com/v2/acts/streamers~youtube-scraper/run-sync-get-dataset-items?token=$APIFY_TOKEN&timeout=60" \
  -H "Content-Type: application/json" \
  -d @input.json
```

</details>

### If it times out (>60s)

The call returns a run ID. Save it, surface to user:

> Scrape is still running on Apify (large channels take longer). Your run ID is `<id>`. I'll keep this in your session. Run `/resume-scrape <id>` in 5–10 minutes to pick up the extracted transcripts.

Log the run ID to `~/.claude/offer-engine/pending_runs.json` with timestamp and source URL.

## After results return

Each item in the dataset has this shape (key fields only):

<details>
<summary>json</summary>

```json
{
  "title": "...",
  "url": "https://www.youtube.com/watch?v=...",
  "viewCount": 12345,
  "date": "2024-...",
  "duration": "12:34",
  "channelName": "...",
  "channelUrl": "...",
  "subtitles": [
    { "type": "srt", "language": "en", "srt": "full SRT text here" }
  ]
}
```

</details>

For each video with a subtitle track:
1. Strip SRT timestamps to get clean transcript text
2. Pass transcript + metadata to `../prompts/framework-extraction.md` for pattern extraction
3. Write each extracted framework as a new row in the user's Framework Library Notion database

Videos without subtitles: skip with a note in the summary ("5 videos skipped — no transcripts available").

## Summary to user after scrape completes

<details>
<summary>code</summary>

```
Scrape complete.

  Channel:            <channel name>
  Videos processed:   <n>
  Videos with subs:   <m>
  Frameworks logged:  <k> entries → Framework Library
  Runtime:            <mm:ss>
  Apify cost:         ~$<0.XX>

  Top framework categories extracted:
    • Offer Structure: <count>
    • Value Stack: <count>
    • Guarantee: <count>
    • Hook: <count>
    • ...

Want to run /build-offer now?
```

</details>

## Deduplication

Before writing a new framework to Notion, query the Framework Library for existing rows with the same `Source URL` **and** similar `Pattern Description` (first 40 chars). Skip if already exists to avoid duplicate entries from re-scrapes.

## Failure modes and handling

- **404 on channel URL** → URL malformed or channel doesn't exist. Ask user to recheck.
- **Channel has no videos with English subtitles** → tell user plainly; offer to try a different language via the `subtitlesLanguage` param.
- **Apify quota exceeded** → surface the actual Apify error, point user to their Apify billing page.
- **Notion write fails** → cache extracted frameworks to `~/.claude/offer-engine/pending_writes.json`, surface error, offer retry.
