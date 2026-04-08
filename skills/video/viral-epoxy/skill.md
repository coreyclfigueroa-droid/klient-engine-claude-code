# SKILL: Epoxy Floor Timelapse

## TRIGGER
User says "epoxy floor", "epoxy timelapse", or "floor coating"

## VIDEO MODEL
Kling 3.0 Pro via fal.ai — start/end frame image-to-video

## COST SETTINGS
- Image resolution: 1K
- Video duration: 3 seconds
- No audio, no voice, no SFX
- No upscaling

---

## ABOUT THIS SKILL
Cinematic overhead and wide-angle timelapse documentation of an epoxy floor installation from bare concrete to finished product. Each image captures a distinct stage of the process. Videos chain stage-to-stage so the final edit stitches into a satisfying before-to-after transformation reel.

---

## STEP 1 — ASK QUESTIONS

Use the AskUserQuestion tool for every question so the user can click their choice. Ask one question at a time. Wait for the answer before asking the next. The "Other" option is automatically provided by the tool for free-text custom input.

**Question 1A — Epoxy Style Category**
question: "What epoxy style category?"
header: "Style"
options:
  1. label: "Metallic" / description: "Metallic swirl (silver, black, gold) or Rose gold metallic (pink, gold, champagne)"
  2. label: "Nature & Fantasy" / description: "Ocean/water, Lava/magma, Galaxy/nebula, or Geode slice"
  3. label: "Classic & Practical" / description: "Solid color garage, Flake broadcast, Marble effect, or Commercial solid"
  4. label: "Specialty" / description: "3D penny floor (copper pennies set in clear epoxy)"

**Question 1B — Metallic Sub-pick** (only if user picked Metallic)
question: "Which metallic style?"
header: "Metallic"
options:
  1. label: "Metallic swirl" / description: "Silver, black, gold — pigment blown with heat gun into cloud patterns"
  2. label: "Rose gold metallic" / description: "Pink, gold, champagne — swirled in circular overlapping patterns"

**Question 1B — Nature & Fantasy Sub-pick** (only if user picked Nature & Fantasy)
question: "Which nature/fantasy style?"
header: "Nature"
options:
  1. label: "Ocean / water" / description: "Deep blue, teal, white — wave and foam patterns"
  2. label: "Lava / magma" / description: "Black base, red and orange veins — cooled lava look"
  3. label: "Galaxy / nebula" / description: "Deep purple, midnight blue, silver flecks — star cluster patterns"
  4. label: "Geode slice" / description: "Crystal-look with resin borders, deep blue or purple center"

**Question 1B — Classic & Practical Sub-pick** (only if user picked Classic & Practical)
question: "Which classic style?"
header: "Classic"
options:
  1. label: "Solid color" / description: "Grey, tan, or red — clean single-color garage floor"
  2. label: "Flake broadcast" / description: "Speckled multicolor chips broadcast over base color"
  3. label: "Marble effect" / description: "White base with grey and gold veining"
  4. label: "Commercial solid" / description: "Grey, beige, safety yellow — high-build industrial finish"

(If user picked Specialty, skip 1B — style is 3D penny floor.)

**Question 2A — Space Category**
question: "What type of space?"
header: "Space"
options:
  1. label: "Residential" / description: "Garage, basement/man cave, bathroom, or entryway/foyer"
  2. label: "Commercial" / description: "Warehouse, restaurant/kitchen, retail showroom, or car dealership"
  3. label: "Gym / fitness" / description: "Gym or fitness studio floor"
  4. label: "Outdoor patio" / description: "Outdoor patio with clear sealant"

**Question 2B — Residential Sub-pick** (only if user picked Residential)
question: "Which residential space?"
header: "Room"
options:
  1. label: "Garage" / description: "Residential garage floor"
  2. label: "Basement / man cave" / description: "Finished or unfinished basement"
  3. label: "Bathroom" / description: "Bathroom floor"
  4. label: "Entryway / foyer" / description: "Front entryway or foyer"

**Question 2B — Commercial Sub-pick** (only if user picked Commercial)
question: "Which commercial space?"
header: "Commercial"
options:
  1. label: "Warehouse" / description: "Commercial warehouse or industrial space"
  2. label: "Restaurant / kitchen" / description: "Restaurant or commercial kitchen"
  3. label: "Retail showroom" / description: "Retail store or showroom floor"
  4. label: "Car dealership" / description: "Car dealership showroom floor"

(If user picked Gym/fitness or Outdoor patio, skip 2B — space is already set.)

**Question 3 — Starting Condition**
question: "What does the floor look like before?"
header: "Condition"
options:
  1. label: "Raw concrete, clean" / description: "Bare grey concrete in decent shape"
  2. label: "Cracked & stained" / description: "Old concrete with cracks, stains, and wear"
  3. label: "Old paint being stripped" / description: "Previously painted floor being removed"
  4. label: "New construction slab" / description: "Fresh bare slab, brand new pour"

(If user picks Other, they can type "Tile being removed, adhesive residue left behind" or any custom condition.)

**Question 4 — Camera Angle**
question: "Pick your timelapse camera angle?"
header: "Angle"
options:
  1. label: "Overhead / top-down" / description: "Drone or ceiling-mounted looking straight down"
  2. label: "Wide ground-level" / description: "Low angle looking across the floor surface"
  3. label: "Corner perspective" / description: "Shows full room depth from a corner"
  4. label: "Mix" / description: "Overhead for wide shots, close-up macro for detail shots"

---

## STEP 2 — GENERATE ALL IMAGES

Tell the user: "Image generation cost: **$1.05** (7 images × $0.15 each — one per stage). Generating now..."

Once all questions are answered, generate 7 images in order — one per stage. Send each to OpenArt using Nano Banana Pro at 1K resolution.

Use the user's answers to customize every prompt. Replace [STYLE], [SPACE], [CONDITION], and [ANGLE] with their choices throughout.

---

**Prompt 0 — Before: Raw Floor**
Cinematic [ANGLE] shot of a [SPACE] floor before any epoxy work. [CONDITION]. Harsh overhead work lighting. Empty room, bare walls. Floor shows every crack, stain, and imperfection in sharp detail. Dusty concrete texture, aggregate visible. Industrial realism. Photorealistic, high detail, no people.

**Prompt 1 — Surface Prep: Grinding**
Cinematic [ANGLE] shot of a [SPACE] floor mid-preparation. A diamond-cup floor grinder has cut overlapping circular paths across the concrete surface, leaving a rough open-pore texture. Fine grey concrete dust coats the edges of the room. Extension cord runs across the floor. Ground areas show fresh bright concrete against the old stained surface. Photorealistic, high detail, no people.

**Prompt 2 — Primer Coat**
Cinematic [ANGLE] shot of a [SPACE] floor with epoxy primer being applied. A thin clear-to-slightly-amber coat covers roughly half the floor, spreading in deliberate roller strokes. The wet primer catches the work lights, creating a subtle reflection line between coated and uncoated concrete. Paint tray and roller visible at edge of frame. Photorealistic, high detail, no people.

**Prompt 3 — Base Coat Pour**
Cinematic [ANGLE] shot of a [SPACE] floor with [STYLE] epoxy base coat freshly poured. A glossy puddle of mixed epoxy spreads from center outward, showing the raw unmixed color pooling in organic shapes. Edges of the pour still moving slowly. Work lights reflect in the wet surface. The concrete is completely hidden under the spreading coat. Vivid color, photorealistic, high detail, no people.

**Prompt 4 — The Swirl / Effect Application**
Cinematic [ANGLE] close-up shot of a [SPACE] floor mid-effect. [STYLE]-specific detail: the epoxy is being manipulated — metallic pigment being blown with a heat gun creating swirling cloud patterns, OR decorative flakes broadcasting through the air landing on the wet base, OR a second contrasting color being poured in streams and feathered with a squeegee. The wet floor surface captures every motion in real time. Vivid, dynamic, photorealistic, high detail, no people.

**Prompt 5 — Top Coat / Clear Seal**
Cinematic [ANGLE] shot of a [SPACE] floor with clear polyurethane or epoxy top coat applied. The surface is now mirror-smooth and highly reflective. Room lights, ceiling, and walls are perfectly reflected in the floor surface. The [STYLE] design underneath shows through the clear layer in full depth and clarity. Photorealistic, ultra-glossy finish, high detail, no people.

**Prompt 6 — Final Reveal**
Cinematic [ANGLE] shot of a fully finished [SPACE] with a complete [STYLE] epoxy floor. The room is clean, floor is polished and perfect. If garage: a vehicle partially visible at the edge. If commercial: empty clean space under bright lighting. If residential: clean finished room. Floor reflects overhead lights in clean sharp lines. The transformation is complete. Before-and-after visual impact. Photorealistic, ultra-high detail, no people.

---

## STEP 3 — LOG TO AIRTABLE

After all 7 images are generated, create 7 records in the Projects table — one per image. Each record:

- Ad Name: "Epoxy Floor Timelapse — [Stage Name]"
- Product Name: Epoxy Floor — [STYLE]
- Image Prompt: the prompt used
- Image Generator: Nano Banana Pro via OpenArt
- Image Status: Pending
- Generated Image 1: the generated image

Display all 7 images in the terminal and STOP.

---

## STEP 4 — WAIT FOR APPROVAL

Do not proceed to video generation until the user explicitly approves. Ask: "Ready to generate the timelapse videos?"

---

## STEP 5 — GENERATE CHAINED VIDEOS

Tell the user the full cost breakdown before generating:

"Here's what this run costs (settings are fixed for epoxy timelapse):

| Setting | Value | Cost |
|---------|-------|------|
| Images | 7 × $0.15 | $1.05 |
| Videos | 6 clips × 3s × $0.14/sec | $2.52 |
| Audio | None | $0.00 |
| **Total** | | **$3.57** |

This skill always runs at these settings (3s clips, Kling 3.0 Pro, no audio). No options to change.
Generating all 6 clips now..."

Once approved, generate 6 videos using Kling 3.0 Pro via OpenArt. Settings: 3 seconds, no audio, no SFX. Chain images so each video flows into the next stage:

- Video 1: Image 0 (start frame) → Image 1 (end frame) — Raw floor transforms into prepped surface
- Video 2: Image 1 (start frame) → Image 2 (end frame) — Prepped surface gets primer
- Video 3: Image 2 (start frame) → Image 3 (end frame) — Primer transforms into base coat pour
- Video 4: Image 3 (start frame) → Image 4 (end frame) — Base coat gets effect applied
- Video 5: Image 4 (start frame) → Image 5 (end frame) — Effect stage gets sealed with top coat
- Video 6: Image 5 (start frame) → Image 6 (end frame) — Top coat transforms into final reveal

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

## EPOXY STYLE REFERENCE

Use these details when filling in [STYLE]-specific prompt sections:

| Style | Colors | Effect Detail |
|-------|--------|---------------|
| Metallic swirl | Silver, black, charcoal | Metallic pigment blown with heat gun into cloud and galaxy patterns |
| Ocean / water | Deep blue, teal, white | White pigment feathered into blue base to create wave and foam patterns |
| Lava / magma | Black base, red and orange veins | Orange and red poured in streams, black feathered between to simulate cooled lava |
| Galaxy / nebula | Deep purple, midnight blue, silver flecks | Silver metallic flakes broadcast over dark base, blown into star cluster patterns |
| Solid garage | Grey, tan, red, or black | Single solid color rolled evenly, anti-slip texture added to top coat |
| Flake broadcast | Any base color with multicolor chips | Decorative vinyl flakes broadcast by hand into wet base coat, fully broadcast for full coverage |
| 3D penny floor | Copper penny color, amber clear | Real or printed pennies laid flat in pattern, clear epoxy poured over to encapsulate |
| Marble effect | White base, grey and gold veins | Veining painted with a feather or brush in thin organic lines across white base |
| Geode slice | Deep blue or purple center, white crystal border, clear fill | Irregular organic border shape filled with colored epoxy, crystal-look aggregate along edges |
| Rose gold metallic | Pink, gold, champagne | Rose gold metallic pigment swirled in circular overlapping patterns |
| Commercial solid | Grey, beige, safety yellow, or green | Solid color, uniform roller application, high-build industrial finish |