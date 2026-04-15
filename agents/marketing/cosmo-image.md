# Cosmo (Character) — Character Image Generator

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
    ⚡ Character Image Generator

  Reference photos → consistent personal brand images.
  Drop your photos in assets/character-references/, describe what you want.
```

---

## What Cosmo Character Does

Reads your reference photos, identifies the primary reference image (clear face, best lighting), uploads it to fal.ai, then generates new consistent character images using `fal-ai/consistent-character` — a model specifically built for identity-consistent generation from a reference photo.

**Why not just prompt Flux?** Flux Pro with a described face produces inconsistent results. `consistent-character` takes your actual reference photo as a conditioning input — same face, same proportions, every time.

**Input:** Image prompt + reference photos in `assets/character-references/`
**Output:** Generated images embedded in a clean HTML report, auto-opened in browser

---

## Variables

| Variable | Description |
|----------|-------------|
| `user_prompt` | What to generate (e.g. "professional headshot on white background, neutral expression") |
| `image_count` | How many variations. Default: 4. Min: 1. Max: 6. |

---

## Pre-Flight

1. **Read `config.md`** — load name, business context
2. **Check `assets/character-references/`** — list the files. If empty, stop:
   > "Drop your reference photos into `assets/character-references/` first — clear headshots, good lighting, face forward. The better the reference, the more consistent the output. Then run Cosmo Character again."
3. **Check `.env` for `FAL_API_KEY`** — required. If missing:
   > "Add your fal.ai API key to `.env` as FAL_API_KEY. Get one at https://fal.ai — ~$0.08/image with consistent-character model."
4. **Get `user_prompt`** — if not provided, ask: "What do you want to generate? (e.g. 'professional headshot, navy suit, white background' or 'casual outdoor photo, golden hour lighting')"
5. **Confirm cost** before generating:
   > "This will generate [image_count] images at ~$0.08 each. Estimated cost: ~$[total]. Proceed?"

---

## Step 1: Select the Best Reference Image

Read all images from `assets/character-references/` using the Read tool — Claude vision processes them natively.

Evaluate each image for reference quality:
- **Face clarity** — is the face clearly visible and in focus?
- **Lighting** — even lighting, no harsh shadows obscuring features
- **Angle** — front-facing or 3/4 view preferred over profile
- **Resolution** — higher res = better conditioning
- **Expressions** — neutral or natural preferred (extreme expressions can bias output)

Pick the **single best reference image** for upload. Note which file you selected and why.

Also extract a **Character Profile** from all reference images combined:
- **Face shape** — oval / round / square / heart / etc.
- **Hair** — color, length, texture, style
- **Eye color**
- **Skin tone**
- **Distinguishing features** — facial hair, freckles, glasses, notable features
- **Build** — where visible
- **Style** — casual / professional / streetwear / etc.
- **Age range** — approximate

---

## Step 2: Upload Reference Image to fal.ai

The `consistent-character` model requires a URL — local file paths don't work. Upload the selected reference image to fal.ai storage:

```bash
curl -X POST "https://fal.run/files/upload" \
  -H "Authorization: Key [FAL_API_KEY]" \
  -H "Content-Type: image/jpeg" \
  --data-binary @"assets/character-references/[SELECTED_FILENAME]"
```

Response:
```json
{
  "url": "https://storage.googleapis.com/..."
}
```

Save the returned URL — this is `[REFERENCE_URL]` used in Step 3.

**Content-Type note:** Use `image/jpeg` for .jpg/.jpeg files, `image/png` for .png files, `image/webp` for .webp files.

If upload fails:
> "Reference image upload failed — check your FAL_API_KEY and that the file is a valid JPG, PNG, or WebP image."

---

## Step 3: Build the Enhanced Prompt

Combine the user's prompt with the character profile from Step 1.

Structure:
```
[USER'S PROMPT]

Character details to preserve:
- Face: [face shape, structure]
- Hair: [color, length, style]
- Eyes: [color]
- Skin tone: [description]
- Distinguishing features: [any notable features]
- Lighting: [derive from prompt or default to: soft natural light, even exposure]
- Camera feel: [derive from prompt or default to: 35mm lens, shallow depth of field]
- Quality: photorealistic, high resolution, sharp focus on face, professional photography
```

Rules:
- Do NOT describe the person so specifically that the model fights the reference — the reference photo handles identity, the prompt handles setting/pose/mood
- Keep clothing, background, and pose instructions clear and specific
- If the user prompt is vague, make minimal reasonable assumptions — do not ask follow-up questions
- Cap at 300 words — shorter prompts with good references outperform long prompts

---

## Step 4: Generate Images

Call `fal-ai/consistent-character` once per batch. This model supports `num_images` directly (no need to loop):

```bash
curl -X POST "https://fal.run/fal-ai/consistent-character" \
  -H "Authorization: Key [FAL_API_KEY]" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "[ENHANCED PROMPT]",
    "image_url": "[REFERENCE_URL]",
    "num_images": [image_count]
  }'
```

Response format:
```json
{
  "images": [
    { "url": "https://..." },
    { "url": "https://..." }
  ]
}
```

If `consistent-character` is unavailable or returns an error, fall back to Flux Pro with the full character description prompt:

```bash
curl -X POST "https://fal.run/fal-ai/flux-pro/v1.1" \
  -H "Authorization: Key [FAL_API_KEY]" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "[ENHANCED PROMPT — include full character description]",
    "num_images": 1,
    "image_size": "[SIZE]",
    "output_format": "jpeg"
  }'
```

Note in the output if fallback was used.

**Image size guidance:**
| User asks for... | image_size |
|---|---|
| Headshot, profile photo | `portrait_4_3` (default) |
| Full-body shot | `portrait_16_9` |
| Social square | `square_hd` |
| Banner, wide shot | `landscape_16_9` |

**Cost tracking:** `consistent-character` is ~$0.08/image. Log actual cost from the response if available.

---

## Step 5: Generate HTML Output

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Cosmo Character — [prompt summary]</title>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body { background: #0a0a0a; color: #a1a1aa; font-family: system-ui, -apple-system, sans-serif; padding: 32px 24px; min-height: 100vh; }
    .container { max-width: 960px; margin: 0 auto; }
    h1 { font-size: 28px; font-weight: 700; color: #fff; }
    h2 { font-size: 18px; font-weight: 600; color: #fff; margin-bottom: 16px; }
    .badge { display: inline-block; padding: 2px 10px; border-radius: 999px; font-size: 11px; font-weight: 600; letter-spacing: 0.05em; text-transform: uppercase; }
    .badge-cosmo { background: #7c3aed22; color: #a78bfa; border: 1px solid #7c3aed44; }
    .badge-model { background: #0e7490; color: #67e8f9; border: 1px solid #0e749044; padding: 2px 10px; border-radius: 999px; font-size: 11px; font-weight: 600; }
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
    .ref-row { display: flex; gap: 16px; flex-wrap: wrap; margin-bottom: 8px; }
    .ref-thumb { width: 80px; height: 80px; object-fit: cover; border-radius: 8px; border: 2px solid #7c3aed44; }
    .ref-thumb.selected { border-color: #7c3aed; }
    .ref-label { font-size: 11px; color: #52525b; margin-top: 4px; text-align: center; }
    .image-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(420px, 1fr)); gap: 20px; }
    .image-card { background: #111; border: 1px solid #222; border-radius: 10px; overflow: hidden; }
    .image-card img { width: 100%; display: block; }
    .image-footer { padding: 12px 16px; display: flex; justify-content: space-between; align-items: center; }
    .image-label { font-size: 13px; color: #e4e4e7; font-weight: 600; }
    .prompt-block { background: #0d0d0d; border: 1px solid #1a1a1a; border-radius: 6px; padding: 14px 16px; font-size: 13px; color: #71717a; line-height: 1.7; font-family: monospace; margin-top: 12px; white-space: pre-wrap; }
    .profile-row { display: flex; gap: 12px; margin-bottom: 8px; align-items: flex-start; }
    .profile-label { font-size: 11px; text-transform: uppercase; letter-spacing: 0.08em; color: #52525b; min-width: 140px; padding-top: 2px; }
    .profile-value { font-size: 13px; color: #e4e4e7; }
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
      <span class="badge badge-cosmo">Cosmo Character</span>
      <span class="badge-model">consistent-character</span>
    </div>
    <h1>[NAME] — Character Images</h1>
    <div class="header-meta">
      <span class="meta-tag">[DATE]</span>
      <span class="meta-tag">[image_count] images</span>
      <span class="meta-tag">fal.ai · consistent-character</span>
      <span class="meta-tag">[N] reference photos loaded</span>
    </div>
  </div>

  <!-- STATS -->
  <div class="stats-grid">
    <div class="stat-card">
      <div class="stat-label">Images Generated</div>
      <div class="stat-value">[N]</div>
      <div class="stat-sub">consistent-character</div>
    </div>
    <div class="stat-card">
      <div class="stat-label">Total Cost</div>
      <div class="stat-value">$[total]</div>
      <div class="stat-sub">~$0.08/image</div>
    </div>
    <div class="stat-card">
      <div class="stat-label">Reference Used</div>
      <div class="stat-value" style="font-size:14px; padding-top:4px">[FILENAME]</div>
      <div class="stat-sub">primary reference</div>
    </div>
    <div class="stat-card">
      <div class="stat-label">Image Size</div>
      <div class="stat-value" style="font-size:16px">[SIZE]</div>
      <div class="stat-sub">[aspect ratio]</div>
    </div>
  </div>

  <!-- REFERENCE IMAGES -->
  <div class="section">
    <h2>Reference Photos</h2>
    <div class="card">
      <div class="ref-row">
        [For each ref image: <img class="ref-thumb [selected if primary]" src="[local path or fal URL]"> with label below — mark primary with "★ Primary reference" label]
      </div>
      <p style="font-size:12px; color:#52525b; margin-top:12px;">Primary reference uploaded to fal.ai for conditioning. Identity consistency comes from the reference photo — not just the prompt.</p>
    </div>
  </div>

  <!-- CHARACTER PROFILE -->
  <div class="section">
    <h2>Character Profile</h2>
    <div class="card">
      [profile rows: hair, eyes, skin tone, build, style, distinguishing features]
      <details>
        <summary>Enhanced prompt sent to model ↓</summary>
        <div class="prompt-block">[FULL ENHANCED PROMPT]</div>
      </details>
    </div>
  </div>

  <!-- GENERATED IMAGES -->
  <div class="section">
    <h2>Generated Images</h2>
    <div class="image-grid">
      [For each image:]
      <div class="image-card">
        <img src="[IMAGE_URL]" alt="Variation N" loading="lazy">
        <div class="image-footer">
          <span class="image-label">Variation [N]</span>
          <a href="[IMAGE_URL]" target="_blank" style="font-size:12px; color:#52525b;">Open full size ↗</a>
        </div>
      </div>
    </div>
  </div>

  <div class="footer">
    Generated by Cosmo Character · Agent OS · [TIMESTAMP] · fal.ai consistent-character · $[TOTAL_COST]
  </div>

</div>
</body>
</html>
```

---

## Step 6: Save Output

```bash
mkdir -p outputs/cosmo-character
```

Save to: `outputs/cosmo-character/[YYYY-MM-DD-HHMMSS]-[prompt-slug].html`

Auto-open:
```bash
open outputs/cosmo-character/[YYYY-MM-DD-HHMMSS]-[prompt-slug].html
```

Tell the user: "Done. [N] character images generated. Report opened in your browser."

---

## How to Run

Tell Claude:
- "Run Cosmo Character — professional headshot, white background"
- "Run Cosmo Character — 4 variations, casual outdoor setting, golden hour"
- "Run Cosmo" (if character-references/ has images, Cosmo will default to character mode)

---

## Rules

- **Never generate without references.** Empty `assets/character-references/` = stop and ask.
- **Always upload the reference before generating.** The model requires a URL — local paths won't work.
- **Pick the best single reference.** The clearest face photo wins over quantity. More refs ≠ better results.
- **Reference handles identity, prompt handles everything else.** Don't over-describe the face in the prompt — trust the reference conditioning.
- **Always confirm cost before generating.** ~$0.08/image × 6 = $0.48 max per run.
- **If consistent-character fails**, fall back to Flux Pro with full character description in prompt — note the fallback in output.
- **Cap at 6 images per run.** Refine and re-run rather than generating 10+ in one batch.
- **Save before opening.** Always write HTML file before calling open.
- **Never display FAL_API_KEY value** — only confirm "present" or "missing."
