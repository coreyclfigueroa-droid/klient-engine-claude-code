# SKILL: Micro Camera Animal

## TRIGGER
User says "micro camera", "animal camera", "bug cam", "critter cam", or "mounted camera"

## VIDEO MODEL
Kling 3.0 Pro via OpenArt — start/end frame image-to-video

## COST SETTINGS
- Image resolution: 1K
- Video duration: 3 seconds
- No upscaling

---

## ABOUT THIS SKILL
Cinematic ultra-realistic mounted micro-camera POV content. A researcher attaches a microscopic camera to a tiny ground-dwelling or burrowing animal, then the viewer experiences the world from the animal's perspective — entering its habitat, navigating tight spaces, encountering other creatures, discovering nesting sites, and exploring the deepest reaches of its environment. Each image captures a distinct phase of the journey. Videos chain phase-to-phase so the final edit stitches into a complete exploration documentary reel.

---

## STEP 1 — ASK QUESTIONS

Use the AskUserQuestion tool for every question so the user can click their choice. Ask one question at a time. Wait for the answer before asking the next. The "Other" option is automatically provided by the tool for free-text custom input.

**Question 1A — Animal Category**
question: "What type of animal?"
header: "Animal"
options:
  1. label: "Insect" / description: "Ant, termite, beetle, dung beetle, mole cricket, earwig, silverfish, springtail, cave cricket"
  2. label: "Arachnid" / description: "Burrowing spider, trapdoor spider, wolf spider, centipede, millipede"
  3. label: "Small Mammal" / description: "Field mouse, shrew, vole, harvest mouse, pygmy gerbil, naked mole-rat, desert kangaroo rat"
  4. label: "Reptile / Other" / description: "Sandfish lizard, woodlouse (pill bug), velvet ant, rove beetle"

**Question 1B — Insect Sub-pick** (only if user picked Insect)
question: "Which insect?"
header: "Insect"
options:
  1. label: "Ant" / description: "Small black or red ant — tunnels, colony chambers, queen"
  2. label: "Termite" / description: "Pale soft-bodied — mud tunnels, wood galleries, fungus gardens"
  3. label: "Beetle" / description: "Ground beetle or dung beetle — surface paths, burrow entries"
  4. label: "Silverfish" / description: "Elongated segmented body — wall cracks, bathroom tiles, dark cavities"

(Show additional sub-picks if Other is selected — earwig, mole cricket, springtail, cave cricket, weevil, rove beetle)

**Question 1B — Arachnid Sub-pick** (only if user picked Arachnid)
question: "Which arachnid/myriapod?"
header: "Arachnid"
options:
  1. label: "Trapdoor spider" / description: "Builds hinged silk-lined burrow — ambush predator"
  2. label: "Wolf spider" / description: "Ground hunter — carries egg sac, no web"
  3. label: "Centipede" / description: "Fast predator — soil gaps, rotting wood, leaf litter"
  4. label: "Millipede" / description: "Slow decomposer — curls defensively, damp soil and logs"

**Question 1B — Small Mammal Sub-pick** (only if user picked Small Mammal)
question: "Which small mammal?"
header: "Mammal"
options:
  1. label: "Field mouse" / description: "Grass tunnels, seed caches, shallow burrows"
  2. label: "Naked mole-rat" / description: "Underground colony — tunnels, queen chamber, worker castes"
  3. label: "Shrew" / description: "Frantic hunter — leaf litter, shallow tunnels, insect prey"
  4. label: "Harvest mouse" / description: "Tiny — climbs grass stems, woven nest above ground"

(Show additional sub-picks if Other is selected — vole, pygmy gerbil, desert kangaroo rat, juvenile ground squirrel, juvenile chipmunk, pocket gopher)

**Question 1B — Reptile/Other Sub-pick** (only if user picked Reptile/Other)
question: "Which animal?"
header: "Other"
options:
  1. label: "Sandfish lizard" / description: "Swims through sand — desert specialist"
  2. label: "Woodlouse (pill bug)" / description: "Rolls into ball — damp soil, rotting wood"
  3. label: "Velvet ant" / description: "Wingless wasp — desert floor, parasitizes ground nests"
  4. label: "Burrowing spider" / description: "Silk-lined tunnel builder — ambush from burrow entrance"

**Question 2 — Habitat / Environment**
question: "What environment does it explore?"
header: "Habitat"
options:
  1. label: "Indoor / structural" / description: "Wall cracks, bathroom tiles, basement gaps, under appliances"
  2. label: "Underground burrow" / description: "Soil tunnels, root networks, chamber systems"
  3. label: "Forest floor" / description: "Leaf litter, rotting logs, moss, bark crevices"
  4. label: "Desert / arid" / description: "Sand, rock crevices, dry scrub, heat shimmer"

**Question 3 — Lighting Condition**
question: "What's the lighting?"
header: "Lighting"
options:
  1. label: "Mounted LED only" / description: "Total darkness — harsh narrow beam from camera LED"
  2. label: "Mixed natural + LED" / description: "Starts with ambient light, transitions to LED as animal goes deeper"
  3. label: "Natural light" / description: "Daytime surface exploration — sunlight, dappled shade"

**Question 4 — Audio Style**
question: "What audio treatment?"
header: "Audio"
options:
  1. label: "Raw ambient only" / description: "Scratching, skittering, dirt shifting — no music, no narration"
  2. label: "Cinematic voiceover" / description: "David Attenborough-style narration over ambient sounds"
  3. label: "Cinematic score" / description: "Tense documentary music — no narration"
  4. label: "Silent" / description: "No audio at all"

---

## STEP 2 — GENERATE ALL IMAGES

Tell the user: "Image generation cost: **$1.05** (7 images × $0.15 each — one per phase). Generating now..."

Once all questions are answered, generate 7 images in order — one per phase. Send each to OpenArt using Nano Banana Pro at 1K resolution.

Use the user's answers to customize every prompt. Replace [ANIMAL], [BODY_DETAIL], [HABITAT], [LIGHTING], and [AUDIO_NOTE] with their choices throughout.

---

**Prompt 0 — Surface Setup: Attaching the Camera**
Ultra-realistic macro wildlife research photograph of a researcher kneeling on [HABITAT] surface beside the entrance to [ANIMAL]'s habitat, gently holding a live [ANIMAL] between fingertips while carefully securing a microscopic research camera onto its [BODY_DETAIL] using a tiny transparent harness, the [ANIMAL]'s body clearly visible at true scale, natural details of the surrounding environment, [LIGHTING], dust particles, raw scientific documentation style, photorealistic, high detail, no fantasy, no stylization

**Prompt 1 — Mounted POV: Entering the Habitat**
Ultra-realistic mounted micro-camera POV from a [ANIMAL] approaching and entering its [HABITAT], the camera is physically strapped to its [BODY_DETAIL] and faces exactly where the [ANIMAL] faces, 5 to 10 percent of its body visible at the bottom of frame, slight jitter from movement, no stabilization, the [ANIMAL] moves from open space into the habitat entrance, [LIGHTING] with transition as depth increases, tight confined space ahead, realistic motion, photorealistic, high detail, no people

**Prompt 2 — Navigating Tight Interior Spaces**
Ultra-realistic mounted micro-camera POV from a [ANIMAL] moving deeper inside [HABITAT], camera fixed to [BODY_DETAIL], forward-facing, slight body-driven shaking, no stabilization, LED light reveals layered debris, organic material, and micro-organisms moving through the space, tunnel or passage narrows and branches, occasional body contact with walls causes small jolts, darkness dominates outside the beam, realistic confined exploration, photorealistic, high detail, no people

**Prompt 3 — Encountering Others of Its Kind**
Ultra-realistic mounted micro-camera POV from a [ANIMAL] entering an area populated by multiple [ANIMAL]s, camera remains fixed and aligned with head direction, 5 to 10 percent of body visible at bottom, LED beam sweeps across several [ANIMAL]s moving through the space, interaction between individuals — antennae touching, body contact, territorial behavior or colony activity, tight environment, frequent micro-encounters, natural jitter from body movement, photorealistic, high detail, no people

**Prompt 4 — Nest / Egg / Cache Discovery**
Ultra-realistic mounted micro-camera POV from a [ANIMAL] approaching a concealed nesting site, egg cluster, or food cache within [HABITAT], camera fixed on [BODY_DETAIL], tilting slightly as the animal adjusts position, LED beam reveals the nest contents — eggs, larvae, stored food, or nesting material — nestled among the surrounding environment, occasional nearby movement from other inhabitants, minimal space, no stabilization, slight vibrations from body movement, photorealistic, high detail, no people

**Prompt 5 — Deepest Point of Exploration**
Ultra-realistic mounted micro-camera POV from a [ANIMAL] moving through the deepest, most confined section of [HABITAT], camera on [BODY_DETAIL], the space barely fits the animal, LED light catches dense organic material, root systems or structural elements, moisture or condensation, micro-fauna (mites, springtails) scattering from the light, claustrophobic framing, the animal pauses and reverses direction, photorealistic, high detail, no people

**Prompt 6 — Return to Surface / Exit**
Ultra-realistic mounted micro-camera POV from a [ANIMAL] navigating back toward the surface, camera on [BODY_DETAIL], light gradually increasing as the animal approaches the habitat exit, transition from LED-only to natural light flooding the frame, final emergence into open air, the contrast between dark confined interior and bright open exterior, the animal pauses at the threshold, photorealistic, high detail, no people

---

## STEP 3 — LOG TO AIRTABLE

After all 7 images are generated, create 7 records in the Projects table — one per image. Each record:

- Ad Name: "Micro Camera [ANIMAL] — [Phase Name]"
- Product Name: Micro Camera — [ANIMAL]
- Image Prompt: the prompt used
- Image Generator: Nano Banana Pro via OpenArt
- Image Status: Pending
- Generated Image 1: the generated image

Display all 7 images in the terminal and STOP.

---

## STEP 4 — WAIT FOR APPROVAL

Do not proceed to video generation until the user explicitly approves. Ask: "Ready to generate the exploration videos?"

---

## STEP 5 — GENERATE CHAINED VIDEOS

Tell the user the video cost breakdown before generating. Base rate is $0.14/sec (Kling 3.0 Pro):

"Here's the full cost breakdown for your run:

| Option | Image Cost | Video Cost (6 clips × 3s) | Total |
|--------|------------|---------------------------|-------|
| Silent | $1.05 | $2.52 | **$3.57** |
| Raw ambient audio ON | $1.05 | ~$2.52+ | **~$3.57+** |
| Cinematic score audio ON | $1.05 | ~$2.52+ | **~$3.57+** |
| Cinematic voiceover | $1.05 | ~$2.52+ | **~$3.57+** (voiceover generated separately in post) |

You selected: **[AUDIO_STYLE]**

Your estimated cost: **$[TOTAL_COST]**
Generating all 6 clips now..."

Calculate [TOTAL_COST] as:
- Silent: 6 × 3 × $0.14 + $1.05 = $3.57
- Audio ON: use actual fal.ai rate if available, otherwise estimate same as silent for generation cost

Once approved, generate 6 videos using Kling 3.0 Pro via OpenArt. Settings: 3 seconds, [AUDIO_NOTE]. Chain images so each video flows into the next phase:

- Video 1: Image 0 (start frame) → Image 1 (end frame) — Setup transitions to entering habitat
- Video 2: Image 1 (start frame) → Image 2 (end frame) — Entrance transitions to deep navigation
- Video 3: Image 2 (start frame) → Image 3 (end frame) — Navigation transitions to encountering others
- Video 4: Image 3 (start frame) → Image 4 (end frame) — Encounter transitions to nest/cache discovery
- Video 5: Image 4 (start frame) → Image 5 (end frame) — Discovery transitions to deepest point
- Video 6: Image 5 (start frame) → Image 6 (end frame) — Deepest point transitions to surface return

Use the corresponding prompt text as the video prompt for each clip. Generate all 6 in parallel.

---

## STEP 6 — UPDATE AIRTABLE

Update each record with:

- Video Prompt: the prompt used
- Video Generator: Kling 3.0 Pro via OpenArt
- Video Status: Pending
- Video Generation 1: the generated video URL

Display all 6 videos in the terminal in order so the user can review and stitch manually.

---

## ANIMAL BODY DETAIL REFERENCE

Use these details when filling in [BODY_DETAIL] for camera placement:

| Animal | Camera Mount Point | Key Visual Details |
|--------|-------------------|-------------------|
| Ant | upper thorax | Six legs, segmented body, antennae, mandibles |
| Termite | upper thorax | Pale soft body, short antennae, large head |
| Beetle | pronotum (back plate) | Hard wing covers, six legs, antennae |
| Silverfish | upper thorax | Elongated segmented body, three tail filaments, long antennae |
| Dung beetle | pronotum | Rounded body, strong forelegs, horn (if male) |
| Earwig | upper thorax | Pincers at rear, flat elongated body |
| Mole cricket | upper thorax | Shovel-like forelegs, cylindrical body |
| Trapdoor spider | cephalothorax | Eight legs, fangs, silk spinnerets, stocky body |
| Wolf spider | cephalothorax | Eight legs, large front eyes, carries egg sac |
| Centipede | head plate | Many leg pairs, long antennae, fast movement |
| Millipede | head segment | Rounded segments, many short legs, slow rolling gait |
| Field mouse | upper back between shoulders | Fur, round ears, long tail, whiskers |
| Naked mole-rat | upper back | Wrinkled pink skin, large incisors, nearly blind |
| Shrew | upper back between shoulders | Pointed snout, tiny eyes, dense fur, rapid breathing |
| Harvest mouse | upper back | Prehensile tail, tiny round ears, golden-brown fur |
| Sandfish lizard | upper back | Smooth scales, wedge-shaped snout, limbs tucked for sand swimming |
| Woodlouse | upper shell plate | Segmented armor, seven pairs of legs, rolls into ball |
| Velvet ant | upper thorax | Dense fuzzy hair, bright warning colors, no wings |
| Springtail | upper thorax | Tiny, spring-loaded furcula for jumping, round body |
| Cave cricket | upper thorax | Long curved antennae, powerful hind legs, humpbacked |

## HABITAT DETAIL REFERENCE

Use these details when filling in environment-specific prompt sections:

| Habitat | Entry Point | Interior Details | Deepest Zone |
|---------|-------------|-----------------|--------------|
| Indoor / structural | Wall crack, baseboard gap, tile seam | Plaster dust, fibers, hair, paper fragments, mites, condensation | Deep wall void, insulation fibers, wiring, pipe surfaces |
| Underground burrow | Soil hole, root gap, mound entrance | Packed earth, root tendrils, fungal threads, beetle larvae | Chamber systems, queen cells, food storage, moisture pockets |
| Forest floor | Leaf litter edge, log crack, bark gap | Decomposing leaves, moss, fungal fruiting bodies, soil fauna | Under rotting heartwood, dense mycelium networks, larval galleries |
| Desert / arid | Sand surface, rock crack, scrub base | Compacted sand walls, seed caches, shed skins, heat refuge zones | Deep cool chamber, moisture condensation, dormant insects |

## VOICEOVER REFERENCE (if user selected cinematic voiceover)

Generate ElevenLabs voiceover script for each video clip. Tone: calm, reverent, David Attenborough-style nature documentary. Each script should be 2-3 sentences that narrate what the viewer is seeing. Use the audio note in the video prompt to indicate voiceover track should be added in post.