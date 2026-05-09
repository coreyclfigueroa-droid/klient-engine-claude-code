# YouTube Single Video Scraper

Use when the user provides a single YouTube video URL (formats: `youtube.com/watch?v=...`, `youtu.be/...`, `youtube.com/shorts/...`).

## Actor

**Apify actor:** `streamers/youtube-scraper`
**Docs:** https://apify.com/streamers/youtube-scraper

Same actor as channel scraper — it accepts single video URLs natively.

## Pre-scrape cost gate (REQUIRED)

<details>
<summary>code</summary>

```
────────────────────────────────────────
  YouTube Video Scrape — Cost Preview
────────────────────────────────────────
  Source:        <video URL>
  Title:         <will fetch during scrape>

  Estimated compute: ~0.01–0.03 Apify credits
  Estimated runtime: under 30 seconds
  Cost at current Apify pricing: ~$0.01–$0.03
────────────────────────────────────────

Proceed? (yes / no)
```

</details>

## Actor input

<details>
<summary>json</summary>

```json
{
  "startUrls": [
    { "url": "<user-provided video URL>" }
  ],
  "maxResults": 1,
  "subtitlesLanguage": "en",
  "subtitlesFormat": "srt",
  "downloadSubtitles": true,
  "saveSubsToKVS": false
}
```

</details>

## Run the actor

<details>
<summary>bash</summary>

```bash
curl -X POST \
  "https://api.apify.com/v2/acts/streamers~youtube-scraper/run-sync-get-dataset-items?token=$APIFY_TOKEN&timeout=60" \
  -H "Content-Type: application/json" \
  -d @input.json
```

</details>

Single videos almost always complete within the sync window, so async fallback is rarely needed. If it does timeout, use the same `/resume-scrape` pattern as `youtube-channel.md`.

## After results return

Expect one item in the dataset. Extract:
- Title, URL, view count, channel name for attribution metadata
- `subtitles[0].srt` → strip timestamps → clean transcript

Pass transcript to `../prompts/framework-extraction.md`. Expect fewer frameworks per run than a channel scrape (single video = single creator's single session) — often 2–6 patterns depending on video density.

## Write to Framework Library

Same as channel scraper: each extracted pattern = one Notion row, with `Source URL` pointing to the specific video URL (not the channel).

## Summary to user

<details>
<summary>code</summary>

```
Scrape complete.

  Video:              <title>
  Channel:            <channel name>
  Frameworks logged:  <k> entries → Framework Library
  Runtime:            <ss>s
  Apify cost:         ~$0.0X

  Extracted patterns:
    1. <pattern name> — <category>
    2. <pattern name> — <category>
    ...

Want to scrape another video, or run /build-offer?
```

</details>

## Edge cases

- **Video has no subtitles** (common for small channels, livestreams, music videos): surface clearly — "This video doesn't have transcripts available. YouTube auto-generates them for most videos within 24 hours of upload, so try again later, or pick a different video."
- **Age-restricted or private video**: actor will fail. Surface the error, move on.
- **Livestream recording**: transcripts usually exist but may be rough. Still worth extracting; flag lower confidence in the `Pattern Description` field.
