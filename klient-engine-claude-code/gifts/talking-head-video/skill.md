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

STEP 3 — AVATAR SELECTION
Ask:

"Which avatar library do you want to use?
1. Klient Engine Library (pre-built avatars)
2. My Library (your custom avatars)"

Wait. Store choice as [AVATAR_LIBRARY].

If [AVATAR_LIBRARY] = 1 → tell the user:
"Browse the Klient Engine avatar gallery here: https://airtable.com/appYKA4l79ZkD1Z0c/shrjUPhqQ45fSRCMw
Come back and type the exact name of the avatar you want."

If [AVATAR_LIBRARY] = 2 → tell the user:
"Open your Airtable Klient Engine base → AI Avatars table. Browse your custom avatars, then come back and type the exact name."

Ask: "Which avatar do you want to use?"
Wait. Store as [AVATAR_NAME].

If [AVATAR_LIBRARY] = 1:
Load /skills/ai-avatars/[AVATAR_NAME]/SKILL.md and extract:
  ## Image Generation Prompt section → store as [AVATAR_PROMPT]
  ## Negative Prompt section → store as [AVATAR_NEGATIVE_PROMPT]
  Airtable Image URL field → store as [AVATAR_REFERENCE_URL]

  If file not found → tell the user:
  "I couldn't find a skill file for [AVATAR_NAME]. Two options:
  Typo? Paste the name exactly as it appears in Airtable and I'll try again.
  Custom avatar? Type CUSTOM and I'll build one from scratch."
  New name pasted → retry
  CUSTOM typed → load and run /skills/ai-avatars/custom/SKILL.md — it will build, generate, save, and log the new avatar then return here with [AVATAR_NAME] already loaded
  Save the new avatar SKILL.md to: /skills/ai-avatars/[AVATAR_NAME]/SKILL.md (relative to workspace root)

If [AVATAR_LIBRARY] = 2:
Query the AI Avatars table in AIRTABLE_BASE_ID where Name = [AVATAR_NAME] and extract:
  Full Prompt field → store as [AVATAR_PROMPT]
  Image URL field → store as [AVATAR_REFERENCE_URL]
  Negative Prompt: use the standard negative prompt block

  If record not found → tell the user:
  "I couldn't find [AVATAR_NAME] in your Airtable. Check the spelling matches exactly or type CUSTOM to build a new one from scratch."
  New name pasted → retry
  CUSTOM typed → load and run /skills/ai-avatars/custom/SKILL.md

STEP 4 — ENVIRONMENT SELECTION
Ask:

"Which environment library do you want to use?
1. Klient Engine Library (pre-built environments)
2. My Library (your custom environments)"

Wait. Store choice as [ENVIRONMENT_LIBRARY].

If [ENVIRONMENT_LIBRARY] = 1 → tell the user:
"Browse the Klient Engine environment gallery here: https://airtable.com/appYKA4l79ZkD1Z0c/shrjUPhqQ45fSRCMw
Come back and type the exact name of the environment you want."

If [ENVIRONMENT_LIBRARY] = 2 → tell the user:
"Open your Airtable Klient Engine base → AI Environments table. Browse your custom environments, then come back and type the exact name."

Ask: "Which environment do you want to use?"
Wait. Store as [ENVIRONMENT_NAME].

If [ENVIRONMENT_LIBRARY] = 1:
Load /skills/ai-environments/[ENVIRONMENT_NAME]/SKILL.md and extract:
  ## Image Prompt Environment Block section → store as [ENVIRONMENT_PROMPT]

  If file not found → tell the user:
  "I couldn't find a skill file for [ENVIRONMENT_NAME]. Two options:
  Typo? Paste the name exactly as it appears in Airtable and I'll try again.
  Custom environment? Type CUSTOM and I'll build one from scratch."
  New name pasted → retry
  CUSTOM typed → load and run /skills/ai-environments/custom/SKILL.md
  Save the new environment SKILL.md to: /skills/ai-environments/[ENVIRONMENT_NAME]/SKILL.md (relative to workspace root)

If [ENVIRONMENT_LIBRARY] = 2:
Query the AI Environments table in AIRTABLE_BASE_ID where Name = [ENVIRONMENT_NAME] and extract:
  Image Prompt Block field → store as [ENVIRONMENT_PROMPT]

  If record not found → tell the user:
  "I couldn't find [ENVIRONMENT_NAME] in your Airtable. Check the spelling matches exactly or type CUSTOM to build a new one from scratch."
  New name pasted → retry
  CUSTOM typed → load and run /skills/ai-environments/custom/SKILL.md

STEP 5 — BUILD IMAGE PROMPT
Assemble the final image prompt by combining:
1. [AVATAR_PROMPT] (full ## Image Generation Prompt from avatar skill)
2. Replace the avatar's own background description with [ENVIRONMENT_PROMPT] (## Image Prompt Environment Block from environment skill)
3. Append: 9:16 vertical composition.
4. Negative prompt: [AVATAR_NEGATIVE_PROMPT]
5. Reference image for character consistency: [AVATAR_REFERENCE_URL]
Store full prompt as [IMAGE_PROMPT].

STEP 6 — GENERATE IMAGE
Use the MCP tool mcp__fal-image-video__execute_custom_model with:
- model_id: fal-ai/nano-banana-2
- input:
  - prompt: [IMAGE_PROMPT]
  - aspect_ratio: "9:16"
  - resolution: "2K"
  - num_images: 1

Do NOT use any other fal.ai model or tool. Must be fal-ai/nano-banana-2.
Retrieve images[0].url from the result. Store as [GENERATED_IMAGE_URL].
Display the image URL to the user.

STEP 7 — LOG IMAGE TO AIRTABLE
Read AIRTABLE_BASE_ID from the user's ~/.claude/settings.json env settings.
Create a new record in the Projects table in that base via MCP.

Only write fields that exist in the table. Use mcp__airtable__describe_table to check available fields first, then write whichever of these exist:
Field | Value
Ad Name | [AVATAR_NAME] — [VIDEO_TOPIC] — [today's date]
Product Name | identify from [VIDEO_TOPIC]
Avatar Name | [AVATAR_NAME]
Avatar Image URL | [AVATAR_REFERENCE_URL]
Environment | [ENVIRONMENT_NAME]
Top Hook | [SELECTED_HOOK]
All Hooks | all 5 hooks generated in Step 2
Image Prompt | [IMAGE_PROMPT]
Image Generator | Nano Banana 2
Image Status | Pending
Generated Image 1 | [GENERATED_IMAGE_URL]

Store the new record ID as [AIRTABLE_RECORD_ID] for updating later.

STEP 8 — IMAGE APPROVAL GATE
Tell the user:

"Your image has been logged to your Airtable Projects table. Open your Klient Engine base, find the record, and review the image — then come back and let me know if you want to continue to video generation.

Type YES to generate the video, or tell me what to adjust."

If adjustments requested → update [IMAGE_PROMPT] accordingly, regenerate, update the existing Airtable record with the new image, loop back
If YES → proceed to Step 9

Do NOT proceed until the user explicitly approves.

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
Use the MCP tool mcp__fal-image-video__execute_custom_model with:
- model_id: [VIDEO_ENDPOINT]
- input:
  - prompt: [VIDEO_PROMPT]
  - image_url: [GENERATED_IMAGE_URL]
  - aspect_ratio: "9:16"
  - duration: "[VIDEO_DURATION]"
  - enable_audio: true if [AUDIO_MODE] is audio ON or audio ON + voice control, false if audio OFF

Do NOT use any other fal.ai model or tool. Must match [VIDEO_ENDPOINT].
Poll until complete. Retrieve video URL. Store as [GENERATED_VIDEO_URL].
Display the video URL to the user.

STEP 12 — UPDATE AIRTABLE RECORD
Update the record [AIRTABLE_RECORD_ID] in the Projects table in the user's AIRTABLE_BASE_ID via MCP:
Field | Value
Video Prompt | [VIDEO_PROMPT]
Video Generator | [VIDEO_MODEL]
Video Status | Pending
Video Generation 1 | [GENERATED_VIDEO_URL]

Confirm to the user:

"✅ Your talking head video is complete and logged to Airtable. Hook, image, and video are all ready."

Key Defaults
Setting | Default
Image model | fal-ai/nano-banana-2
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
