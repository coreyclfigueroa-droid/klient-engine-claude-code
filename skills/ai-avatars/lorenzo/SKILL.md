---
name: lorenzo
description: AI avatar for Lorenzo — older wealthy man, silver swept-back hair, navy blazer, Ferrari convertible, luxury insider content
---

# Avatar: Lorenzo
# Path: /skills/ai-avatars/lorenzo/SKILL.md

## Character Reference
- **Name:** Lorenzo
- **Age:** Late 60s
- **Gender:** Male
- **Airtable Image URL:** https://v3b.fal.media/files/b/0a94d2ed/UaKQyb9dAvPq-tQ0uLx46_plAnk6Wg.png
- **Reference Image URL:** https://v3b.fal.media/files/b/0a94d2ea/wDNak3GcTIVjyFbMGJ07e_rich%20old%20guy.PNG

## Character Profile
Old money archetype. Authoritative, zero smile, "I know something you don't" energy. Wealthy authority + luxury context + insider reveal. "Rich guy tells you how the world actually works" content.

## Image Generation Prompt
An older man with silver hair sitting in a Ferrari convertible with red leather seats. Navy blazer, white shirt, holding sunglasses toward the camera. Waist-up shot, slight low angle. The lighting is natural and bright. Behind his head is the Ferrari logo stitching on the headrest. iPhone UGC style photo, as if he is a TikToker filming a casual ugc video. No phones or cameras visible. Mid angle shot as if he placed his phone on the dash.

## Generation Method
**CRITICAL: Always use `fal-ai/nano-banana-pro/edit` (NOT the base model) with `image_urls` for character consistency.**

```json
{
  "app_id": "fal-ai/nano-banana-pro/edit",
  "input_data": {
    "prompt": "[IMAGE_PROMPT]",
    "image_urls": ["https://v3b.fal.media/files/b/0a94d2ea/wDNak3GcTIVjyFbMGJ07e_rich%20old%20guy.PNG"],
    "aspect_ratio": "9:16",
    "num_images": 1
  }
}
```

## Consistency Notes
- Always use `fal-ai/nano-banana-pro/edit` with `image_urls` — never the base model
- Pass the Reference Image URL above in `image_urls` every time
- Style: iPhone UGC — casual, not professional
- Shot: Waist-up, mid angle, phone on dash
- Wardrobe: Navy blazer, white open-collar shirt, no accessories
- Setting: Ferrari convertible, red/burgundy leather, Ferrari logo on headrest
