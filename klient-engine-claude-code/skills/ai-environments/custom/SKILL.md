# 🌍 Custom Environment Builder — Skill
# Path: /skills/ai-environments/custom/SKILL.md

You are building a custom environment/background from scratch for AI video production.
Follow every step in order. The goal is a richly described scene that places the avatar in a consistent, visually compelling setting.

---

## STEP 1 — ENVIRONMENT INTAKE

Ask the user the following questions one at a time. Wait for each answer before moving to the next.

> "Let's build your custom environment. A few quick questions."

1. **Location** — "Where is your avatar? (e.g. home office, rooftop, coffee shop, studio, outdoors)"
2. **Vibe** — "What feeling should it give off? (e.g. professional, cozy, energetic, cinematic, casual)"
3. **One specific detail** — "Give me one specific object or surface detail you want in the scene. The more specific the better. (e.g. 'a half-empty coffee cup on a worn oak desk')"
4. **Lighting** — "What kind of light? (e.g. morning window light, golden hour, overcast, warm lamp glow, neon)"
5. **Best use** — "What type of content will this be used for? (e.g. tips, stories, pitches, announcements)"

Store all answers. Proceed to Step 2.

---

## STEP 2 — BUILD THE ENVIRONMENT DESCRIPTION

Using the user's answers, assemble the full environment description:

### Visual Description
A [VIBE] [LOCATION]. [SPECIFIC_DETAIL]. [LIGHTING_DESCRIPTION]. [ONE_ADDITIONAL_ATMOSPHERIC_DETAIL]. Real and lived-in, not staged.

### Image Prompt Block (for use in fal.ai generation)
```
Background: blurred [LOCATION] — [SPECIFIC_DETAIL], [LIGHTING_DESCRIPTION], [ATMOSPHERIC_DETAIL]. [VIBE_ADJECTIVE], not staged.
```

### Lighting Notes
- Source: [LIGHT_SOURCE] from [DIRECTION]
- Quality: [SOFT/HARD/DIFFUSED/MIXED]
- Mood: [MOOD_WORD]
- Time of day: [TIME]

---

## STEP 3 — CONFIRM

Output the full environment description to the user clearly labeled as **"Your Environment Description."**

Ask:
> "Does this capture the vibe? Type YES to save or tell me what to adjust."

If they request changes — update and loop back.
If YES — proceed to Step 4.

---

## STEP 4 — SAVE THE ENVIRONMENT

Create a new file at:
`/skills/ai-environments/[ENVIRONMENT_NAME]/SKILL.md`

Write the following into that file:

```markdown
# Environment: [ENVIRONMENT_NAME]
# Path: /skills/ai-environments/[ENVIRONMENT_NAME]/SKILL.md

## Environment Reference
- **Name:** [ENVIRONMENT_NAME]
- **Image URL:** [generate on first use]
- **Vibe:** [VIBE]

## Visual Description
[FULL VISUAL DESCRIPTION FROM STEP 2]

## Lighting Notes
[LIGHTING NOTES FROM STEP 2]

## Image Prompt Environment Block
[IMAGE PROMPT BLOCK FROM STEP 2]

## Usage Notes
- Best for: [BEST_USE]
- Pair with close-up or medium shots
- Lighting in this environment: [LIGHTING_SUMMARY]
```

---

## STEP 5 — LOG TO AIRTABLE

Read `AIRTABLE_BASE_ID` and `AIRTABLE_API_KEY` from the user's `~/.claude/settings.json` env settings.

First, check if an "AI Environments" table exists in the user's base. Use the Airtable API to list tables:
```
GET https://api.airtable.com/v0/meta/bases/{AIRTABLE_BASE_ID}/tables
```

**If the AI Environments table does NOT exist** — create it first:
```
POST https://api.airtable.com/v0/meta/bases/{AIRTABLE_BASE_ID}/tables
{
  "name": "AI Environments",
  "fields": [
    {"name": "Name", "type": "singleLineText"},
    {"name": "Vibe", "type": "singleLineText"},
    {"name": "Visual Description", "type": "multilineText"},
    {"name": "Lighting Notes", "type": "multilineText"},
    {"name": "Best For", "type": "singleLineText"},
    {"name": "Image Prompt Block", "type": "multilineText"},
    {"name": "Status", "type": "singleSelect", "options": {"choices": [{"name": "Active"}, {"name": "Draft"}, {"name": "Archived"}]}}
  ]
}
```

**If the AI Environments table exists** — search for an existing record with this Name. If found, update it. If not, create a new record.

Create/update record fields:
- `Name` → `[ENVIRONMENT_NAME]`
- `Vibe` → vibe description
- `Visual Description` → full visual description
- `Lighting Notes` → lighting summary
- `Best For` → best content type
- `Image Prompt Block` → the fal.ai prompt block
- `Status` → "Active"

Confirm to the user:
> "✅ **[ENVIRONMENT_NAME]** saved as a skill file and logged to your Airtable. Ready for any future video."

Then return to the Talking Head Video skill at Step 4 — Environment Selection — and continue with `[ENVIRONMENT_NAME]` already loaded.
