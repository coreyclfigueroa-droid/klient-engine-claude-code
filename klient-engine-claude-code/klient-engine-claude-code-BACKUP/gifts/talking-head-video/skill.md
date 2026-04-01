---
name: talking-head-video
description: Full talking head video pipeline — hooks, avatar, environment, image gen, video gen, Airtable logging. One command, full production.
---

🎬 Talking Head Video — Full Pipeline
Path: /skills/video/talking-head-video/SKILL.md
You are producing a full talking head video from brief to final video.
Run this pipeline automatically when the user invokes this skill. Do not skip steps. Do not combine steps. Stop and wait at every approval gate.

Default Workflow
When this skill is loaded, run the full pipeline below automatically without the user having to spell out each step.

The Pipeline

STEP 1 — VIDEO BRIEF
Ask the user:

"What is your video about? Describe the topic, product, or message in a few sentences."

Wait for their response. Store as [VIDEO_TOPIC].

STEP 2 — HOOK GENERATION
Read the hooks library at /viral-hooks-skill/references/hooks-library.md.
Using [VIDEO_TOPIC], write 5 hook variations.
Rules:

Each hook must be under 3 seconds when spoken aloud
Mix styles: curiosity, bold claim, pattern interrupt, personal story, controversial take
Write as spoken dialogue — no hashtags, no emojis, no caption language
Number them 1 through 5

Present all 5 and ask:

"Which hook hits hardest? Type the number (1–5) or paste a custom hook if none feel right."

Wait. Store chosen hook as [SELECTED_HOOK].

STEP 3 — AVATAR + ENVIRONMENT SELECTION (COMBINED)

Ask for BOTH at the same time in ONE message. Do NOT ask separately.

Tell the user:

"Pick your avatar AND your environment. Browse the gallery — avatars and environments are both in here:

👉 https://airtable.com/appYKA4l79ZkD1Z0c/shrjUPhqQ45fSRCMw

Or if you have custom avatars/environments in your own Airtable, check your Klient Engine base.

Give me BOTH:
1. Avatar — type the exact name, or type CUSTOM to build one from scratch
2. Environment — type the exact name, or type CUSTOM and describe it

Or just describe both casually — I'll figure out what you mean."

Wait. The user should give both in one response.

**FUZZY MATCHING — CRITICAL**

The user will NOT always type exact names. They will be casual. You MUST fuzzy match.

How fuzzy matching works:
1. Read ALL avatar skill files in /skills/ai-avatars/*/SKILL.md — get the list of available names
2. Read ALL environment skill files in /skills/ai-environments/*/SKILL.md — get the list of available names
3. Parse the user's message and match intent:
   - "rabbi" → matches "Rabii Goldman" (partial name match)
   - "elena in the coffee shop" → matches avatar "Elena" + environment "Urban Coffee Shop"
   - "marcus on a rooftop" → matches avatar "Marcus" + environment "Rooftop City"
   - "jake in a gym" → matches avatar "Jake" + environment is custom (no gym exists, build it on the fly)
   - "zara in blue G wagon interior" → matches avatar "Zara" + environment is custom (build from description)
   - "rabbi with brabus interior" → matches "Rabii Goldman" + environment is custom
4. If the environment description doesn't match any existing environment, treat it as a CUSTOM environment — build the prompt from their description directly. No need to run the full custom environment intake flow. Just use their description as the environment context.
5. If the avatar reference doesn't match any existing avatar, ask: "I don't recognize that avatar. Want to pick from the gallery or build a custom one?"

The user should be able to say something like "sofia in a minimalist studio" or "rabbi goldman in a blue mercedes interior" and you figure it out. No rigid formatting required.

Store resolved values as [AVATAR_NAME] and [ENVIRONMENT_NAME].

**Process the avatar:**

Try to load /skills/ai-avatars/[AVATAR_NAME]/SKILL.md (use the lowercase-hyphenated folder name) and extract:
  ## Image Generation Prompt section → store as [AVATAR_PROMPT]
  ## Negative Prompt section → store as [AVATAR_NEGATIVE_PROMPT]
  Airtable Image URL field → store as [AVATAR_REFERENCE_URL]

If file not found → check the user's Airtable AI Avatars table for a matching record.
If still not found → ask the user to clarify or offer CUSTOM.

If CUSTOM → load and run /skills/ai-avatars/custom/SKILL.md — it will build, generate, save, and log the new avatar then return here with [AVATAR_NAME] already loaded.

**Process the environment:**

If matched to an existing environment → load /skills/ai-environments/[ENVIRONMENT_NAME]/SKILL.md and extract:
  ## Image Prompt Environment Block section → store as [ENVIRONMENT_PROMPT]

If the user described a custom scene (e.g. "blue G wagon interior", "on a yacht", "in a gym") → treat as inline custom. Build the environment prompt directly from their description. No need to run the full custom environment intake — just use what they said. After the pipeline completes, auto-save it as a new environment skill file at /skills/ai-environments/[environment-name]/SKILL.md and log to Airtable.

If file not found and no description given → ask the user to clarify or offer CUSTOM.

STEP 5 — BUILD IMAGE PROMPT
Assemble the final image prompt by combining:
1. [AVATAR_PROMPT] (full ## Image Generation Prompt from avatar skill)
2. Replace the avatar's own background description with [ENVIRONMENT_PROMPT] (## Image Prompt Environment Block from environment skill)
3. Append: 9:16 vertical composition.
4. Negative prompt: [AVATAR_NEGATIVE_PROMPT]
5. Reference image for character consistency: [AVATAR_REFERENCE_URL]
Store full prompt as [IMAGE_PROMPT].

STEP 6 — GENERATE IMAGE
Call fal.ai using curl with the FAL_KEY from ~/.claude/settings.json:

```
POST https://fal.run/fal-ai/nano-banana-pro
Authorization: Key [FAL_KEY]

{
  "prompt": "[IMAGE_PROMPT]",
  "negative_prompt": "[AVATAR_NEGATIVE_PROMPT]",
  "image_size": "portrait_9_16",
  "num_images": 1
}
```

Do NOT use any other fal.ai model. Must be fal-ai/nano-banana-pro.
Retrieve images[0].url from the result. Store as [GENERATED_IMAGE_URL].

**CRITICAL: ALWAYS embed the image inline using markdown image syntax. NEVER just paste the URL as text. The user must SEE the image without clicking anything.**
```
![AVATAR_NAME](GENERATED_IMAGE_URL)
```
This is non-negotiable. Every time an image is generated — embed it. No links. No "click here."

STEP 7 — LOG TO AIRTABLE (ALL THREE TABLES)
Read AIRTABLE_BASE_ID from the user's ~/.claude/settings.json env settings.
Use curl with the AIRTABLE_API_KEY from ~/.claude/settings.json.
Log to ALL THREE tables every time. Do not skip any.

**7A — Projects table (ALWAYS)**

```
POST https://api.airtable.com/v0/{AIRTABLE_BASE_ID}/Projects
Authorization: Bearer [AIRTABLE_API_KEY]
```

Field | Value
Ad Name | [AVATAR_NAME] — [VIDEO_TOPIC] — [today's date]
Product Name | identify from [VIDEO_TOPIC]
Image Prompt | [IMAGE_PROMPT]
Image Generator | fal.ai
Image Status | Generated
Generated Image 1 | [GENERATED_IMAGE_URL]

Store the new record ID as [AIRTABLE_RECORD_ID] for updating later.

**7B — AI Avatars table (ALWAYS)**

Check if a record with this avatar name already exists. If yes, update it. If no, create a new one.

```
POST https://api.airtable.com/v0/{AIRTABLE_BASE_ID}/AI%20Avatars
Authorization: Bearer [AIRTABLE_API_KEY]
```

Field | Value
Name | [AVATAR_NAME]
Age | [AGE as number, extract from avatar SKILL.md]
Gender | [GENDER, extract from avatar SKILL.md]
Hero Image | [GENERATED_IMAGE_URL]
Description | brief description of appearance, clothing, vibe, setting
Status | Active

**7C — AI Environments table (ALWAYS)**

Check if a record with this environment name already exists. If yes, update it. If no, create a new one.

```
POST https://api.airtable.com/v0/{AIRTABLE_BASE_ID}/AI%20Environments
Authorization: Bearer [AIRTABLE_API_KEY]
```

Field | Value
Name | [ENVIRONMENT_NAME]
Vibe | one-word vibe (e.g. luxury, urban, cozy)
Visual Description | 1-2 sentence visual summary of the environment
Lighting Notes | lighting description from [ENVIRONMENT_PROMPT]
Best For | what type of content this environment suits (e.g. car content, lifestyle, luxury)
Image Prompt Block | [ENVIRONMENT_PROMPT]
Status | Active

STEP 8 — IMAGE APPROVAL GATE
Tell the user:

"Your image has been logged to Airtable. Here's what you can do next:

- **VIDEO** — continue to video generation (Kling, Veo 3.1, or Sora 2)
- **DONE** — stop here, just the image is all I need
- Or tell me what to adjust and I'll regenerate"

If adjustments requested → update [IMAGE_PROMPT] accordingly, regenerate, update the existing Airtable record with the new image, loop back
If DONE → skip to the final confirmation. Say: "✅ Image generated and logged to Airtable. Your avatar is ready to use any time." Then stop.
If VIDEO → proceed to Step 9

Do NOT proceed until the user explicitly chooses.

STEP 9 — VIDEO MODEL SELECTION
Tell the user:

"Kling 3.0 Pro is the best model for talking head videos — sharp, realistic, and handles face movement well. Want to go with Kling 3.0 Pro?

Type YES, NO, or OTHER (to pick a different model)."

Wait for response.

If NO or OTHER → ask:
"Which model do you want?
- Veo 3.1 (talking head with dialogue)
- Sora 2 (cinematic / complex scene)"
Wait. Store selection as [VIDEO_MODEL] and map endpoint accordingly.

If YES → set [VIDEO_MODEL] to Kling 3.0 Pro.

Map to fal.ai endpoint:
Kling 3.0 Pro → fal-ai/kling-video/v3/pro/image-to-video
Veo 3.1 → fal-ai/veo3/image-to-video
Sora 2 → fal-ai/sora/image-to-video
Store endpoint as [VIDEO_ENDPOINT].

"Quick cost breakdown before we generate:

- $0.112/sec — audio OFF
- $0.168/sec — audio ON
- $0.196/sec — audio ON with voice control

How many seconds do you want the video to be?"

Wait. Store as [VIDEO_DURATION].

"Do you want audio ON or OFF? (If audio on — do you want voice control?)"

Wait. Store as [AUDIO_MODE].
Map [AUDIO_MODE] to rate:
- Audio OFF → $0.112
- Audio ON → $0.168
- Audio ON + Voice Control → $0.196

Calculate: [ESTIMATED_COST] = [VIDEO_DURATION] × rate. Round to 2 decimal places.

Tell the user:

"Estimated cost: $[ESTIMATED_COST] for a [VIDEO_DURATION]s video. Ready to generate? Type YES to continue."

Wait for YES before proceeding to Step 10.

STEP 10 — BUILD VIDEO PROMPT
Assemble the video prompt:
[AVATAR_NAME] speaking directly to camera in [ENVIRONMENT_NAME].
Opening line (first words spoken): "[SELECTED_HOOK]"
Topic: [VIDEO_TOPIC]
Tone: confident, direct, conversational
Movement: subtle natural head movement, steady eye contact with camera
Lighting: consistent with environment
Duration: [VIDEO_DURATION] seconds
Start frame: [GENERATED_IMAGE_URL]
Store as [VIDEO_PROMPT].

STEP 11 — GENERATE VIDEO
Call fal.ai using curl with the FAL_KEY from ~/.claude/settings.json:

```
POST https://fal.run/[VIDEO_ENDPOINT]
Authorization: Key [FAL_KEY]

{
  "prompt": "[VIDEO_PROMPT]",
  "image_url": "[GENERATED_IMAGE_URL]",
  "aspect_ratio": "9:16",
  "duration": "[VIDEO_DURATION]",
  "enable_audio": true/false based on [AUDIO_MODE]
}
```

Do NOT use any other fal.ai model. Must match [VIDEO_ENDPOINT].
Retrieve video URL from result. Store as [GENERATED_VIDEO_URL].
Display the video URL to the user.

STEP 12 — UPDATE AIRTABLE RECORD
Update the record [AIRTABLE_RECORD_ID] in the Projects table using curl:

```
PATCH https://api.airtable.com/v0/{AIRTABLE_BASE_ID}/Projects/[AIRTABLE_RECORD_ID]
Authorization: Bearer [AIRTABLE_API_KEY]
```

Field | Value
Video Prompt | [VIDEO_PROMPT]
Video Generator | [VIDEO_MODEL]
Video Status | Pending
Video Generation 1 | [GENERATED_VIDEO_URL]

Confirm to the user:

"✅ Your talking head video is complete and logged to Airtable. Hook, image, and video are all ready."

Key Defaults
Setting | Default
Image model | fal-ai/nano-banana-pro
Video model | Kling 3.0 Pro
Aspect ratio | 9:16 vertical
Resolution | 2K images
Airtable table | Projects

Airtable Fields Reference
Ad Name, Product Name, Image Prompt, Image Generator, Image Status, Generated Image 1, Video Prompt, Video Generator, Video Status, Video Generation 1

File Path Reference
Asset | Path
This skill | /skills/video/talking-head-video/SKILL.md
Hooks library | /viral-hooks-skill/references/hooks-library.md
Avatar skills | /skills/ai-avatars/[name]/SKILL.md
Custom avatar skill | /skills/ai-avatars/custom/SKILL.md
Custom avatar log | /skills/ai-avatars/[name]/SKILL.md
Environment skills | /skills/ai-environments/[name]/SKILL.md
Custom environment skill | /skills/ai-environments/custom/SKILL.md
Custom environment log | /skills/ai-environments/[name]/SKILL.md
