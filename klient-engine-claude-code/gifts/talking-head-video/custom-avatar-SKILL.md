# 🎭 Custom Avatar Creator — Skill
# Path: /skills/ai-avatars/custom/SKILL.md

You are building a custom AI avatar from scratch.
Follow every step in order. The goal is a character that looks like a REAL person in a REAL photo — like a screenshot from someone's TikTok or Instagram story.

---

## STEP 1 — CHARACTER INTAKE

If the user provides a reference image, reverse-engineer it — extract all character details directly from the image. Skip the questions and go straight to Step 2.

If no reference image is provided, ask the user the following questions one at a time. Wait for each answer before moving to the next.

> "Let's build your custom avatar. A few quick questions."

1. **Name** — "What is your avatar's name?"
2. **Age & Gender** — "How old do they appear, and what is their gender presentation?"
3. **Appearance** — "Describe their general look — skin tone, face, any defining features (glasses, freckles, beard, etc.)"
4. **Hair** — "Describe their hair — length, color, style."
5. **What are they wearing?** — "Describe their outfit. Be specific — brand names, colors, accessories, jewelry, anything visible."
6. **Where are they?** — "Where is this person when the photo is taken? What's behind them? What objects are around them?"
7. **What are they doing?** — "What's the vibe? Talking to camera? Mid-conversation? Looking at something? Holding something?"

Store all answers. Proceed to Step 2.

---

## STEP 2 — BUILD THE IMAGE PROMPT

**THE #1 RULE: Write the prompt like you're describing a real photo to a friend. NOT like a photography brief. NOT like an AI prompt. Just describe what you SEE in the photo.**

The prompt style that works is hyper-specific and mundane. Study this example of a prompt that produces realistic results:

> "A young woman with light brown hair and freckles is in a bedroom, holding a Starbucks iced coffee in her right hand, with a green straw in it. She is wearing a green long-sleeved shirt and white sweatpants. Her left hand is reaching out behind the camera. In the background, there is a bed with rumpled beige bedding, a wooden headboard with photos attached, and a bookshelf filled with books and decorative items. String lights are draped along the top of the bookshelf. The lighting is natural and bright. iPhone UGC style photo."

### What makes this work:
1. **Specific objects** — not "a coffee" but "a Starbucks iced coffee with a green straw"
2. **Specific clothing** — not "casual clothes" but "green long-sleeved shirt and white sweatpants"
3. **Specific background clutter** — rumpled bedding, photos on headboard, bookshelf with stuff, string lights
4. **Body position** — "holding X in right hand", "left hand reaching out"
5. **One line about photo style** — "iPhone UGC style photo" and that's IT
6. **Imperfect details** — "rumpled", "random layout", stuff that makes it look lived-in
7. **ZERO photography jargon** — no f-stops, no focal lengths, no "shallow depth of field", no camera names, no lighting direction

### How to build the prompt:

Write ONE paragraph that covers:
1. **Who** — age, gender, key features (hair, glasses, beard, freckles, etc.)
2. **Where** — the specific location with 2-3 specific background objects/details that are slightly messy or imperfect
3. **Wearing** — specific clothing with colors, plus any accessories (jewelry, watch, hat, etc.)
4. **Doing** — what they're doing with their hands, their expression, their body angle
5. **Photo style** — end with "iPhone UGC style photo" or "iPhone selfie style" or "as if someone just snapped this on their phone"

### What to NEVER include:
- Camera names (Canon, Sony, etc.)
- F-stops, aperture, focal length
- "Shallow depth of field" or "bokeh"
- "Cinematic" or "dramatic lighting"
- "Skin pore texture" or "strand visibility"
- "Natural color grade" or "film grain"
- Anything that sounds like a photographer wrote it

### Negative Prompt
Always use:
```
professional photography, studio lighting, DSLR, 4K, 8K, ultra detailed, hyper realistic, perfect skin, airbrushed, CGI, 3D render, digital art, illustration, cartoon, watermark, perfect lighting, magazine photo, stock photo, headshot, posed, model
```

---

## STEP 3 — ASSEMBLE + CONFIRM

Output the full assembled prompt to the user clearly labeled as **"Your Avatar Image Prompt."**

Ask:
> "Does this capture your character? Type YES to generate or tell me what to adjust."

If they request changes — update and loop back.
If YES — proceed to Step 4.

---

## STEP 4 — IMAGE GENERATION

Call fal.ai using the synchronous endpoint:

**Endpoint:** `fal-ai/nano-banana-pro` via `https://fal.run/fal-ai/nano-banana-pro`

**Parameters:**
```json
{
  "prompt": "[assembled prompt from Step 2]",
  "negative_prompt": "[negative prompt from Step 2]",
  "aspect_ratio": "9:16",
  "num_images": 1
}
```

Retrieve `images[0].url`. Store as `[AVATAR_IMAGE_URL]`.

**CRITICAL: ALWAYS embed the image inline using markdown image syntax. NEVER just paste the URL as text. The user must SEE the image without clicking anything.**

```
![AVATAR_NAME](IMAGE_URL)
```

This is non-negotiable. Every time an image is generated anywhere in this skill or any skill that generates images — embed it. No links. No "click here." The image shows up RIGHT THERE in the conversation.

Then immediately save — do NOT ask for confirmation. Always auto-save the avatar and proceed to Step 5.

---

## STEP 5 — SAVE THE AVATAR

Auto-save. Do not ask permission — just save and confirm.

Create a new file at:
`/skills/ai-avatars/[AVATAR_NAME]/SKILL.md`

(Use lowercase-hyphenated version of the name for the folder, e.g. "rabii-goldman")

Add frontmatter so Claude Code recognizes it as a skill:

```markdown
---
name: [lowercase-hyphenated-name]
description: AI avatar for [AVATAR_NAME] — [brief one-line description of their appearance]
---

# Avatar: [AVATAR_NAME]
# Path: /skills/ai-avatars/[AVATAR_NAME]/SKILL.md

## Character Reference
- **Name:** [AVATAR_NAME]
- **Airtable Image URL:** [AVATAR_IMAGE_URL]

## Image Generation Prompt
[FULL PROMPT FROM STEP 2]

## Consistency Notes
- Always use the Airtable image URL above as a reference for character consistency
- Style: iPhone UGC — must look like a real phone photo, not professional
- Always append the negative prompt block from this file

## Negative Prompt
professional photography, studio lighting, DSLR, 4K, 8K, ultra detailed,
hyper realistic, perfect skin, airbrushed, CGI, 3D render, digital art,
illustration, cartoon, watermark, perfect lighting, magazine photo,
stock photo, headshot, posed, model
```

---

## STEP 6 — LOG TO AIRTABLE

Read `AIRTABLE_BASE_ID` and `AIRTABLE_API_KEY` from the user's `~/.claude/settings.json` env settings.

The **AI Avatars** table already exists. Do NOT try to create it. Just log the record:

```
POST https://api.airtable.com/v0/{AIRTABLE_BASE_ID}/AI%20Avatars
```

Fields to write (these are the EXACT field names — do not deviate):
- `Name` → `[AVATAR_NAME]`
- `Age` → `[AGE as number]`
- `Gender` → `[GENDER]`
- `Hero Image` → `[AVATAR_IMAGE_URL]`
- `Description` → brief description of appearance, clothing, vibe, and setting
- `Status` → `"Active"`

Confirm to the user:
> "✅ **[AVATAR_NAME]** saved to skill file and logged to Airtable AI Avatars. Ready for any future video."

Then return to the calling skill with `[AVATAR_NAME]` already loaded.
