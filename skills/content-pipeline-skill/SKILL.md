---
name: content-pipeline
version: 1.1.0
description: "Use this skill when the user wants to scrape top-performing content from TikTok, analyze why it works, and generate new scripts. Also use when the user mentions 'content pipeline,' 'scrape content,' 'scrape TikTok,' 'viral scripts,' 'script pipeline,' 'content scraper,' or 'generate scripts from trending content.' This skill chains Apify scraping → psychological analysis → viral hooks generation → copy editing → Airtable output."
---

# Content Pipeline

You are an automated content intelligence pipeline. Your job is to scrape top-performing short-form content, analyze WHY it works psychologically, generate new scripts using proven hook patterns, edit them for quality, and deliver finished scripts ready to film.

**CRITICAL: Nothing is ever saved to disk. No local files, no pipeline-data folder, no JSON files. All data flows in memory from stage to stage. The only local file allowed is `.claude/pipeline-config.json` for saved scrape parameters. Everything else goes directly to Airtable.**

---

## Pipeline Overview

The pipeline runs in 6 stages:

```
[1] SCRAPE → [2] FILTER → [3] PSYCHOLOGY → [4] HOOKS → [5] EDIT → [6] OUTPUT
```

| Stage | What Happens |
|-------|-------------|
| 1. Scrape | Apify TikTok scraper pulls content + metrics + transcripts |
| 2. Filter | Sort by hearts, keep top performers with transcripts — in memory |
| 3. Psychology | Analyze cognitive triggers in each winning transcript — in memory |
| 4. Hooks | Generate new hook variations using viral hooks skill — in memory |
| 5. Edit | Seven Sweeps copy editing pass on each script — in memory |
| 6. Output | Finished scripts pushed directly to Airtable |

---

## Stage 1: Scrape Configuration

### Check for Saved Parameters

```bash
cat .claude/pipeline-config.json 2>/dev/null
```

**If config exists**, ask:

> Last time you ran this pipeline, you used these settings:
> - **Hashtags:** [list them]
> - **Accounts:** [list them]
> - **Min hearts:** [number]
> - **Date range:** [range]
> - **Videos to scrape:** [number]
> - **Scripts to generate:** [number]
>
> Run with the same settings, or new ones?

**If config does NOT exist** (or they want new settings), ask these questions one at a time:

1. **What hashtags should I scrape?** Give me 3-5 hashtags in your niche. No # symbol needed.

2. **Any specific TikTok accounts to scrape?** Give me usernames, or say "skip" to just use hashtags.

3. **What's your minimum hearts filter?** This filters out low-performing content. Start with 10,000 for big niches, 1,000 for smaller ones. I'll suggest a number based on your niche if you're not sure.

4. **How far back should I look?** 30 days gets you what's working NOW. 60-90 days gives more volume.

5. **How many videos should I scrape?** 50 is a good starting point. More = more credits but more data to work with.

6. **How many finished scripts do you want out of this run?** This determines how many top videos I analyze and how many scripts I generate.

After collecting answers, save config:

```bash
# Save to .claude/pipeline-config.json — this is the ONLY file we write
{
  "hashtags": ["hashtag1", "hashtag2", "hashtag3"],
  "accounts": ["username1", "username2"],
  "min_hearts": 10000,
  "date_range_days": 30,
  "num_videos": 50,
  "num_scripts": 10,
  "last_run": null
}
```

### Pre-Launch Confirmation

**HARD GATE: Before firing the scrape, explain the cost and timeout reality. Do NOT skip this.**

Say:

> **Before I run this, here's what you need to understand:**
>
> This pipeline uses Apify's sync API endpoint, which has a **300 second (5 minute) timeout.** That means:
>
> - **50 videos or less** → runs clean, no issues
> - **50-100 videos** → usually fine, but depends on how fast TikTok responds
> - **200+ videos** → will likely timeout and fail
> - **500+ videos** → will definitely timeout and you'll waste credits
>
> You set this to scrape **[X] videos**. [If over 100, say: "That's pushing it — I'd recommend dropping to 50-75 for a clean run. You can always run the pipeline again with different hashtags to get more volume."] [If 50 or under, say: "That's a safe number, no timeout risk."]
>
> This will also use Apify credits from your account. At roughly $3.70 per 1,000 results, **[X] videos will cost approximately $[calculated amount].**
>
> **Do you understand the timeout limits and want to launch the scrape?**

**Wait for explicit "yes" before proceeding. Do NOT fire the API call without confirmation.**

If they want to adjust the number of videos, update `pipeline-config.json` with the new number and re-confirm.

---

### Execute the Scrape

Read the Apify API key from `$APIFY_API_KEY` and the Content Pipeline base ID from `$CONTENT_PIPELINE_BASE_ID` (env vars in settings.json). **NEVER use `$AIRTABLE_BASE_ID` for this pipeline — that points to the klient engine base.**

Fire the TikTok scraper via Apify API and capture the response **directly into a Python variable — do NOT write to disk:**

```python
import subprocess, json

result = subprocess.run([
    'curl', '-s', '-X', 'POST',
    'https://api.apify.com/v2/acts/clockworks~tiktok-scraper/run-sync-get-dataset-items?token=TOKEN&timeout=300',
    '-H', 'Content-Type: application/json',
    '-d', json.dumps({
        "hashtags": ["hashtag1"],
        "resultsPerPage": 50,
        "shouldDownloadSubtitles": True,
        "shouldDownloadCovers": False,
        "shouldDownloadVideos": False,
        "proxyConfiguration": {"useApifyProxy": True, "apifyProxyGroups": ["RESIDENTIAL"]}
    })
], capture_output=True, text=True)

raw_data = json.loads(result.stdout)
# raw_data is now in memory — never written to disk
```

**If scraping accounts**, add to the JSON body:
```json
"profiles": ["username1"],
"profileSorting": "popular",
"profileMinHearts": 10000
```

**IMPORTANT:** `profileSorting` must be lowercase: `"popular"`, `"latest"`, or `"oldest"`. Do NOT use `"Latest"` — it will fail with an invalid-input error.

**If the API call fails or times out:**
- Tell the user what happened
- Suggest reducing the number of videos or trying fewer hashtags
- Do NOT proceed to Stage 2

---

## Stage 2: Filter Top Performers

Work entirely in memory. Do NOT write to disk.

From `raw_data`:

1. **Sort by `diggCount`** (hearts/likes) descending
2. **Filter out videos WITHOUT English subtitle links** — check `videoMeta.subtitleLinks` for a link where `language` starts with `"eng"`
3. **Apply the hearts minimum** from config
4. **Take the top N videos** where N = `num_scripts` from config (or 2x if you want buffer)
5. **Download each transcript** by fetching the subtitle URL (`subtitleLinks[0].downloadLink`) — parse the SRT/WEBVTT text to extract only the spoken words (strip timestamps and indices)
6. **Build a list of dicts in memory:**

```python
top_videos = [
    {
        "video_url": v["webVideoUrl"],
        "creator_handle": v["authorMeta"]["name"],
        "play_count": v["playCount"],
        "heart_count": v["diggCount"],
        "comment_count": v["commentCount"],
        "share_count": v["shareCount"],
        "caption": v["text"],
        "transcript": parsed_transcript_text,
        "hashtags": " ".join(["#" + h["name"] for h in v.get("hashtags", [])]),
        "create_time": v["createTimeISO"]
    }
    for v in filtered
]
# top_videos is in memory — never written to disk
```

### Log to Airtable — Scraped Videos Tab

Read `base_id` from `$CONTENT_PIPELINE_BASE_ID` env var and `scraped_table_id` from config.

```python
import urllib.request

def post_airtable(base_id, table_id, records, key):
    payload = json.dumps({"records": records}, ensure_ascii=False).encode("utf-8")
    req = urllib.request.Request(
        f"https://api.airtable.com/v0/{base_id}/{table_id}",
        data=payload,
        headers={"Authorization": f"Bearer {key}", "Content-Type": "application/json"},
        method="POST"
    )
    return json.loads(urllib.request.urlopen(req).read())

# Batch in groups of 10 (Airtable limit)
for i in range(0, len(top_videos), 10):
    batch = [{"fields": {
        "Video URL": v["video_url"],
        "Creator": v["creator_handle"],
        "Views": v["play_count"],
        "Hearts": v["heart_count"],
        "Comments": v["comment_count"],
        "Shares": v["share_count"],
        "Caption": v["caption"],
        "Transcript": v["transcript"],
        "Hashtags": v["hashtags"],
        "Date Posted": v["create_time"],
        "Scrape Date": today
    }} for v in top_videos[i:i+10]]
    post_airtable(BASE_ID, SCRAPED_TABLE_ID, batch, AIRTABLE_KEY)
```

---

## Stage 3: Psychology Analysis

Work entirely in memory. Do NOT write to disk.

For EACH top-performing transcript in `top_videos`, analyze it using the marketing psychology mental models below.

**Triggers to scan for:**

| Category | Triggers |
|----------|----------|
| **Attention** | Curiosity gap, pattern interrupt, open loop, novelty, controversy |
| **Desire** | Mimetic desire, loss aversion, FOMO, identity signaling, aspiration gap |
| **Trust** | Authority, social proof, specificity, vulnerability/pratfall effect |
| **Action** | Scarcity, urgency, commitment/consistency, goal-gradient, zero-risk |
| **Retention** | Zeigarnik effect (open loops), serial position effect (strong open/close), peak-end rule |

**For each transcript, identify:**
- Top 3 psychological triggers present
- WHERE in the transcript each trigger appears (quote the line)
- WHY that trigger works in this context
- How to replicate that trigger for a different offer

Store as a list of dicts in memory alongside `top_videos`. Use this in Stage 4. Do NOT log psychology to Airtable unless the user asks.

---

## Stage 4: Viral Hooks Generation

**Read these files before generating:**
- `skills/viral-hooks-skill/references/hooks-library.md` — the 1,000+ hook templates
- `skills/viral-hooks-skill/references/seven-factors-framework.md` — the 7-factor system

Work entirely in memory. Do NOT write to disk.

**For each script to generate** (based on `num_scripts` from config):

1. **Pick a source video** from `top_videos`
2. **Match the psychological triggers** identified in Stage 3
3. **Select hooks from the hooks library** that leverage those same triggers
4. **Apply the 7-Factor Framework:**
   - **Topic:** Adapt the source video's topic to the user's niche/offer
   - **Hooks:** Verbal + Written + Visual — pulled from hooks-library.md, adapted with the psychological triggers
   - **Value:** Define the specific measurable takeaway for the viewer
   - **Script Angle:** Choose from the 7 angles (tutorial, comparison, myth bust, do's vs don'ts, tip/hack, transformation, challenge)
   - **CTA:** Match to the user's goal
   - **Format:** Suggest filming format
   - **Editing Style:** Brief notes on text overlays, pacing, transitions

5. **Write the full script** — not just a hook, the complete spoken-word script from open to CTA. Include:
   - Hook (verbal — what they say in first 3 seconds)
   - Hook (written — text overlay)
   - Hook (visual — what the viewer sees)
   - Body (the value delivery)
   - CTA (the close)
   - Estimated length (in seconds)

Proceed directly to Stage 5 copy editing in memory.

---

## Stage 5: Copy Editing — Seven Sweeps

Work entirely in memory. Do NOT write to disk.

Run each generated script through these sweeps:

### Sweep 1: Clarity
- Can the viewer understand what you're saying in ONE listen?
- No jargon, no compound sentences, no ambiguity
- Short-form = spoken word. If it doesn't sound natural spoken aloud, rewrite it.

### Sweep 2: Voice and Tone
- Is it consistent? Does it sound like one person talking?
- Does it match the energy of the platform (TikTok = casual, fast, direct)?
- Kill anything that sounds like a blog post or marketing email

### Sweep 3: So What
- Does every line earn its place?
- Every claim should connect to "why should I care?" within the same breath
- In short-form, you don't get a second chance — if a line doesn't hit, cut it

### Sweep 4: Specificity
- Replace vague language with concrete numbers, timeframes, outcomes
- "A lot of money" → "$4,700 in the first month"
- "Really fast" → "In under 3 minutes"

### Sweep 5: Hook Strength
- Re-examine the first 3 seconds — is the hook strong enough to stop the scroll?
- Does the written text overlay create a curiosity gap?
- Does the visual hook match the verbal energy?

### Sweep 6: CTA Tightness
- Is the CTA clear, single-action, and compelling?
- Does it flow naturally from the content, not feel bolted on?
- Is it appropriate for the platform?

### Sweep 7: Final Trim
- Read the entire script. Cut any word that doesn't need to be there.
- Target: every script should be as tight as possible without losing meaning
- Short-form scripts should be 30-90 seconds when spoken. If it's longer, cut.

**Use the plain-english-alternatives reference** to catch corporate/complex language:
- Read `references/plain-english-alternatives.md` for word-level replacements
- "Utilize" → "Use," "Facilitate" → "Help," "Implement" → "Set up"
- Cut filler: "basically," "actually," "really," "very," "just"

Proceed directly to Stage 6 and write to Airtable.

---

## Stage 6: Output to Airtable

Scripts are in memory from Stage 5. Use `$CONTENT_PIPELINE_BASE_ID` env var and `scripts_table_id` from config.

```python
records = [{"fields": {
    "Script Title": s["title"],
    "Hook (Verbal)": s["hook_verbal"],
    "Hook (Written)": s["hook_written"],
    "Hook (Visual)": s["hook_visual"],
    "Full Script": s["full_script"],
    "Psychology Triggers": s["psychology"],
    "Source Video URL": s["source_url"],
    "Script Angle": s["angle"],
    "Format": s["format"],
    "CTA": s["cta"],
    "Estimated Length": s["length"],
    "Generated Date": today
}} for s in scripts]

# Batch in groups of 10
for i in range(0, len(records), 10):
    post_airtable(BASE_ID, SCRIPTS_TABLE_ID, records[i:i+10], AIRTABLE_KEY)
```

After all scripts are saved, output:

```
╔══════════════════════════════════════════════════════════╗
║                                                          ║
║  PIPELINE COMPLETE                                       ║
║                                                          ║
║  Scraped: [X] videos                                    ║
║  Top performers: [Y] videos with transcripts            ║
║  Scripts generated: [N] finished scripts                 ║
║                                                          ║
║  Airtable: Content Pipeline base                         ║
║     Scraped Videos tab: raw data logged                  ║
║     Generated Scripts tab: finished scripts ready        ║
║                                                          ║
╚══════════════════════════════════════════════════════════╝
```

Then show a brief summary of each generated script — title, hook, angle, estimated length — so the user can see what they got without opening Airtable.

---

## References

- **Marketing Psychology:** 70+ mental models embedded in Stage 3 (see trigger table above)
- **Viral Hooks:** `skills/viral-hooks-skill/references/hooks-library.md` — 1,000+ hook templates
- **7-Factor Framework:** `skills/viral-hooks-skill/references/seven-factors-framework.md` — complete content framework
- **Plain English:** `references/plain-english-alternatives.md` — word-level replacements for copy editing
- **Copy Editing Framework:** Seven Sweeps adapted for short-form scripts (Stage 5)

---

## Error Handling

- **Apify scrape fails:** Tell user, suggest reducing scope, do not proceed
- **No transcripts returned:** Tell user that TikTok captions weren't available for those videos. Suggest trying different hashtags or accounts with more talking-head content.
- **Airtable insert fails:** Batch size issue — retry in smaller batches
- **Not enough top performers:** If fewer than `num_scripts` videos have transcripts, tell user and generate scripts for however many are available
