# 🎭 Custom Avatar Creator
# Path: /skills/ai-avatars/custom/SKILL.md

You are building a custom AI avatar from scratch. The goal is a character that looks like a real person filmed casually on an iPhone — not a polished AI image. Think UGC creator, not studio shoot.

---

## CRITICAL RULES — NEVER BREAK THESE

- **ALWAYS vertical 9:16** — every image is portrait orientation. Shot on iPhone. Never horizontal.
- **NEVER close-up** — always medium shot showing full torso and both hands. Pulled back, not crammed into the face.
- **Prompt only — no negative prompt, no extra params** — just prompt. No negative_prompt, no resolution, no num_images.
- **Use MCP tools for fal.ai** — never curl with raw API keys. Keys must never be visible.

---

## STEP 1 — CHARACTER INTAKE

Ask the user one at a time. Wait for each answer before moving on.

> "Let's build your custom avatar. I'll ask you a few questions to lock in the character."

1. **Name** — "What is your avatar's name?"
2. **Age & Gender** — "How old do they appear, and what is their gender presentation?"
3. **Ethnicity / Appearance** — "Describe their general look — skin tone, face structure, any defining features."
4. **Hair** — "Describe their hair — length, color, texture, style."
5. **Clothing** — "What do they wear? Think fabric weight and fit, not just style. This should never change."
6. **Persona** — "What is their identity or role? (e.g. TCM practitioner, fitness coach, tech founder)"
7. **Expression / Energy** — "What do they communicate on camera? (e.g. urgency, calm authority, warmth)"
8. **Environments** — "Describe 3-4 environments they appear in. Be specific — surfaces, objects, lighting, colors. Each one should reinforce the persona."

Store all answers. Proceed to Step 2.

---

## STEP 2 — BUILD THE CHARACTER PROFILE

Using the user's answers, write a single character profile in this exact format:

```
UGC Text-to-Image Prompt Structure
Subject Description (Who)

Age, gender, ethnicity, physical features (hair, facial hair, skin, distinguishing traits)

Wardrobe & Accessories (What they're wearing)

Clothing style, fabric, color, closures/details
Jewelry, glasses, headwear

Props & Actions (What they're doing)

Left hand: what it's holding or doing
Right hand: what it's holding or doing
Body language / gesture intent (teaching, explaining, reacting, showing)

Camera Angle & Framing (How it's shot)

Angle (low, eye-level, high, dutch)
Distance (close-up, medium, wide)
Implied setup (phone propped on table, held by someone, tripod, selfie)

Background & Environment (The room/setting)

Wall details: posters, art, signage, shelving
Surfaces: what's on desks, counters, shelves
Objects that sell the setting's identity (jars, equipment, books, products)

Lighting

Source (natural window, ring light, overhead, lamp)
Quality (soft, harsh, warm, cool, diffused)
Direction (left, right, behind, above)

Mood & Context (The vibe)

What they appear to be doing in the "video"
Platform energy (TikTok explainer, IG story, YouTube intro)

Style Tag (Output format)

"iPhone UGC style photo" + aspect ratio


Output this to the user labeled as **"Your Avatar Profile."**

Ask:
> "Does this capture your character? Type YES to save or tell me what to adjust."

If they request changes — update and loop back.
If YES — proceed to Step 3.

---

## STEP 3 — BUILD THE IMAGE PROMPT

Convert the character profile into an image generation prompt using this structure:

```
UGC Text-to-Image Prompt Structure
Subject Description (Who)

Age, gender, ethnicity, physical features (hair, facial hair, skin, distinguishing traits)

Wardrobe & Accessories (What they're wearing)

Clothing style, fabric, color, closures/details
Jewelry, glasses, headwear

Props & Actions (What they're doing)

Left hand: what it's holding or doing
Right hand: what it's holding or doing
Body language / gesture intent (teaching, explaining, reacting, showing)

Camera Angle & Framing (How it's shot)

Angle (low, eye-level, high, dutch)
Distance (close-up, medium, wide)
Implied setup (phone propped on table, held by someone, tripod, selfie)

Background & Environment (The room/setting)

Wall details: posters, art, signage, shelving
Surfaces: what's on desks, counters, shelves
Objects that sell the setting's identity (jars, equipment, books, products)

Lighting

Source (natural window, ring light, overhead, lamp)
Quality (soft, harsh, warm, cool, diffused)
Direction (left, right, behind, above)

Mood & Context (The vibe)

What they appear to be doing in the "video"
Platform energy (TikTok explainer, IG story, YouTube intro)

Style Tag (Output format)

"iPhone UGC style photo" + aspect ratio

```

---

## STEP 4 — IMAGE GENERATION

Call fal.ai via MCP (`mcp__fal__generate`). Never use curl with raw API keys.

```json
{
  "app_id": "fal-ai/nano-banana-pro",
  "input_data": {
    "prompt": "[assembled prompt from Step 3]",
    "aspect_ratio": "9:16"
  }
}
```

Poll with `mcp__fal__status` until COMPLETED, then fetch with `mcp__fal__result`. Retrieve `images[0].url`.

Provide the direct URL to the user (VSCode extension may not render inline images):
> "Here's your avatar: [URL]"
> "Type YES to save or NO to regenerate."

If NO — ask what to change, adjust, regenerate.
If YES — proceed to Step 5.

---

## STEP 5 — SAVE THE AVATAR

Create a new file at: `/skills/ai-avatars/[avatar-name-lowercase-hyphenated]/SKILL.md`

Write the full character profile from Step 2 and the image generation prompt from Step 3 into the file, along with:

- **Airtable Image URL** — the generated image URL
- **Consistency Notes** — always use the Airtable image URL as reference, clothing never changes, always 9:16 vertical, never close-up, prompt only (no negatives)

---

## STEP 6 — LOG TO AIRTABLE

Use `mcp__airtable__create_record`. Never use curl with raw API keys.

Log to the **AI Avatars** table (`baseId`: read from `~/.claude/settings.json` → `AIRTABLE_BASE_ID`):

Fields:
- `Name` → avatar name
- `Age` → age as string
- `Gender` → gender
- `Image URL` → generated image URL (plain text)
- `Image` → `[{"url": "[IMAGE_URL]"}]` (attachment — so the image shows in Airtable)
- `Character Description` → the character profile from Step 2
- `Full Prompt` → the full image generation prompt from Step 3
- `Shot Type` → "Vertical 9:16, medium shot from slightly below"
- `Status` → "Active"

Confirm:
> "✅ **[AVATAR_NAME]** saved to skill file and logged to Airtable. Ready for any future video."
