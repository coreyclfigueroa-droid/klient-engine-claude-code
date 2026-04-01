# 🎭 Custom Avatar Creator — Skill
# Path: /skills/ai-avatars/custom/SKILL.md

You are building a custom AI avatar from scratch using cinematic photography principles.
Follow every step in order. The goal is a photorealistic human character that stays consistent across all future video productions.

---

## STEP 1 — CHARACTER INTAKE

Ask the user the following questions one at a time. Wait for each answer before moving to the next.

> "Let's build your custom avatar. I'll ask you a few questions to lock in the character."

1. **Name** — "What is your avatar's name?"
2. **Age & Gender** — "How old do they appear, and what is their gender presentation?"
3. **Ethnicity / Appearance** — "Describe their general look — skin tone, face structure, any defining features."
4. **Hair** — "Describe their hair — length, color, texture, style."
5. **Clothing style** — "What kind of clothes do they typically wear? Think about the fabric weight and fit, not just the style."
6. **Personality vibe** — "What energy do they give off? (e.g. confident, warm, edgy, intellectual, casual)"
7. **Shot type preference** — "Close-up portrait, medium shot, or wide shot?"

Store all answers. Proceed to Step 2.

---

## STEP 2 — BUILD THE IMAGE PROMPT

Using the user's answers, assemble the full image prompt using this exact framework:

### 2A — Shot Type + Camera
```
[SHOT_TYPE] — [FOCAL_LENGTH], [ANGLE]
Shallow depth of field, subject in sharp focus, background softly blurred, f/1.8 aperture
Shot on [DEVICE — e.g. Canon EOS R5, iPhone 15 Pro, Sony A7IV]
```

Shot type guide:
- Close-up portrait → 50mm, slight low angle
- Medium shot → 35mm, eye level
- Wide establishing → 24mm, slight high angle

### 2B — Subject Description
```
[SHOT_TYPE] of a [AGE]-year-old [GENDER] with [SKIN_TONE] skin, [FACE_STRUCTURE], 
[DEFINING_FEATURES]. [HAIR_DESCRIPTION] with individual strand visibility, 
slight flyaways, natural root variation.
Wearing [CLOTHING] — [FABRIC_WEIGHT] with natural drape and visible fabric texture, 
slight wrinkle at [NATURAL_CREASE_POINT].
Natural skin pore texture, slight redness around nose, faint under-eye shadow, no airbrushing.
Relaxed natural hand position if visible, fingers slightly curved.
[PERSONALITY_VIBE] expression, candid unposed feel.
```

### 2C — Lighting
```
[LIGHT_SOURCE] coming from [DIRECTION].
Light hitting [SKIN / FABRIC / SURFACE].
[TIME_OF_DAY] atmosphere.
```

Lighting examples to use based on vibe:
- Warm / approachable → Soft morning window light from camera left, golden warmth on skin
- Professional / clean → Overcast afternoon diffused light, even skin tone
- Premium / cinematic → Warm golden hour backlight, rim light on hair and shoulders

### 2D — Environment Surface Detail
```
Background: [ENVIRONMENT] with [ONE SPECIFIC SURFACE DETAIL].
```

Examples:
- Worn oak desk with a faint coffee ring
- Concrete wall with subtle paint scuff
- Linen couch with natural creases and soft shadow

### 2E — Film Style + Color Grade
```
Slightly desaturated natural color grade, no HDR, no oversaturation.
Subtle film grain, uneven ambient lighting, raw documentary style.
Natural available light only.
```

### 2F — Negative Prompt
Always append this block:
```
Negative prompt: smooth plastic skin, airbrushed, CGI, 3D render, studio lighting, 
perfect symmetry, symmetrical lighting, glossy finish, oversharpened, fake, 
digital art, illustration, cartoon, watermark, studio backdrop, lens flare, 
obviously posed position, perfect lighting
```

---

## STEP 3 — ASSEMBLE + CONFIRM

Output the full assembled prompt to the user clearly labeled as **"Your Avatar Image Prompt."**

Ask:
> "Does this capture your character? Type YES to generate or tell me what to adjust."

If they request changes — update the relevant section and loop back.
If YES — proceed to Step 4.

---

## STEP 4 — IMAGE GENERATION

Call fal.ai via MCP using the queue-based pattern:

**Endpoint:** `POST https://queue.fal.run/fal-ai/nano-banana-pro`

**Parameters:**
```json
{
  "prompt": "[assembled prompt from Step 2]",
  "aspect_ratio": "16:9",
  "resolution": "1K",
  "num_images": 1
}
```

Poll until complete. Retrieve `images[0].url`. Store as `[AVATAR_IMAGE_URL]`.

Show the image URL to the user:
> "Here's your avatar: [AVATAR_IMAGE_URL]
> Type YES to save this character or NO to regenerate."

If NO — ask what to change, adjust prompt, regenerate.
If YES — proceed to Step 5.

---

## STEP 5 — SAVE THE AVATAR

Tell the user:

> "Your avatar is ready. I'll now create the skill file for **[AVATAR_NAME]** so it can be used in all future video productions."

Create a new file at:
`/skills/ai-avatars/[AVATAR_NAME]/SKILL.md`

Write the following into that file:

```markdown
# Avatar: [AVATAR_NAME]
# Path: /skills/ai-avatars/[AVATAR_NAME]/SKILL.md

## Character Reference
- **Name:** [AVATAR_NAME]
- **Airtable Image URL:** [AVATAR_IMAGE_URL]

## Image Generation Prompt
[FULL ASSEMBLED PROMPT FROM STEP 2]

## Consistency Notes
- Always use the Airtable image URL above as a reference frame for character consistency
- Shot type: [SHOT_TYPE]
- Camera: [DEVICE]
- Lighting: [LIGHT_SOURCE + DIRECTION]
- Always append the negative prompt block from this file

## Negative Prompt
Smooth plastic skin, airbrushed, CGI, 3D render, studio lighting, 
perfect symmetry, symmetrical lighting, glossy finish, oversharpened, fake, 
digital art, illustration, cartoon, watermark, studio backdrop, lens flare, 
obviously posed position, perfect lighting
```

---

## STEP 6 — LOG TO AIRTABLE

Read `AIRTABLE_BASE_ID` and `AIRTABLE_API_KEY` from the user's `~/.claude/settings.json` env settings.

Log the new avatar to the **AI Avatars** table:

```
POST https://api.airtable.com/v0/{AIRTABLE_BASE_ID}/AI%20Avatars
```

Fields to write:
- `Name` → `[AVATAR_NAME]`
- `Age` → `[AGE as number]`
- `Gender` → `[GENDER]`
- `Hero Image` → `[AVATAR_IMAGE_URL]`
- `Description` → brief description of appearance, clothing, vibe, and setting
- `Status` → `"Active"`

Confirm to the user:
> "✅ **[AVATAR_NAME]** saved to skill file and logged to Airtable AI Avatars. Ready for any future video."

Then return to the calling skill with `[AVATAR_NAME]` already loaded.

---

## PROMPT FRAMEWORK REFERENCE

| Layer | What to Include |
|---|---|
| Shot type | Close-up / Medium / Wide + focal length + angle |
| Depth of field | Shallow DOF, f/1.8, subject sharp, background blurred |
| Lighting | Source type + direction + what it hits |
| Skin | Pore texture, natural redness, under-eye shadow, no airbrushing |
| Clothing | Fabric weight, drape, natural wrinkle points |
| Hair | Strand visibility, flyaways, root variation |
| Hands | Relaxed, fingers slightly curved |
| Environment | One specific surface detail over five adjectives |
| Film style | Device name, desaturated grade, film grain, no HDR |
| Negative prompt | Always appended, non-negotiable |