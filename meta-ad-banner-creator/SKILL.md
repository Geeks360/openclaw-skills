---
name: meta-ad-banner-creator
description: >
  Generate production-ready advertising banners for Meta Ads (Facebook, Instagram).
  Use when the user asks to create ad creatives, banners, promotional images,
  or any visual assets for Facebook/Instagram advertising campaigns.
  Produces clean, professional banners with perfectly readable text,
  proper safe zones, and mobile-first design.
  Triggers: ad banner, Facebook ad, Instagram ad, Meta ad, рекламный баннер,
  креатив, рекламный креатив, промо-баннер, Stories ad, Reels ad, carousel ad.
version: "1.0"
date: "2026-04-07"
prices_current_as_of: "2026-04-07"  # Model pricing may change — verify before use
author: custom
---

# Meta Ad Banner Creator

Create professional, high-converting advertising banners for Facebook and Instagram.
This skill produces banners with perfectly readable text, correct safe zones,
and designs optimized for mobile-first viewing.

---

## CRITICAL RULE: NEVER BAKE TEXT INTO AI-GENERATED IMAGES

AI image generators (Midjourney, FLUX, DALL-E, Stable Diffusion) produce
**unreadable, garbled, misaligned text** when you include text in the prompt.

**NEVER DO THIS:**
```
prompt: "A banner with text 'Hiring psychiatrists' on a photo of a doctor"
```
This will produce broken, overlapping, misspelled text every time.

**ALWAYS DO THIS instead — use the Two-Layer Method:**
1. Generate a background image with NO text in the prompt
2. Overlay text programmatically using HTML/CSS or a graphics library

This is non-negotiable. Every banner must use this two-layer approach.

---

## Step 1: Understand the Ad Format

Before generating anything, determine the placement and gather specs.

### Size Reference Table

| Placement            | Size (px)   | Aspect Ratio | Use Case                        |
|----------------------|-------------|--------------|----------------------------------|
| Feed (Universal)     | 1080 × 1080 | 1:1          | Default for FB + IG Feed        |
| Feed (Tall)          | 1080 × 1350 | 4:5          | More screen real estate in Feed |
| Stories / Reels      | 1080 × 1920 | 9:16         | Full-screen vertical            |
| Carousel (per card)  | 1080 × 1080 | 1:1          | Swipeable multi-card            |
| Right Column (FB)    | 1080 × 1080 | 1:1          | Desktop sidebar                 |
| In-Stream Video Thumb| 1080 × 1080 | 1:1          | Video ad thumbnail              |
| Marketplace          | 1080 × 1080 | 1:1          | FB Marketplace listing          |

**If the user doesn't specify a format, default to 1080×1350 (4:5)** — it takes
up the most screen space in the Feed and works well across placements.

### Safe Zones — Where NOT to Place Content

Meta overlays UI elements (profile name, CTA button, swipe-up indicator)
on top of your creative. Keep critical content away from these areas:

**Feed (1080×1080 or 1080×1350):**
- Top 180px — reserved for account name + "Sponsored" label
- Bottom 180px — reserved for headline, CTA button, description text

**Stories/Reels (1080×1920):**
- Top 250px — reserved for account name, close button
- Bottom 340px — reserved for CTA button, swipe-up, reply bar

**Practical rule:** Keep all important text and key visual elements in the
**center 60%** of the image. Treat the outer edges as bleed/atmosphere only.

---

## Step 2: Generate the Background Image

Create a prompt for the AI image generator that describes ONLY the visual scene.
Do NOT include any text, words, letters, or typography in the prompt.

### Prompt Structure for Backgrounds

```
[Scene description], [lighting], [mood/atmosphere], [style],
[composition notes], no text, no words, no letters, no typography,
no watermarks, clean background for advertising
```

### Good Background Prompt Examples

**For a hiring/recruitment ad:**
```
Professional doctor in white coat sitting at a modern desk with a laptop
showing a video call, bright natural window light from the left,
clean modern medical office interior, warm and inviting atmosphere,
shallow depth of field on background, photorealistic,
no text no words no letters no typography no watermarks
```

**For a product ad:**
```
Minimalist flat-lay of skincare bottles on white marble surface,
soft diffused studio lighting from above, pastel pink and gold accents,
luxury beauty aesthetic, editorial photography style, negative space
on the right side for copy placement,
no text no words no letters no typography no watermarks
```

**For an app/SaaS ad:**
```
Abstract gradient background, deep navy blue transitioning to electric
purple, subtle geometric light patterns, modern tech aesthetic,
large area of clean negative space in the center,
no text no words no letters no typography no watermarks
```

### Key Rules for Background Prompts

1. **Always end with:** `no text no words no letters no typography no watermarks`
2. **Leave space for text:** Include "negative space" or "clean area for copy"
   in the prompt so the AI leaves room for your text overlay
3. **Think mobile-first:** The image will be viewed on a phone screen ~375px wide.
   Avoid tiny details that disappear at small sizes
4. **Prefer simple compositions:** One clear subject + clean background.
   Cluttered scenes make text unreadable
5. **Match the aspect ratio:** Generate at the target size or crop afterward

### Recommended Image Models (by priority)

For OpenClaw, use these models/services in order of preference:

1. **Recraft** (via MCP skill) — best text rendering if you DO need text baked in,
   plus brand kit for style consistency, SVG export
2. **FLUX 2 Pro** (via inference.sh or BFL API) — excellent photorealism,
   good text capability, $0.03/image
3. **Ideogram 3.0** (via fal.ai) — best text accuracy of any model,
   $0.03–0.09/image
4. **Nano Banana Pro** (via Google/APIYI) — fast, good quality, $0.05/image
5. **Pollinations.ai** (free, no API key) — good for quick drafts

---

## Step 3: Create the Text Overlay (THE MOST IMPORTANT STEP)

Text must be rendered programmatically — NEVER by the AI image generator.

### Method A: HTML/CSS Banner (Recommended)

Generate an HTML file that composites the background image with text overlays.
This gives pixel-perfect control over fonts, sizes, colors, and positioning.

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<style>
  @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap');

  * { margin: 0; padding: 0; box-sizing: border-box; }

  .banner {
    position: relative;
    width: 1080px;
    height: 1350px;       /* 4:5 Feed format */
    overflow: hidden;
    font-family: 'Inter', sans-serif;
  }

  .banner__bg {
    position: absolute;
    inset: 0;
    width: 100%;
    height: 100%;
    object-fit: cover;
  }

  /* Dark gradient overlay to ensure text readability */
  .banner__overlay {
    position: absolute;
    inset: 0;
    background: linear-gradient(
      to bottom,
      rgba(0,0,0,0.0) 0%,
      rgba(0,0,0,0.0) 30%,
      rgba(0,0,0,0.55) 60%,
      rgba(0,0,0,0.8) 100%
    );
  }

  /* Safe zone: keep text within center 60% vertically */
  .banner__content {
    position: absolute;
    bottom: 200px;          /* above the Meta CTA zone */
    left: 60px;
    right: 60px;
    color: #FFFFFF;
  }

  .banner__headline {
    font-size: 64px;
    font-weight: 900;
    line-height: 1.1;
    margin-bottom: 16px;
    text-shadow: 0 2px 12px rgba(0,0,0,0.5);
  }

  .banner__headline em {
    font-style: normal;
    color: #00E5CC;         /* accent color for key phrase */
  }

  .banner__subline {
    font-size: 32px;
    font-weight: 700;
    letter-spacing: 2px;
    margin-bottom: 12px;
    color: #00E5CC;
  }

  .banner__body {
    font-size: 26px;
    font-weight: 400;
    line-height: 1.4;
    opacity: 0.85;
    font-style: italic;
  }
</style>
</head>
<body>
  <div class="banner">
    <img class="banner__bg" src="background.jpg" alt="">
    <div class="banner__overlay"></div>
    <div class="banner__content">
      <div class="banner__headline">
        CA telehealth company<br>
        <em>hiring psychiatrists.</em>
      </div>
      <div class="banner__subline">
        Remote · Contract · Flexible hours
      </div>
      <div class="banner__body">
        Fully remote, set your own schedule
      </div>
    </div>
  </div>
</body>
</html>
```

Then screenshot/export this HTML to a PNG at 1080×1350.

### Method B: Python with Pillow (for automation)

```python
from PIL import Image, ImageDraw, ImageFont
import textwrap

# Load background
bg = Image.open("background.jpg").resize((1080, 1350))
draw = ImageDraw.Draw(bg)

# Semi-transparent gradient overlay
overlay = Image.new("RGBA", (1080, 1350), (0, 0, 0, 0))
overlay_draw = ImageDraw.Draw(overlay)
for y in range(1350):
    alpha = int(min(255, max(0, (y - 400) / 950 * 200)))
    overlay_draw.line([(0, y), (1080, y)], fill=(0, 0, 0, alpha))
bg = Image.alpha_composite(bg.convert("RGBA"), overlay)
draw = ImageDraw.Draw(bg)

# Fonts — use a clean sans-serif
font_headline = ImageFont.truetype("Inter-Black.ttf", 64)
font_accent   = ImageFont.truetype("Inter-Black.ttf", 64)
font_sub      = ImageFont.truetype("Inter-Bold.ttf", 32)
font_body     = ImageFont.truetype("Inter-Regular.ttf", 26)

# Text positioning (above Meta's bottom safe zone)
y_start = 900

# Headline
draw.text((60, y_start), "CA telehealth company", font=font_headline, fill="white")
draw.text((60, y_start + 72), "hiring psychiatrists.", font=font_accent, fill="#00E5CC")

# Subline
draw.text((60, y_start + 160), "Remote · Contract · Flexible hours",
          font=font_sub, fill="#00E5CC")

# Body
draw.text((60, y_start + 210), "Fully remote, set your own schedule",
          font=font_body, fill=(255, 255, 255, 200))

bg.save("banner_final.png")
```

### Method C: Node.js with Sharp + SVG Overlay

```javascript
const sharp = require('sharp');

const svgOverlay = `
<svg width="1080" height="1350" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="fade" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0.3" stop-color="black" stop-opacity="0"/>
      <stop offset="1"   stop-color="black" stop-opacity="0.8"/>
    </linearGradient>
  </defs>
  <rect width="1080" height="1350" fill="url(#fade)"/>
  <text x="60" y="960"  font-family="Inter,Helvetica,sans-serif"
        font-size="64" font-weight="900" fill="white">
    CA telehealth company
  </text>
  <text x="60" y="1032" font-family="Inter,Helvetica,sans-serif"
        font-size="64" font-weight="900" fill="#00E5CC">
    hiring psychiatrists.
  </text>
  <text x="60" y="1100" font-family="Inter,Helvetica,sans-serif"
        font-size="32" font-weight="700" fill="#00E5CC"
        letter-spacing="2">
    Remote · Contract · Flexible hours
  </text>
  <text x="60" y="1150" font-family="Inter,Helvetica,sans-serif"
        font-size="26" fill="white" opacity="0.85"
        font-style="italic">
    Fully remote, set your own schedule
  </text>
</svg>`;

sharp('background.jpg')
  .resize(1080, 1350)
  .composite([{ input: Buffer.from(svgOverlay), top: 0, left: 0 }])
  .toFile('banner_final.png');
```

---

## Step 4: Typography Rules

Follow these rules for every banner. No exceptions.

### Font Selection

| Element    | Weight    | Size (1080px canvas) | Mobile equivalent |
|------------|-----------|----------------------|-------------------|
| Headline   | Black/900 | 56–72px              | ~20–26px on phone |
| Subheadline| Bold/700  | 28–36px              | ~10–13px on phone |
| Body text  | Regular   | 22–28px              | ~8–10px on phone  |
| CTA / Tag  | Bold/700  | 32–40px              | ~12–14px on phone |

### Font Families (in order of preference)

For ads, use these clean, high-readability sans-serif fonts:
1. **Inter** — designed for screens, excellent at small sizes
2. **Montserrat** — modern, geometric, strong for headlines
3. **DM Sans** — friendly, clean, works well for both headline and body
4. **Plus Jakarta Sans** — contemporary, distinctive
5. **Manrope** — geometric, techy feel

**NEVER use:** decorative fonts, script fonts, thin/light weights,
all-caps body text, or more than 2 font families per banner.

### Text Readability Checklist

Before finalizing any banner, verify:

- [ ] **Maximum 6–8 words per text block** — users spend 0.9 seconds on an ad
- [ ] **Contrast ratio ≥ 4.5:1** between text and its immediate background
- [ ] **Dark overlay or solid panel** behind text if placed over a photo
- [ ] **No text in the top 14% or bottom 14%** of the canvas (Meta UI zones)
- [ ] **No text smaller than 22px** on a 1080px-wide canvas
- [ ] **One clear hierarchy:** headline (big) → subline (medium) → body (small)
- [ ] **Accent color on the key phrase** — the one thing you want them to read

### Color Rules for Text

- White text on dark overlay = safest, always readable
- Avoid pure white (#FFFFFF) for body text — use #F0F0F0 or 85% opacity
- Use ONE accent color for the most important phrase (CTA or key benefit)
- Accent color must have ≥ 60% saturation and ≥ 70% lightness
- Good accent colors: #00E5CC (teal), #FFD60A (gold), #FF6B6B (coral),
  #A78BFA (violet), #34D399 (green)

---

## Step 5: Design Patterns That Convert

### Pattern 1: Photo + Bottom Text Block (Highest CTR for services)

```
┌─────────────────────┐
│                     │
│   [Photo/Visual]    │   ← 60% of canvas: AI-generated photo
│                     │
│ ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓│   ← gradient overlay starts here
│                     │
│  HEADLINE TEXT      │   ← bottom 30%: text content
│  Accent subline     │
│  Body text          │
│                     │   ← 180px bottom padding (Meta CTA zone)
└─────────────────────┘
```

Best for: hiring ads, service promotions, real estate, healthcare.

### Pattern 2: Split Screen (High CTR for products)

```
┌─────────────────────┐
│          │          │
│  [Text   │ [Product │
│   side]  │  image]  │
│          │          │
│ Headline │          │
│ Subline  │          │
│ CTA text │          │
│          │          │
└─────────────────────┘
```

Best for: e-commerce, app ads, SaaS, physical products.

### Pattern 3: Bold Text on Solid/Gradient (High CTR for announcements)

```
┌─────────────────────┐
│                     │
│  ████████████████   │   ← solid color or gradient background
│                     │
│   BIG HEADLINE      │   ← centered, large text
│   supporting line   │
│                     │
│   [Small product    │
│    image or icon]   │
│                     │
└─────────────────────┘
```

Best for: sales, announcements, events, offers with % discount.

### Pattern 4: Stories/Reels Vertical (9:16)

```
┌───────────┐
│  ░░░░░░░  │  ← 250px safe zone (Meta UI)
│           │
│  [Visual  │
│   takes   │
│   full    │
│   screen] │
│           │
│ HEADLINE  │  ← text in center-bottom area
│ Subline   │
│           │
│  ░░░░░░░  │  ← 340px safe zone (CTA button)
└───────────┘
```

---

## Step 6: Batch Production Workflow

When the user needs multiple banner variants:

### A. Same message, different visuals:
1. Generate 3–5 different background images with varied compositions
2. Apply the SAME text overlay template to each
3. Export all variants for A/B testing

### B. Same visual, different messages:
1. Generate 1 strong background image
2. Create 3–5 text overlay variants (different headlines, CTAs)
3. Export all for copy testing

### C. Multi-format adaptation:
1. Generate one background at highest resolution (1080×1920 for Stories)
2. Crop/recompose for each format:
   - 1080×1080 (1:1 Feed)
   - 1080×1350 (4:5 Feed)
   - 1080×1920 (9:16 Stories)
3. Adjust text position per format (safe zones differ!)

---

## Step 7: Export and Delivery

### File Requirements for Meta Ads

- **Format:** PNG or JPG (PNG preferred for text-heavy creatives)
- **Max file size:** 30MB (aim for under 5MB for fast loading)
- **Color space:** sRGB
- **Resolution:** Exactly match target dimensions (no upscaling)

### Quality Checklist Before Delivery

- [ ] Text is 100% readable at 375px width (simulate phone screen)
- [ ] No text in Meta safe zones (top/bottom)
- [ ] No more than 20% of the image area is text (Meta penalizes text-heavy ads)
- [ ] Accent color draws eye to the key message
- [ ] Image is not stretched or pixelated
- [ ] File size is under 5MB
- [ ] Exported at exact target dimensions

### The 20% Text Rule

Meta no longer hard-blocks ads with >20% text, but the algorithm
**deprioritizes them**, resulting in higher CPM and lower reach.
Keep text overlay minimal. If you need more copy, put it in the
ad's Primary Text field, not on the image.

---

## Common Mistakes to Avoid

| Mistake | Why It Fails | Fix |
|---------|-------------|-----|
| Text baked into AI image | Garbled, misspelled, overlapping | Use two-layer method |
| Text over busy photo | Unreadable | Add gradient overlay or solid panel |
| Too much text on image | Meta penalizes, users skip | Max 6–8 words on image |
| Thin/light font weight | Invisible on mobile | Use Bold (700) or Black (900) |
| Decorative/script fonts | Unreadable at small sizes | Stick to sans-serif |
| Text in safe zones | Hidden by Meta UI | Keep text in center 60% |
| No visual hierarchy | Everything competes for attention | One big headline, one accent |
| Generic stock-photo look | Users scroll past | Use AI-generated or branded imagery |
| Same creative for all placements | Poor fit, wasted space | Adapt per format |
| No contrast between text and bg | Text disappears | Overlay, panel, or text shadow |

---

## Quick Start Prompts

When the user says "make me a banner," follow this exact sequence:

1. **Ask** (if not provided): What product/service? What's the key message?
   What format — Feed, Stories, or both?

2. **Generate background:**
   ```
   [Relevant scene], [good lighting], photorealistic,
   clean composition with negative space for text placement,
   no text no words no letters no typography no watermarks
   ```

3. **Build text overlay** using HTML/CSS Method A (preferred) or Python Method B

4. **Composite** background + text overlay into final PNG

5. **Verify** against the quality checklist

6. **Deliver** the final file(s) to the user

---

## Example: Complete Banner Creation

**User request:** "Create a hiring ad for a CA telehealth company
looking for psychiatrists. Remote, contract, flexible hours."

**Step 1 — Background prompt:**
```
Professional male doctor in white coat at modern desk, laptop open
with video call on screen, bright natural light from large window,
clean contemporary medical office, green plant in background, warm
and inviting atmosphere, editorial photography style, shallow depth
of field, shot from slight angle, 4:5 vertical composition,
no text no words no letters no typography no watermarks
```

**Step 2 — Text overlay (HTML method):**
- Headline: "CA telehealth company **hiring psychiatrists.**"
- Subline: "Remote · Contract · Flexible hours"
- Body: "Fully remote, set your own schedule"
- Accent color: #00E5CC on "hiring psychiatrists."
- Dark gradient overlay on bottom 50% of image
- Text positioned at y=68% of canvas height, x-padding=60px

**Step 3 — Export:** 1080×1350 PNG, sRGB, <3MB

Done. Clean, readable, professional.
