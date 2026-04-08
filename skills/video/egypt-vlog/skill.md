---
name: egypt-vlog
description: "End-to-end ancient Egyptian time-travel vlog pipeline. Generates cinematic image prompts, sends to fal.ai for image generation, stores in Airtable, gets user approval, then generates video with SFX/duration/quality options and stores everything in Airtable. Use when the user asks to create Egypt vlog content, Egyptian construction scenes, or time-travel vlog images/videos."
---

This skill runs an end-to-end ancient Egyptian time-travel vlog pipeline:

1. Ask scene questions -> generate cinematic image prompts
2. Automatically send prompts to fal.ai for image generation
3. Store images + prompts in Airtable
4. Wait for user approval
5. Ask video options (SFX, duration, quality)
6. Automatically generate video from approved image via fal.ai
7. Store video + prompt in Airtable

Each image prompt follows a strict formula that creates immersive, historically accurate "front-facing phone camera" shots -- as if a modern vlogger were embedded in ancient Egypt.

---

## Step 1: Ask the User These Questions ONE AT A TIME

**Ask each question individually. Wait for the user's reply before moving to the next question. Do not skip ahead. Do not assume answers. Do not generate anything until all questions are answered.**

**Use the AskUserQuestion tool for EVERY question so the user gets clickable selection buttons.** Present the options as the `options` array parameter. This is mandatory — never present questions as plain text.

### Question 1: Format

Ask using AskUserQuestion with these exact options:

- Question: `Single scene or full series?`
- Options: `["Single scene (1 prompt)", "Full episode series (5–7 prompts, dawn → dusk)"]`

### Question 2: Character

Ask using AskUserQuestion with these exact options:

- Question: `Which character?`
- Options: `["🪨 Young stone mason — quarry at dawn", "🏗️ Ramp worker — hauling blocks uphill", "📐 Architect's apprentice — high staging area overview", "🌞 General laborer — workers' camp midday", "🌙 Master builder — final stones at dusk", "⚖️ Supply scribe — harbor delivery zone", "🛶 Nile boat worker — limestone transport barge", "🔨 Copper chisel smith — metalworking station", "🍞 Camp baker — workers' village cookfire", "🏛️ Priest-overseer — corner marker alignment ritual", "Surprise me"]`

### Question 3: Time of day (skip if user chose full series)

Ask using AskUserQuestion with these exact options:

- Question: `What time of day?`
- Options: `["🌅 Dawn", "☀️ Morning", "🌞 Midday", "🌇 Golden hour", "🌆 Dusk", "🌙 Night"]`

### Question 4: Vibe / expression

Ask using AskUserQuestion with these exact options:

- Question: `What's the character's vibe?`
- Options: `["Overwhelmed and in awe", "Focused and intense", "Tired but proud", "Excited and wide-eyed", "Strained and determined"]`

### Question 5: Background action

Ask using AskUserQuestion with these exact options:

- Question: `Any specific background action?`
- Options: `["Workers chanting while hauling", "Priests doing alignment ritual", "Oxen dragging blocks", "Nile supply boats arriving", "Scribes counting deliveries", "Your call (auto-select best fit)"]`

---

Once all 5 questions are answered, map the inputs directly into the formula below and generate the prompt(s). Then immediately proceed to Step 3 (Image Generation). Do not ask any follow-up questions between prompt generation and image generation.

---

## Step 2: The Core Prompt Formula

Every prompt must be structured in this exact order — no exceptions:

1. **Shot type**: `A realistic cinematic vlog-style front-facing phone shot of [CHARACTER] in Old Kingdom Egypt during the construction of the Great Pyramid at [SPECIFIC LOCATION].`
2. **Camera mechanics** *(never change this line)*: `The shot is captured from the character's front-facing phone camera at arm's length, with the recording device not visible in frame and no separate camera shown in the hand.`
3. **Character reaction**: `The character is facing the lens with [EXPRESSION], reacting to [SPECIFIC EVENT OR SIGHT].`
4. **Historical accuracy block**: `The scene must be accurate to the chosen setting, with appropriate [ERA-SPECIFIC CLOTHING, TOOLS, PROPS, and DETAILS].`
5. **Background activity**: `In the background, show [3–5 SPECIFIC BACKGROUND ACTIONS OR SCENES].`
6. **Technical integration**: `The character must feel naturally integrated into the environment with matching ambient light, matching color temperature, consistent shadows, realistic contact shadows, realistic skin tones, atmospheric depth, and scene-consistent cinematic color grading.`
7. **Color mandate**: `Full color, rich natural colors, vivid but realistic color grading, not black and white, not monochrome, not sepia.`
8. **Closing tags**: `Cinematic realism, immersive atmosphere, detailed surroundings, natural lighting, believable arm perspective, no mirror selfie, no tripod, high detail.`

---

## Egyptian Character Roster

| Emoji | Character | Role in Story |
|-------|-----------|---------------|
| 🪨 | Young stone mason | Quarry worker cutting limestone at dawn |
| 🏗️ | Ramp worker | Hauling blocks up the main supply ramp |
| 📐 | Architect's apprentice | Overseeing layout from a high staging area |
| 🌞 | General laborer | Working the midday camp support operations |
| 🌙 | Master builder | Final inspection of stones placed at dusk |
| ⚖️ | Supply scribe | Recording deliveries at the harbor staging area |
| 🛶 | Nile boat worker | Unloading limestone from transport barges |
| 🔨 | Copper chisel smith | Resharpening tools at the metalworking station |
| 🍞 | Camp baker | Feeding the labor force at the workers' village |
| 🏛️ | Priest-overseer | Conducting alignment ritual at a corner marker |

---

## Egyptian Historical Accuracy Details

**Clothing & Appearance**
- Simple white or off-white linen kilts (workers), finer linen for supervisors
- Shaved heads or short hair, some with head cloths
- Dusty, sun-darkened skin with sweat
- Bare feet or simple leather sandals
- Eye kohl in some cases (both genders)

**Tools & Props**
- Copper chisels, wooden mallets, dolerite pounding balls
- Wooden sledges with rope rigging
- Wooden levers, rollers under stone blocks
- Water pots, bread baskets, clay beer jugs
- Reed measuring cords, wooden set squares, plumb bobs
- Writing palettes, reed brushes, limestone ostraca (for scribes)

**Setting Details by Location**
- **Quarry**: Limestone outcropping, tool marks, copper chisel fragments, workers in trenches
- **Ramp**: Mudbrick and rubble ramp, workers pouring water on sand, chanting teams
- **High staging area**: Sweeping view of plateau, supply lines, Nile in distance
- **Workers' camp**: Bakeries, breweries, medical tents, cookfires, rotation areas
- **Dusk**: Fading orange light on limestone, exhausted but proud workers, torchlight

**Background Activity Options**
- Teams chanting rhythmically while hauling ropes
- Overseers signaling with hands or wooden staves
- Workers pouring water over sand ahead of sledges
- Priests performing alignment rituals at corner markers
- Supply boats on the Nile in the far distance
- Oxen dragging material loads
- Scribes recording block counts on limestone ostraca
- Medics treating injured workers
- Bakers pulling bread from clay ovens
- Pyramid rising layer by layer under specific light conditions

---

## Time-of-Day Lighting Guide

| Time | Light Quality | Color Temperature |
|------|--------------|-------------------|
| Dawn | Orange-pink horizon glow, long shadows | Warm amber |
| Morning | Bright directional sunlight, crisp | Golden white |
| Midday | Harsh overhead sun, heat shimmer | Bleached warm |
| Golden hour | Soft directional, long golden shadows | Deep gold |
| Dusk | Fading orange/purple sky, torchlight | Amber-violet |
| Night | Torch and fire light only | Deep warm orange |

---

## Output Format

- Emoji + scene title header (e.g., `🪨 Quarry Run at Sunrise`)
- Full prompt as a single paragraph — no line breaks mid-prompt
- Target length: 180–260 words per prompt
- If full series: chronological order dawn → dusk

---

## Example Output

**🪨 Quarry Run at Sunrise**

A realistic cinematic vlog-style front-facing phone shot of a young ancient Egyptian stone mason in Old Kingdom Egypt during the construction of the Great Pyramid at the Giza limestone quarry. The shot is captured from the character's front-facing phone camera at arm's length, with the recording device not visible in frame and no separate camera shown in the hand. The character is facing the lens with a focused, slightly overwhelmed expression, reacting to enormous limestone blocks being cut and hauled toward the rising pyramid. The scene must be accurate to the chosen setting, with appropriate linen kilt, shaved head, dusty sun-darkened skin, copper chisels, dolerite pounding stones, rope rigging, and wooden sledge runners visible nearby. In the background, show quarry workers cutting trenches into the bedrock, overseers signaling teams with wooden staves, oxen dragging sledges loaded with rough blocks, scribes recording deliveries on limestone ostraca, and the partially-risen pyramid glowing amber under the first light of dawn. The character must feel naturally integrated into the environment with matching ambient light, matching color temperature, consistent shadows, realistic contact shadows, realistic skin tones, atmospheric depth, and scene-consistent cinematic color grading. Full color, rich natural colors, vivid but realistic color grading, not black and white, not monochrome, not sepia. Cinematic realism, immersive atmosphere, detailed surroundings, natural lighting, believable arm perspective, no mirror selfie, no tripod, high detail.

---

## Hard Rules

- Camera mechanics line is **word-for-word every time** — never paraphrase it
- Color mandate block is **non-negotiable** — always include it
- Closing tags always end with `no mirror selfie, no tripod, high detail`
- No modern objects or anachronistic props ever
- Worker/ground-level POV only — no pharaohs, no royalty

---

## Step 3: Image Generation (Automatic)

**After generating the prompt(s), tell the user the image cost, then immediately send to fal.ai. Do not ask for approval — just generate.**

Image cost: $0.15 per image (fal-ai/nano-banana-pro)
- Single scene: **$0.15**
- Full series (5 prompts): **$0.75** / (7 prompts): **$1.05**

Tell the user: "Generating [N] image[s] — estimated cost: $[COST]. Starting now..."

### How to Generate

Use `mcp__fal__generate` with the following parameters:

```json
{
  "app_id": "fal-ai/nano-banana-pro",
  "input_data": {
    "prompt": "<the full generated prompt from Step 2>",
    "num_images": 1,
    "aspect_ratio": "9:16",
    "output_format": "png",
    "resolution": "2K"
  }
}
```

- For a **single scene**: generate 1 image from the 1 prompt
- For a **full series**: generate 1 image per prompt (5–7 images total). Send all generation requests in parallel.

### After Generation

Once fal.ai returns the image URL(s), display them to the user inline and proceed immediately to Step 4.

---

## Step 4: Store Images in Airtable (Automatic)

**Immediately after image generation, create a record in Airtable for each image. Do not ask the user.**

### Airtable Setup

- First, use `mcp__airtable__list_bases` to find the user's base
- Then use `mcp__airtable__list_tables` to find or confirm a table called **"Time Travel Vlog"**
- If the table doesn't exist, create it with `mcp__airtable__create_table` using these fields:

| Field Name         | Type              | Description                                    |
|--------------------|-------------------|------------------------------------------------|
| Scene Title        | singleLineText    | Emoji + title (e.g., "🛶 First Light on the Nile Barge") |
| Character          | singleLineText    | Character name from the roster                 |
| Time of Day        | singleLineText    | Dawn, Morning, Midday, etc.                    |
| Image Prompt       | multilineText     | The full image generation prompt               |
| Image URL          | url               | The generated image URL from fal.ai            |
| Image Status       | singleSelect      | Options: Pending, Approved, Rejected           |
| Video Prompt       | multilineText     | The video motion prompt (filled later)         |
| Video URL          | url               | The generated video URL (filled later)         |
| Video Status       | singleSelect      | Options: Pending, Approved, Rejected           |
| SFX Enabled        | checkbox          | Whether audio/SFX was requested                |
| Video Duration     | singleLineText    | Duration in seconds                            |
| Video Quality      | singleLineText    | 1K, 2K, 3K, or 4K                             |

### Creating Records

For each generated image, use `mcp__airtable__create_record`:

```json
{
  "baseId": "<base_id>",
  "tableId": "Time Travel Vlog",
  "fields": {
    "Scene Title": "<emoji + title>",
    "Character": "<character name>",
    "Time of Day": "<time of day>",
    "Image Prompt": "<full prompt text>",
    "Image URL": "<fal.ai image url>",
    "Image Status": "Pending"
  }
}
```

After storing, tell the user: "Images generated and saved to Airtable! Take a look and let me know what you think."

---

## Step 5: User Review (Wait for Confirmation)

**FULL STOP. Wait for the user to review the generated images.**

Ask using AskUserQuestion:

- Question: `What do you think of the image(s)?`
- Options:
  - `["Love it — let's make a video!", "Not quite — regenerate with tweaks", "Scrap it — start over"]`

### If the user likes the image(s):
- Update the Airtable record's **Image Status** to **"Approved"** using `mcp__airtable__update_records`
- Proceed to Step 6 (Video Questions)

### If the user wants tweaks:
- Ask what they'd like changed
- Regenerate with an adjusted prompt
- Update the Airtable record with the new image URL and prompt
- Ask for review again

### If the user wants to start over:
- Update the Airtable record's **Image Status** to **"Rejected"**
- Go back to Step 1

---

## Step 6: Video Generation Questions

**Only proceed here after the user has approved the image(s).**

First, show the user this cost matrix before asking anything:

"Here's what video generation costs per clip by quality and duration:

| Quality | Model | 5s | 10s |
|---------|-------|----|-----|
| 1K | Kling v3 Standard | $0.70 | $1.40 |
| 2K | Kling v3 Pro | $0.70 | $1.40 |
| 3K | Veo 3.1 | $2.00 | $4.00 |
| 4K | Sora | TBD | TBD |

*Single scene = 1 clip. Full series = 5–7 clips. Multiply per-clip cost by number of scenes for total.*
*Audio ON adds extra cost on top of these base rates.*"

Then ask these questions ONE AT A TIME using AskUserQuestion.

### Question V1: Sound Effects

- Question: `Would you like SFX / audio? (this costs more)`
- Options: `["Yes — add ambient sound and SFX", "No — silent video is fine"]`

### Question V2: Video Duration

- Question: `How long should the video be? (longer = more expensive)`
- Options: `["5 seconds (standard)", "10 seconds (extended)"]`

### Question V3: Video Quality

- Question: `What quality? (higher = more expensive)`
- Options: `["1K (budget) — $0.70/clip at 5s", "2K (standard) — $0.70/clip at 5s", "3K (high quality) — $2.00/clip at 5s", "4K (premium) — Sora pricing TBD"]`

---

## Step 7: Video Generation (Automatic)

**After all video questions are answered, calculate the cost and tell the user before generating.**

Video cost rates:
| Quality | Model | Rate |
|---------|-------|------|
| 1K | Kling v3 Standard | $0.14/sec |
| 2K | Kling v3 Pro | $0.14/sec |
| 3K | Veo 3.1 | $0.40/sec |
| 4K | Sora | check fal.ai for current pricing |

Calculate:
- [VIDEO_COST_PER_CLIP] = [DURATION] × rate for chosen quality
- [TOTAL_VIDEO_COST] = [VIDEO_COST_PER_CLIP] × [N_SCENES]
- [TOTAL_RUN_COST] = image cost + [TOTAL_VIDEO_COST]

Confirm the specific cost to the user based on their answers before generating:

"Your selections:
- Quality: [QUALITY] ([MODEL]) — $[rate]/sec
- Duration: [DURATION]s per clip
- Audio/SFX: [SFX_MODE]
- Scenes: [N_SCENES]

Cost: [N_SCENES] clip[s] × [DURATION]s × $[rate]/sec = **$[TOTAL_VIDEO_COST]**
Images already generated: $[IMAGE_COST]
**Total run cost: $[TOTAL_RUN_COST]**

Generating now..."

### Building the Video Prompt

Create a motion prompt that describes how the scene comes to life. Use the **image-to-video** approach — the approved image IS the first frame. The video prompt should only describe MOVEMENT, not re-describe the scene.

**Video prompt formula for time-travel vlog scenes:**

```
[Anchor] <Re-state key visual elements: character appearance, clothing, what they're holding, setting>

[Motion] <Describe 2-3 subtle movements: character expression shift, background worker activity, environmental motion like dust/smoke/water>

[Camera] <One camera movement: slow push-in, gentle drift, static with subject movement>

[Negative] No sudden movements. No camera shake. No text overlays. No modern objects.
```

**Example video prompt:**
```
A Nile boat worker in a linen kilt with a damp head cloth over one shoulder, gripping thick palm-fiber rope on a transport barge dock. He looks tired but proud, facing the camera at arm's length. The partially completed Great Pyramid glows amber in the dawn light behind him. He exhales slowly, his expression shifting from fatigue to quiet satisfaction. In the background, dock workers roll a limestone block down a wooden ramp, and a second barge glides in with its sail catching the morning light. Dust particles drift through the warm amber air. The camera holds still with a very slow, subtle push-in. No sudden movements. No camera shake. No text overlays. No modern objects.
```

### Sending to fal.ai

Use `mcp__fal__generate` with:

```json
{
  "app_id": "fal-ai/kling-video/v3/pro/image-to-video",
  "input_data": {
    "prompt": "<video motion prompt>",
    "image_url": "<approved_image_url from Step 3>",
    "duration": "<5 or 10 based on user choice>",
    "aspect_ratio": "9:16",
    "generate_audio": <true if user chose SFX, false otherwise>
  }
}
```

**Model selection by quality:**
| Quality | Model                                              |
|---------|-----------------------------------------------------|
| 1K      | `fal-ai/kling-video/v3/standard/image-to-video`   |
| 2K      | `fal-ai/kling-video/v3/pro/image-to-video`        |
| 3K      | `fal-ai/veo3/image-to-video`                       |
| 4K      | `fal-ai/sora/image-to-video`                       |

For a **full series**: generate videos for all approved images in parallel.

### After Video Generation

Once fal.ai returns the video URL(s), display them to the user inline.

---

## Step 8: Store Video in Airtable (Automatic)

**Immediately after video generation, update the existing Airtable record(s) with the video data.**

Use `mcp__airtable__update_records` to update each record:

```json
{
  "baseId": "<base_id>",
  "tableId": "Time Travel Vlog",
  "records": [
    {
      "id": "<record_id from Step 4>",
      "fields": {
        "Video Prompt": "<the video motion prompt>",
        "Video URL": "<fal.ai video url>",
        "Video Status": "Pending",
        "SFX Enabled": <true/false>,
        "Video Duration": "<5s or 10s>",
        "Video Quality": "<1K/2K/3K/4K>"
      }
    }
  ]
}
```

After storing, tell the user: "Video generated and saved to Airtable! Here's your time-travel vlog scene."

---

## Full Pipeline Summary

```
Step 1: Ask 5 scene questions (AskUserQuestion, one at a time)
  ↓
Step 2: Generate image prompt(s) using the formula
  ↓
Step 3: Send prompt(s) to fal.ai for image generation (automatic)
  ↓
Step 4: Store image(s) + prompt(s) in Airtable (automatic)
  ↓
Step 5: ⏸ WAIT — user reviews images
  ↓  ✅ Approved → continue     ❌ Rejected → regenerate or restart
Step 6: Ask 3 video questions (SFX, duration, quality)
  ↓
Step 7: Generate video from approved image via fal.ai (automatic)
  ↓
Step 8: Store video + prompt in Airtable (automatic)
```


use this image as a reference "C:\Users\Corey Figueroa\Downloads\IMG_3519.PNG"