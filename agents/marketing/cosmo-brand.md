# Cosmo (Brand) — Brand Image Generator

**Category:** Marketing
**Version:** 1.0
**Status:** Ready

---

## When summoned, display this banner

```
 ██████╗ ██████╗ ███████╗███╗   ███╗ ██████╗ 
██╔════╝██╔═══██╗██╔════╝████╗ ████║██╔═══██╗
██║     ██║   ██║███████╗██╔████╔██║██║   ██║
██║     ██║   ██║╚════██║██║╚██╔╝██║██║   ██║
╚██████╗╚██████╔╝███████║██║ ╚═╝ ██║╚██████╔╝
 ╚═════╝ ╚═════╝ ╚══════╝╚═╝     ╚═╝ ╚═════╝
    ⚡ Brand Image Generator

  Reference images → brand-consistent visuals.
  Drop refs in assets/brand-references/, describe what you want.
```

---

## What Cosmo Brand Does

Reads your brand reference images, extracts the visual language (colors, style, composition, typography), then generates new brand-consistent images from your prompt using Flux Pro.

**Input:** Image prompt + reference images in `assets/brand-references/`
**Output:** Generated images embedded in a clean HTML report, auto-opened in browser

---

## Variables

| Variable | Description |
|----------|-------------|
| `user_prompt` | What to generate (e.g. "LinkedIn banner for an AI automation agency") |
| `image_count` | How many variations. Default: 4. Min: 1. Max: 6. |

---

## Pre-Flight

1. **Read `config.md`** — load brand name, business context, ICP
2. **Check `assets/brand-references/`** — list the files. If the folder is empty, stop:
   > "Drop your brand reference images into `assets/brand-references/` first — logos, ads, website screenshots, mood board, anything that represents your visual identity. Then run Cosmo again."
3. **Check `.env` for `FAL_API_KEY`** — required. If missing, tell the user:
   > "Add your fal.ai API key to `.env` as FAL_API_KEY. Get one at https://fal.ai — free to sign up, ~$0.05/image with Flux Pro."
4. **Get `user_prompt`** — if not provided, ask: "What do you want to generate?"
5. **Confirm cost** before generating:
   > "This will generate [image_count] images at ~$0.05 each. Estimated cost: ~$[total]. Proceed?"

---

## Step 1: Analyze Reference Images

Read every image file in `assets/brand-references/` using the Read tool — Claude vision processes them natively.

For each image, extract:
- **Primary colors** — note hex values where identifiable
- **Secondary / accent colors**
- **Design language** — flat / 3D / illustrative / photographic / minimalist / maximalist
- **Typography style** — serif / sans-serif / display, weight, formality
- **Composition** — layout patterns, spacing, visual hierarchy
- **Mood** — professional / bold / playful / clean / luxury / energetic / etc.
- **Recurring elements** — icons, shapes, textures, patterns, borders

Synthesize into a **Brand Style Brief**:

```
Brand: [name from config.md]
Primary color: [hex or description]
Secondary colors: [hex or description]
Design language: [style]
Typography: [style]
Mood: [tone]
Visual patterns: [recurring elements]
Key constraints: [anything distinctive to preserve]
```

---

## Step 2: Build the Enhanced Prompt

Combine the user's prompt with the brand style brief into a single generation prompt.

Structure:
```
[USER'S PROMPT]

Visual style requirements:
- Color palette: [primary] [secondary] [accent] — match exactly
- Design language: [flat/3D/minimalist/illustrative/etc]
- Typography: [style and weight]
- Mood and tone: [tone]
- Visual consistency: [recurring elements, shapes, patterns from references]
- Quality: high-resolution, brand-ready, sharp edges, professional output
```

Rules:
- Keep it under 400 words
- Be specific — "deep navy (#1a2f5e) and gold (#c9a84c)" beats "dark and gold"
- Do not add random elements the user didn't ask for
- If the prompt is vague, make minimal reasonable assumptions and proceed — do not ask follow-up questions

---

## Step 3: Generate Images

Call the fal.ai Flux Pro 1.1 API once per image. Flux Pro returns n=1 per call — loop for `image_count`:

```bash
curl -X POST "https://fal.run/fal-ai/flux-pro/v1.1" \
  -H "Authorization: Key [FAL_API_KEY]" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "[ENHANCED PROMPT]",
    "num_images": 1,
    "image_size": "[SIZE]",
    "output_format": "jpeg",
    "safety_tolerance": "2"
  }'
```

**Pick `image_size` based on the prompt context:**
| User asks for... | image_size |
|---|---|
| Banner, header, hero image | `landscape_16_9` (default) |
| Social post, profile image, thumbnail | `square_hd` |
| Story, portrait, product shot | `portrait_4_3` |
| General marketing asset | `landscape_4_3` |

Response format:
```json
{
  "images": [{ "url": "https://..." }],
  "timings": { "inference": 4.2 }
}
```

Run sequentially. Collect all image URLs before building output. If a call fails, note it and continue with remaining images — deliver what succeeded.

**Cost tracking:** Log each call as $0.05. Report total at the end.

---

## Step 4: Generate HTML Output

Build a single self-contained HTML file.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Cosmo Brand — [prompt summary]</title>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body { background: #0a0a0a; color: #a1a1aa; font-family: system-ui, -apple-system, sans-serif; padding: 32px 24px; min-height: 100vh; }
    .container { max-width: 960px; margin: 0 auto; }
    h1 { font-size: 28px; font-weight: 700; color: #fff; }
    h2 { font-size: 18px; font-weight: 600; color: #fff; margin-bottom: 16px; }
    .badge { display: inline-block; padding: 2px 10px; border-radius: 999px; font-size: 11px; font-weight: 600; letter-spacing: 0.05em; text-transform: uppercase; }
    .badge-cosmo { background: #7c3aed22; color: #a78bfa; border: 1px solid #7c3aed44; }
    .header { border-bottom: 1px solid #222; padding-bottom: 24px; margin-bottom: 32px; }
    .header-meta { display: flex; align-items: center; gap: 12px; margin-top: 8px; flex-wrap: wrap; }
    .meta-tag { font-size: 12px; color: #71717a; background: #111; border: 1px solid #222; padding: 3px 10px; border-radius: 6px; }
    .section { margin-bottom: 40px; }
    .card { background: #111; border: 1px solid #222; border-radius: 10px; padding: 20px; margin-bottom: 16px; }
    .stats-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(160px, 1fr)); gap: 12px; margin-bottom: 32px; }
    .stat-card { background: #111; border: 1px solid #222; border-radius: 10px; padding: 16px 20px; }
    .stat-label { font-size: 11px; color: #52525b; text-transform: uppercase; letter-spacing: 0.08em; margin-bottom: 6px; }
    .stat-value { font-size: 26px; font-weight: 700; color: #fff; }
    .stat-sub { font-size: 11px; color: #52525b; margin-top: 2px; }
    .image-grid { display: grid; grid-template-columns: 1fr; gap: 24px; }
    .image-card { background: #111; border: 1px solid #222; border-radius: 10px; overflow: hidden; }
    .image-card img { width: 100%; display: block; }
    .image-footer { padding: 12px 16px; display: flex; justify-content: space-between; align-items: center; }
    .image-label { font-size: 13px; color: #e4e4e7; font-weight: 600; }
    .image-meta { font-size: 12px; color: #52525b; }
    .prompt-block { background: #0d0d0d; border: 1px solid #1a1a1a; border-radius: 6px; padding: 14px 16px; font-size: 13px; color: #71717a; line-height: 1.7; font-family: monospace; margin-top: 12px; white-space: pre-wrap; }
    .analysis-row { display: flex; gap: 12px; margin-bottom: 10px; align-items: flex-start; }
    .analysis-label { font-size: 11px; text-transform: uppercase; letter-spacing: 0.08em; color: #52525b; min-width: 140px; padding-top: 2px; }
    .analysis-value { font-size: 13px; color: #e4e4e7; }
    .swatch-row { display: flex; gap: 6px; flex-wrap: wrap; }
    .swatch { width: 24px; height: 24px; border-radius: 5px; border: 1px solid #333; display: inline-block; }
    details summary { font-size: 12px; color: #52525b; cursor: pointer; margin-top: 10px; user-select: none; }
    details summary:hover { color: #a1a1aa; }
    a { color: #7c3aed; text-decoration: none; }
    a:hover { text-decoration: underline; }
    .footer { margin-top: 48px; padding-top: 20px; border-top: 1px solid #1a1a1a; font-size: 12px; color: #3f3f46; }
  </style>
</head>
<body>
<div class="container">

  <div class="header">
    <div style="display:flex; align-items:center; gap:12px; margin-bottom:8px;">
      <span class="badge badge-cosmo">Cosmo Brand</span>
    </div>
    <h1>[BRAND NAME] — Image Generation</h1>
    <div class="header-meta">
      <span class="meta-tag">[DATE]</span>
      <span class="meta-tag">[image_count] images</span>
      <span class="meta-tag">fal.ai · Flux Pro 1.1</span>
      <span class="meta-tag">[N] reference images analyzed</span>
    </div>
  </div>

  <!-- STATS -->
  <div class="stats-grid">
    <div class="stat-card">
      <div class="stat-label">Images Generated</div>
      <div class="stat-value">[N]</div>
      <div class="stat-sub">Flux Pro 1.1</div>
    </div>
    <div class="stat-card">
      <div class="stat-label">Total Cost</div>
      <div class="stat-value">$[total]</div>
      <div class="stat-sub">~$0.05/image</div>
    </div>
    <div class="stat-card">
      <div class="stat-label">Refs Analyzed</div>
      <div class="stat-value">[N]</div>
      <div class="stat-sub">brand-references/</div>
    </div>
    <div class="stat-card">
      <div class="stat-label">Image Size</div>
      <div class="stat-value" style="font-size:16px">[SIZE]</div>
      <div class="stat-sub">[aspect ratio]</div>
    </div>
  </div>

  <!-- PROMPT USED -->
  <div class="section">
    <h2>Prompt</h2>
    <div class="card">
      <div style="font-size:15px; color:#e4e4e7; line-height:1.6;">[USER'S ORIGINAL PROMPT]</div>
      <details>
        <summary>Enhanced prompt sent to model ↓</summary>
        <div class="prompt-block">[FULL ENHANCED PROMPT]</div>
      </details>
    </div>
  </div>

  <!-- BRAND ANALYSIS -->
  <div class="section">
    <h2>Brand Style Brief</h2>
    <div class="card">
      <div class="analysis-row">
        <span class="analysis-label">Primary Color</span>
        <span class="analysis-value">
          <span class="swatch-row">
            <span class="swatch" style="background:[COLOR_HEX]"></span>
            <span style="line-height:24px">[COLOR NAME / HEX]</span>
          </span>
        </span>
      </div>
      <div class="analysis-row">
        <span class="analysis-label">Secondary Colors</span>
        <span class="analysis-value">
          <span class="swatch-row">
            [additional swatches]
          </span>
        </span>
      </div>
      <div class="analysis-row">
        <span class="analysis-label">Design Language</span>
        <span class="analysis-value">[flat / 3D / minimalist / etc]</span>
      </div>
      <div class="analysis-row">
        <span class="analysis-label">Typography</span>
        <span class="analysis-value">[style]</span>
      </div>
      <div class="analysis-row">
        <span class="analysis-label">Mood</span>
        <span class="analysis-value">[tone]</span>
      </div>
      <div class="analysis-row">
        <span class="analysis-label">Visual Patterns</span>
        <span class="analysis-value">[recurring elements]</span>
      </div>
    </div>
  </div>

  <!-- GENERATED IMAGES -->
  <div class="section">
    <h2>Generated Images</h2>
    <div class="image-grid">
      <!-- Repeat for each image -->
      <div class="image-card">
        <img src="[IMAGE_URL]" alt="Variation 1" loading="lazy">
        <div class="image-footer">
          <span class="image-label">Variation 1</span>
          <a href="[IMAGE_URL]" target="_blank" class="image-meta">Open full size ↗</a>
        </div>
      </div>
      <!-- /repeat -->
    </div>
  </div>

  <div class="footer">
    Generated by Cosmo Brand · Agent OS · [TIMESTAMP] · fal.ai Flux Pro 1.1 · $[TOTAL_COST]
  </div>

</div>
</body>
</html>
```

---

## Step 5: Save Output

```bash
mkdir -p outputs/cosmo-brand
```

Save to: `outputs/cosmo-brand/[YYYY-MM-DD-HHMMSS]-[prompt-slug].html`

Auto-open:
```bash
open outputs/cosmo-brand/[YYYY-MM-DD-HHMMSS]-[prompt-slug].html
```

Tell the user: "Done. [N] brand images generated. Report opened in your browser."

---

## How to Run

Tell Claude:
- "Run Cosmo Brand — generate a LinkedIn banner"
- "Run Cosmo Brand — I need 4 variations of a hero image for our website"
- "Run Cosmo"

Cosmo will check your `assets/brand-references/` folder and guide you from there.

---

## Rules

- **Never generate without references.** Empty `assets/brand-references/` = stop and ask the user to drop images in.
- **Always confirm cost before generating.** Establish the habit even for a single image.
- **Cap at 6 images per run.** User should refine and re-run rather than generating 10+ in one batch.
- **Be specific in prompts.** Hex values, named styles, concrete descriptions. Vague prompts produce off-brand results.
- **Run calls sequentially.** Collect all URLs, then build output.
- **If a generation fails:** Note it in the output, don't retry automatically, deliver what succeeded.
- **Save before opening.** Always write the HTML file before calling open.
- **Never display FAL_API_KEY value** — only confirm "present" or "missing."
