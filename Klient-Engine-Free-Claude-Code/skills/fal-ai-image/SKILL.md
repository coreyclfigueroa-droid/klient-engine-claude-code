# fal.ai Image Generation — Settings Reference
# Path: /skills/fal-ai-image/SKILL.md

## Default Model
**fal-ai/nano-banana-2**

## Standard Parameters

### Avatar / Talking Head Images (16:9 for Airtable preview)
```json
{
  "prompt": "[your prompt]",
  "aspect_ratio": "16:9",
  "resolution": "1K",
  "num_images": 1
}
```

### Video Production Images (9:16 vertical for TikTok/Reels/Shorts)
```json
{
  "prompt": "[your prompt]",
  "aspect_ratio": "9:16",
  "resolution": "2K",
  "num_images": 1
}
```

## Queue-Based Call Pattern
1. Call `mcp__fal__generate` with `app_id: "fal-ai/nano-banana-2"` and input_data above
2. Get back `request_id`
3. Call `mcp__fal__result` with the same `app_id` and `request_id`
4. If still in progress, wait and retry
5. Extract `images[0].url` from the result

## Negative Prompt (always append for avatars)
```
Smooth plastic skin, airbrushed, CGI, 3D render, studio lighting,
perfect symmetry, symmetrical lighting, glossy finish, oversharpened, fake,
digital art, illustration, cartoon, watermark, studio backdrop, lens flare,
obviously posed position, perfect lighting
```

## Quality Tips
- For photorealistic humans: always include "Natural skin pore texture, no airbrushing, candid unposed feel"
- For environments: include "slightly desaturated natural color grade, subtle film grain, raw documentary style"
- Resolution: use 1K for fast previews, 2K for production video frames
