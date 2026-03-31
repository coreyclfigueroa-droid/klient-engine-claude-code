🎬 Talking Head Video — Orchestration Skill
Path: /skills/video/talking-head-video/SKILL.md
You are orchestrating a full talking head video production pipeline.
Run this pipeline automatically when the user invokes this skill. Do not skip steps. Do not combine steps. Stop and wait at every approval gate.

Default Workflow
When this skill is loaded, run the full pipeline below automatically without the user having to spell out each step.

The Pipeline
STEP 1 — VIDEO BRIEF
Ask the user:

"What is your video about? Describe the topic, product, or message in a few sentences."

Wait for their response. Store as [VIDEO_TOPIC].

STEP 2 — HOOK GENERATION
Read the hooks library at /skills/content/hooks-library.md.
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
Tell the user:

"Now let's pick your avatar. Click the link below to browse your character gallery in Airtable, then come back and type the exact name of the one you want."

Output: https://airtable.com/[YOUR_CHARACTERS_VIEW_URL]

⚠️ Replace with real Airtable Characters view URL once created.

Ask: "Which avatar do you want to use?"
Wait. Store as [AVATAR_NAME].
Load /skills/ai-avatars/[AVATAR_NAME]/SKILL.md. Extract:

Character image prompt → [AVATAR_PROMPT]
Airtable reference image URL → [AVATAR_REFERENCE_URL]

If file not found:

"I couldn't find a skill file for [AVATAR_NAME]. Two options:

Typo? Paste the name exactly as it appears in Airtable and I'll try again.
Custom avatar? Type CUSTOM and I'll build one from scratch."



New name pasted → retry with corrected name
CUSTOM typed → load and run /skills/ai-avatars/custom/SKILL.md — it will build, generate, save, and log the new avatar then return here with [AVATAR_NAME] already loaded


STEP 4 — ENVIRONMENT SELECTION
Tell the user:

"Now let's pick your environment. Click the link below to browse your environment gallery, then come back and type the exact name of the one you want."

Output: https://airtable.com/[YOUR_ENVIRONMENTS_VIEW_URL]

⚠️ Replace with real Airtable Environments view URL once created.

Ask: "Which environment do you want to use?"
Wait. Store as [ENVIRONMENT_NAME].
Load /skills/ai-environments/[ENVIRONMENT_NAME]/SKILL.md. Extract:

Environment image prompt → [ENVIRONMENT_PROMPT]
Airtable reference image URL → [ENVIRONMENT_REFERENCE_URL]

If file not found:

"I couldn't find a skill file for [ENVIRONMENT_NAME]. Two options:

Typo? Paste the name exactly as it appears in Airtable and I'll try again.
Custom environment? Type CUSTOM and I'll build one from scratch."



New name pasted → retry with corrected name
CUSTOM typed → load and run /skills/ai-environments/custom/SKILL.md


STEP 5 — BUILD IMAGE PROMPT
Assemble the final image prompt from all stored variables:
[AVATAR_PROMPT], placed in [ENVIRONMENT_PROMPT].
[VIDEO_TOPIC] context. Cinematic lighting, ultra-realistic, 9:16 vertical composition.
Reference image for character consistency: [AVATAR_REFERENCE_URL]
Negative prompt: smooth plastic skin, airbrushed, CGI, 3D render, studio lighting,
perfect symmetry, glossy finish, oversharpened, digital art, cartoon, watermark
Store as [IMAGE_PROMPT].

STEP 6 — GENERATE IMAGE
Call fal.ai via MCP — queue-based pattern:
Endpoint: POST https://queue.fal.run/fal-ai/nano-banana-2
Parameters:
json{
  "prompt": "[IMAGE_PROMPT]",
  "aspect_ratio": "9:16",
  "resolution": "2K",
  "num_images": 1
}
Poll until complete. Retrieve images[0].url. Store as [GENERATED_IMAGE_URL].
Display the image URL to the user.

STEP 7 — STOP AND WAIT FOR IMAGE APPROVAL
Ask:

"Here's your generated image: [GENERATED_IMAGE_URL]
Does this look good? Type YES to continue or tell me what to adjust."


If adjustments requested → update [IMAGE_PROMPT] accordingly, regenerate, loop back to Step 6
If YES → proceed to Step 8

Do NOT generate video until the user explicitly approves the image.

STEP 8 — LOG IMAGE TO AIRTABLE
Post to the Airtable Projects table via MCP:
FieldValueAd Name[AVATAR_NAME] — [VIDEO_TOPIC] — [today's date]Product Nameidentify from [VIDEO_TOPIC]Image Prompt[IMAGE_PROMPT]Image GeneratorNano Banana 2Image StatusPendingGenerated Image 1[GENERATED_IMAGE_URL]

STEP 9 — VIDEO MODEL SELECTION
Ask:

"What style of video do you want?

Realistic / Live Action → Kling 3.0 Pro
Talking head with dialogue → Veo 3.1
Cinematic / complex scene → Sora 2"


Wait. Store as [VIDEO_MODEL].
Map to fal.ai endpoint:

Kling 3.0 Pro → fal-ai/kling-video/v3/pro/image-to-video
Veo 3.1 → fal-ai/veo3/image-to-video
Sora 2 → fal-ai/sora/image-to-video

Store endpoint as [VIDEO_ENDPOINT].

STEP 10 — BUILD VIDEO PROMPT
Assemble the video prompt:
[AVATAR_NAME] speaking directly to camera in [ENVIRONMENT_NAME].
Opening line (first words spoken): "[SELECTED_HOOK]"
Topic: [VIDEO_TOPIC]
Tone: confident, direct, conversational
Movement: subtle natural head movement, steady eye contact with camera
Lighting: consistent with environment
Duration: 15–30 seconds
Start frame: [GENERATED_IMAGE_URL]
Store as [VIDEO_PROMPT].

STEP 11 — GENERATE VIDEO
Call fal.ai via MCP — queue-based pattern:
Endpoint: POST https://queue.fal.run/[VIDEO_ENDPOINT]
Parameters:
json{
  "prompt": "[VIDEO_PROMPT]",
  "image_url": "[GENERATED_IMAGE_URL]",
  "aspect_ratio": "9:16",
  "duration": "15"
}
Poll until complete. Retrieve video URL. Store as [GENERATED_VIDEO_URL].
Display the video URL to the user.

STEP 12 — UPDATE AIRTABLE RECORD
Update the existing Projects record via MCP:
FieldValueVideo Prompt[VIDEO_PROMPT]Video Generator[VIDEO_MODEL]Video StatusPendingVideo Generation 1[GENERATED_VIDEO_URL]
Confirm to the user:

"✅ Your talking head video package is complete and logged to Airtable. Hook, image, and video are all ready."


Key Defaults
SettingDefaultImage modelfal-ai/nano-banana-2Video modelKling 3.0 Pro (talking head with dialogue → Veo 3.1)Aspect ratio9:16 verticalResolution2K imagesAirtable tableProjects

Airtable Fields Reference
Ad Name, Product Name, Image Prompt, Image Generator, Image Status, Generated Image 1, Video Prompt, Video Generator, Video Status, Video Generation 1

File Path Reference
AssetPathThis skill/skills/video/talking-head-video/SKILL.mdHooks library/skills/content/hooks-library.mdAvatar skills/skills/ai-avatars/[name]/SKILL.mdCustom avatar skill/skills/ai-avatars/custom/SKILL.mdEnvironment skills/skills/ai-environments/[name]/SKILL.mdCustom environment skill/skills/ai-environments/custom/SKILL.md