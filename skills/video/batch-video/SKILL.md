---
name: batch-video
description: Bulk talking head video pipeline — one avatar, one environment, 10 scripts, 10 hooks each = 100 videos. Collects everything upfront, confirms cost once, then runs hands-free.
---

# Batch Video — Bulk Pipeline
Path: /skills/video/batch-video/SKILL.md

This skill generates 100 talking head videos in a single run.
Same avatar. Same environment. Same base image. 10 different scripts. 10 hook variations per script.

Unlike the single talking-head skill, there are NO per-video approval gates.
All settings are collected upfront. One cost confirmation. Then it runs.

---

## STEP 1 — AVATAR + ENVIRONMENT SELECTION

Ask for BOTH at once in ONE message (same as talking-head skill):

"Pick your avatar AND environment — same pair will be used for all 100 videos.

👉 https://airtable.com/appYKA4l79ZkD1Z0c/shrjUPhqQ45fSRCMw

Give me both:
1. Avatar — exact name or CUSTOM
2. Environment — exact name or describe it casually"

Wait. Apply fuzzy matching exactly as the talking-head skill does.

Load avatar SKILL.md → extract [AVATAR_PROMPT], [AVATAR_NEGATIVE_PROMPT], [AVATAR_REFERENCE_URL]
Load environment SKILL.md → extract [ENVIRONMENT_PROMPT]

Store as [AVATAR_NAME] and [ENVIRONMENT_NAME].

---

## STEP 2 — BUILD AND GENERATE BASE IMAGE

Assemble [IMAGE_PROMPT] exactly as talking-head skill Step 2.

Tell the user: "Base image cost: **$0.15** (fal-ai/nano-banana-pro) — one image shared across all 100 videos."

Generate ONE image via fal.ai — this is the shared start frame for all 100 videos.

Use fal-ai/nano-banana-pro/edit with image_urls: [[AVATAR_REFERENCE_URL]] if reference exists.
Use fal-ai/nano-banana-pro base model only if no reference.

```json
{
  "app_id": "fal-ai/nano-banana-pro/edit",
  "input_data": {
    "prompt": "[IMAGE_PROMPT]",
    "image_urls": ["[AVATAR_REFERENCE_URL]"],
    "aspect_ratio": "9:16",
    "num_images": 1
  }
}
```

Poll with mcp__fal__status until COMPLETED. Fetch with mcp__fal__result.
Store result as [BASE_IMAGE_URL].

Embed the image inline — mandatory:
![AVATAR_NAME in ENVIRONMENT_NAME](BASE_IMAGE_URL)

Tell the user:
"This is the start frame for all 100 videos. Approve it or I'll regenerate before we go any further."

Wait. If rejected → adjust [IMAGE_PROMPT] and regenerate. Loop until approved.

---

## STEP 3 — COLLECT 10 SCRIPTS

Tell the user:

"Give me 10 topics/scripts — one per line. These are the 10 subjects your 100 videos will cover.

Each one should be 1-3 sentences describing what the video is about. Example:

1. Why most business owners waste money on ads before fixing their offer
2. The one AI tool I'd keep if I could only pick one
3. How I used AI to save 10 hours a week in my business
... and so on

Paste all 10 now."

Wait. Store as [SCRIPT_LIST] (array of 10 strings, indexed 1–10).

---

## STEP 4 — GENERATE ALL 100 HOOKS

Read the hooks library at /viral-hooks-skill/references/hooks-library.md.

For each script in [SCRIPT_LIST], generate 10 hook variations.
Rules for every hook:
- Under 3 seconds when spoken aloud
- Mix styles across the 10: curiosity, bold claim, pattern interrupt, personal story, controversial take, myth-bust, authority, relatable fail, number-driven, direct call-out
- Written as spoken dialogue — no hashtags, no emojis, no caption language
- Tailored specifically to the script topic, not generic

Output format:

**Script 1: [script topic summary]**
1. [hook]
2. [hook]
...
10. [hook]

**Script 2: [script topic summary]**
...

Display all 100 hooks (all 10 scripts × 10 hooks).

Then ask:

"Here are all 100 hooks. Want to swap any out before I generate? 
Type the script number and hook number you want to change plus your replacement — or type GO to run the full batch as-is."

Wait. If swaps requested → update [HOOK_MATRIX] accordingly. Loop until user types GO.

Store final approved set as [HOOK_MATRIX] — a 10×10 array where [HOOK_MATRIX][script][hook] = hook text.

---

## STEP 5 — VIDEO SETTINGS (SET ONCE FOR ALL 100)

Show the user this cost reference table BEFORE asking settings:

"Here's what 100 videos will cost at different durations and audio modes:

| Duration | Audio OFF ($0.112/s) | Audio ON ($0.168/s) | Audio ON + Voice ($0.196/s) |
|----------|---------------------|--------------------|-----------------------------|
| 5s       | $56.15              | $84.15             | $98.15                      |
| 10s      | $112.15             | $168.15            | $196.15                     |
| 15s      | $168.15             | $252.15            | $294.15                     |
| 30s      | $336.15             | $504.15            | $588.15                     |

*All totals include $0.15 for the base image. Duration can be anything — not limited to examples above.*

Now answer all three:
1. Video length — how many seconds per video? (recommend 15–30)
2. Audio — ON, OFF, or ON + Voice Control?
3. Video model — Kling 3.0 Pro is the default. Type YES to confirm or name another model."

Wait. Store:
- [VIDEO_DURATION] — seconds
- [AUDIO_MODE] — OFF / ON / ON+VOICE
- [VIDEO_MODEL] — default: Kling 3.0 Pro
- [VIDEO_ENDPOINT] — map as:
  - Kling 3.0 Pro → fal-ai/kling-video/v3/pro/image-to-video
  - Veo 3.1 → fal-ai/veo3/image-to-video
  - Sora 2 → fal-ai/sora/image-to-video

Map [AUDIO_MODE] to rate:
- Audio OFF → $0.112/sec
- Audio ON → $0.168/sec
- Audio ON + Voice Control → $0.196/sec

---

## STEP 6 — COST CONFIRMATION GATE

Calculate:
[COST_PER_VIDEO] = [VIDEO_DURATION] × rate (rounded to 4 decimal places)
[VIDEO_TOTAL] = [COST_PER_VIDEO] × 100 (rounded to 2 decimal places)
[TOTAL_COST] = $0.15 (base image) + [VIDEO_TOTAL] (rounded to 2 decimal places)

Tell the user:

"Cost breakdown for the full batch:

- Base image: $0.15 (one image, shared across all 100 videos)
- Videos: 100 × [VIDEO_DURATION]s @ $[rate]/sec ([AUDIO_MODE]) = $[VIDEO_TOTAL]
- **Total estimated cost: $[TOTAL_COST]**

Once you say GO this runs hands-free until all 100 are done. No more stops.

Type GO to start the batch or CANCEL to exit."

Wait. If CANCEL → stop immediately. If GO → proceed to Step 7.

---

## STEP 7 — LOG BASE IMAGE TO AIRTABLE

Read AIRTABLE_BASE_ID and AIRTABLE_API_KEY from ~/.claude/settings.json.

Create a batch header record in the Projects table:

```
POST https://api.airtable.com/v0/{AIRTABLE_BASE_ID}/Projects
Authorization: Bearer [AIRTABLE_API_KEY]
```

Field | Value
Ad Name | BATCH — [AVATAR_NAME] — [ENVIRONMENT_NAME] — [today's date]
Image Prompt | [IMAGE_PROMPT]
Image Generator | fal.ai
Image Status | Generated
Generated Image 1 | [BASE_IMAGE_URL]

Store as [BATCH_HEADER_RECORD_ID].

Also log/update avatar in AI Avatars table and environment in AI Environments table — same as talking-head skill Steps 4B and 4C.

---

## STEP 8 — BATCH VIDEO LOOP

Run the following loop for all 100 combinations.
Outer loop: scripts 1–10. Inner loop: hooks 1–10 per script.

**For each video (script S, hook H):**

Tell the user: "Generating video [CURRENT] of 100 — Script [S], Hook [H]..."

**8A — Build video prompt:**

[AVATAR_NAME] speaking directly to camera in [ENVIRONMENT_NAME].
Opening line (first words spoken): "[HOOK_MATRIX][S][H]"
Topic: [SCRIPT_LIST][S]
Tone: confident, direct, conversational
Movement: subtle natural head movement, steady eye contact with camera
Lighting: consistent with [ENVIRONMENT_NAME]
Duration: [VIDEO_DURATION] seconds
Start frame: [BASE_IMAGE_URL]

Store as [VIDEO_PROMPT_SH].

**8B — Generate video via fal.ai curl:**

```bash
curl -s -X POST "https://queue.fal.run/[VIDEO_ENDPOINT]" \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "[VIDEO_PROMPT_SH]",
    "image_url": "[BASE_IMAGE_URL]",
    "aspect_ratio": "9:16",
    "duration": "[VIDEO_DURATION]",
    "enable_audio": [true/false based on AUDIO_MODE]
  }'
```

Read FAL_KEY from ~/.claude/settings.json.
Poll status until COMPLETED. Retrieve video URL.
Store as [VIDEO_URL_SH].

**8C — Log to Airtable (Projects table, one row per video):**

```
POST https://api.airtable.com/v0/{AIRTABLE_BASE_ID}/Projects
Authorization: Bearer [AIRTABLE_API_KEY]
```

Field | Value
Ad Name | [AVATAR_NAME] — Script [S] Hook [H] — [today's date]
Product Name | extracted from [SCRIPT_LIST][S]
Image Prompt | [IMAGE_PROMPT]
Image Generator | fal.ai
Image Status | Generated
Generated Image 1 | [BASE_IMAGE_URL]
Video Prompt | [VIDEO_PROMPT_SH]
Video Generator | [VIDEO_MODEL]
Video Status | Complete
Video Generation 1 | [VIDEO_URL_SH]

**8D — Advance counter and repeat.**

After every 10 videos, report progress:
"10 videos done. [X] remaining. Still running..."

---

## STEP 9 — BATCH SUMMARY

When all 100 are complete, output a summary table:

| # | Script | Hook | Video URL |
|---|--------|------|-----------|
| 1 | [script topic] | [hook text] | [url] |
| 2 | ... | ... | ... |
...

Then tell the user:

"✅ Batch complete. 100 videos generated and logged to Airtable.
Check your Projects table — every video is there with its hook and prompt.
Total spend: ~$[TOTAL_COST]"

---

## Key Defaults

| Setting | Default |
|---|---|
| Image model | fal-ai/nano-banana-pro/edit |
| Video model | Kling 3.0 Pro |
| Aspect ratio | 9:16 vertical |
| Scripts | 10 |
| Hooks per script | 10 |
| Total videos | 100 |
| Approval gates | One (cost confirmation only) |

---

## File Path Reference

| Asset | Path |
|---|---|
| This skill | /skills/video/batch-video/SKILL.md |
| Single video skill | /skills/video/talking-head-video/SKILL.md |
| Hooks library | /viral-hooks-skill/references/hooks-library.md |
| Avatar skills | /skills/ai-avatars/[name]/SKILL.md |
| Environment skills | /skills/ai-environments/[name]/SKILL.md |
