---
name: ccskill-gptimage
description: |
  Skill for image generation needs.
  Generate and edit high-quality images via OpenAI gpt-image-2 (ChatGPT Images 2.0).
  Strong at multilingual text rendering (Japanese included), complex instruction following,
  reference-image fidelity, transparent PNG, and complex infographics.
---

# ccskill-gptimage Image Generation Skill

## Overview

This skill generates and edits images via OpenAI gpt-image-2. **The user only describes intent** — Claude composes the structured prompt, style hints and cost-optimized parameters from conversation history and project context. Sister skill: `ccskill-nanobanana` (Gemini 3 Pro Image); use them based on use case.

## Prerequisites

- Set `CCSKILL_GPTIMAGE_DIR` to this skill's repository path:
  ```bash
  export CCSKILL_GPTIMAGE_DIR="$HOME/projects/ccskill-gptimage"
  ```
- `OPENAI_API_KEY` must be set (or written into `$CCSKILL_GPTIMAGE_DIR/.env`)
- **The OpenAI Organization must be Verified** (unverified orgs get 403)

## Usage

```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py "prompt"
```

### Options

| Option | Description | Default | Values |
|---|---|---|---|
| `--size` | Output size | `1024x1024` | `auto` / `1024x1024` / `1024x1536` (portrait) / `1536x1024` (landscape) |
| `--quality` | Quality | `auto` | `auto` / `low` / `medium` / `high` |
| `--background` | Background | `auto` | `auto` / `opaque` (use `--model gpt-image-1.5` for `transparent`) |
| `--output-format` | Output format | `png` | `png` / `jpeg` / `webp` |
| `--output-compression` | Compression (jpeg/webp) | none | 0-100 |
| `--output` | Output directory | `./generated_images` | any path |
| `--output-name` | Output filename stem (extension auto from format) | timestamp | any string |
| `--reference` | Reference image (repeatable) | none | image file path |
| `--mask` | Mask image (transparent area is editable; requires `--reference`) | none | image file path |
| `--input-fidelity` | Not needed for gpt-image-2 (always max fidelity). For `gpt-image-1.5` | none | `high` / `low` |
| `--moderation` | Moderation level | `auto` | `auto` / `low` |
| `--model` | Model ID | `gpt-image-2` | any model ID |

### Examples

#### Basic
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "A children's book illustration of a veterinarian listening to a baby otter's heartbeat"
```

#### Japanese poster
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "A minimalist editorial poster with the exact title \"腹落ちDMARC\" in large serif Japanese font at the top, dark navy background, subtle terminal motif" \
  --size 1024x1536 --quality high
```

#### When you need transparency

gpt-image-2 has no transparent background support. Pick one of:

1. **Post-process with `rembg`** (recommended — keeps gpt-image-2's text rendering and layout strengths)
   ```bash
   $CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py "minimalist fox logo on plain white background"
   rembg i generated_images/<image>.png generated_images/<image>_alpha.png
   ```
2. **Switch to `gpt-image-1.5`** (the older model that still supports transparency)
   ```bash
   $CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
     "minimalist fox logo, flat vector, navy and gold" \
     --model gpt-image-1.5 --background transparent --output-format png
   ```
3. **Use the sister skill `ccskill-nanobanana`**

#### Reference editing
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Place the same fox logo on a deep navy background with subtle gold sparkles, ready for use as a hero image. Preserve the fox's pose and proportions from the reference." \
  --reference ./logo.png --quality medium
```

> **About reference fidelity**: gpt-image-2 always processes input images at maximum fidelity automatically — `input_fidelity` is unnecessary (specifying it returns 400). This is a feature, not a missing parameter: reference preservation is in fact strong. Just spell out what to "Preserve … from the reference" in the prompt. The trade-off: input-image tokens add up when editing, so **don't pass unnecessary references**.

#### Multi-reference composition
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Photorealistic gift basket on white, labeled 'Relax & Unwind', containing all items" \
  --reference ./body-lotion.png --reference ./bath-bomb.png --reference ./soap.png
```

#### Mask inpainting
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "A sunlit indoor lounge area with a pool containing a flamingo" \
  --reference ./lounge.png --mask ./mask.png
```

> The transparent area of the mask is what gets replaced. The prompt must describe the **complete final image**, not just the masked region.

#### Editing only a small region (e.g. anonymizing a screenshot)

The OpenAI guide is explicit: *"Masking with GPT Image is entirely prompt-based. The model uses the mask as guidance, but may not follow its exact shape with complete precision."* In practice **the edits API always regenerates the full canvas** — even with a mask, every pixel is re-rendered. Pick the right strategy:

| Goal | Recommended approach |
|---|---|
| Replace one region, accept that the rest is **redrawn at very high fidelity but is no longer pixel-identical** (UI text/serial numbers etc. usually survive thanks to "always max fidelity") | `--reference` + a strong `Preserve absolutely everything else exactly as in the reference: …` prompt that **enumerates the surrounding elements** (sidebar items, headers, dates, button labels, logos, etc.). This is often more than good enough for blogs / docs |
| **Pixel-perfect** preservation outside the edited region (e.g. legal evidence, regulated screenshots) | **Hybrid: crop → edit → paste back** with Pillow / ImageMagick. gpt-image-2 only edits the cropped tile; the rest of the original is untouched at the bit level |
| Composite multiple references | `--reference a.png --reference b.png …` (no mask) and write a goal-oriented prompt |

For approach 1, two prompt patterns work well:

- **Enumerate what to preserve**: list every visible element by name and quoted text — `"the dialog header '所在地を表示', the date 'これは…の最後の位置情報です。', the '閉じる' button, the central green location dot, ..."`. The more you name, the stronger the preservation.
- **Replace only X**: `"Replace ONLY the map area with [fictional content]. Do not change any layout, font, color, or text outside the map rectangle."`

Real-world example (anonymizing the map inside an Apple Business "lost mode location" screenshot): a single `--reference --quality high` call replaced the map (street pattern, river, route number, real POI labels) with a fictional neighborhood while preserving the surrounding UI down to device serial numbers. Output resolution was 1536×1024 (down from 2000×1305) — small UI text was redrawn but readable. See `docs/dogfooding-log.md`.

## Prompt Design Guide (gpt-image-2 best practices)

> Sources: [OpenAI Image generation guide](https://developers.openai.com/api/docs/guides/image-generation), [gpt-image-2 model page](https://developers.openai.com/api/docs/models/gpt-image-2)

OpenAI describes gpt-image-2 as the "state-of-the-art image generation model for fast, high-quality image generation and editing" ([model page](https://developers.openai.com/api/docs/models/gpt-image-2)), with strong instruction following, improved layouts, and better text rendering than previous generations. Prompts written like a creative director's brief — ordered, specific, with explicit constraints — work best.

### Structured prompt: consistent ordering, free format

Per the Cookbook:

> "Write prompts in a consistent order (background/scene → subject → key details → constraints)"
> "Use the format that is easiest to maintain. Minimal prompts, descriptive paragraphs, JSON-like structures, instruction-style prompts, and tag-based prompts can all work well"
> — [OpenAI Cookbook](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

Keep the **4-part order** — the format itself (paragraph / JSON / tag / bullet) is free, pick the one easiest to maintain:

```
background/scene → subject → key details → constraints
```

Example (paragraph form):
```
A dim Kyoto ryokan tatami room at dusk, rain against shoji screens (scene) —
an elderly potter in indigo kimono kneading clay at a low wooden wheel (subject),
warm paper-lantern light catching the wet clay and his silver hair (key details).
No modern objects, no text.
```

**Expanded axes** (add when relevant, not required every time): style, composition, lighting, camera, color palette. These are cues, not mandatory slots — the Cookbook treats structure as ordering discipline, not a fixed template.

### Text rendering (gpt-image-2's flagship strength)

Per the Cookbook:

> "Put literal text in **quotes** or **ALL CAPS** and specify typography details (font style, size, color, placement)"
> — [OpenAI Cookbook](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

Two equivalent ways to mark the literal string to render:

- **Quotes** (best when the string contains spaces or mixed-language characters):
  ```
  ...poster with the exact title "腹落ちDMARC" in large serif Japanese font at the top
  ```
- **ALL CAPS** (good for short, single-language headlines):
  ```
  ...bold block typography reading SHIPPING TODAY across the center
  ```

**Always include typography details** — font style, size, color, placement. Just naming the string is not enough:

```
...headline "Email Authentication for SaaS" in medium-weight English sans-serif,
white on dark navy, centered at the top one-third of the canvas.
```

Multilingual text (especially Japanese kanji/kana and emoji) is dramatically improved over previous generations.

### Positive body + explicit exclusions in constraints

Two complementary patterns — use both:

**Body description**: state what *is* there in positive form.

| Vague | Concrete |
|---|---|
| `a room without furniture` | `an empty room with bare walls and polished concrete floor` |

**Constraints**: state exclusions and invariants explicitly. Per the Cookbook:

> "State exclusions and invariants explicitly (e.g., 'no watermark,' 'no extra text,' 'no logos/trademarks')"
> — [OpenAI Cookbook: GPT Image Generation Models Prompting Guide](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

Example (combined):
```
A flat-lay desk scene with a leather notebook, a brass pen, and a ceramic coffee mug
on warm oak wood, soft window light from the left.
Constraints: no watermark, no extra text, no logos.
```

The body uses positive description; the constraints line names what to keep out.

### When editing, name what to keep

```
Preserve the woman's face, hair, and pose exactly as in the reference.
Replace only the background with a neon Tokyo street at night.
```

> gpt-image-2 always processes input images at maximum fidelity, so `input_fidelity` is unnecessary (and rejected by the API). The combination of automatic high fidelity + explicit "Preserve …" wording yields strong reference preservation.

### Goal description + small iterative refinements

Two complementary patterns recommended by the Cookbook:

**1. Describe the goal, not the steps.** Tell the model what the final artifact is *for*, and the structural choices follow:

| ❌ Stepwise | ✅ Goal-oriented |
|---|---|
| `draw a bar chart of 4 bars with values 10 20 30 40 colored blue` | `Create an infographic comparing Q1-Q4 revenue (10, 20, 30, 40 million yen) for a board deck. Clean dark tech aesthetic with neon blue accents. Include title, axis labels and value labels.` |

**2. Iterate with small single-change refinements.** Quoting the Cookbook:

> "Long prompts can work well, but debugging is easier when you start with a clean base prompt and refine with small, single-change follow-ups"
> — [OpenAI Cookbook](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

In practice: write the goal-oriented base prompt, generate once, then refine one thing at a time ("now make the bars thicker", "change the accent to orange") — don't rewrite the whole prompt. This is cheaper and far easier to debug than packing all adjustments into a single mega-prompt.

### Style by visual attributes, not proper nouns

Avoid copyrighted style names. Use `hand-painted watercolor, soft pastel palette, cel-shaded` rather than `Studio Ghibli style`.

### Observe `revised_prompt`

Responses may include a `revised_prompt` (the model's own paraphrase). This skill prints it as `[Revised] ...` and saves it to the metadata sidecar. Use it as a feedback signal to refine the next prompt.

## Cost Optimization

Use `--quality low` ($0.006/image) while iterating; `medium` ($0.053) or `high` ($0.211) for delivery.

**Counter-intuitive**: `1024×1536` (portrait) at `high` is **$0.165** — *cheaper* than `1024×1024` at `high` ($0.211). Pick portrait whenever the use case allows.

| Quality | 1024×1024 | 1024×1536 | 1536×1024 |
|---|---|---|---|
| low | $0.006 | $0.011 | $0.011 |
| medium | $0.053 | $0.080 | $0.079 |
| high | $0.211 | **$0.165** | $0.210 |

## Use case → parameter table

| Intent | size | quality | background | output-format | Notes |
|---|---|---|---|---|---|
| Blog/article hero | `1024x1536` | `medium` | `auto` | `webp` (compression 80) | Portrait reads better |
| OGP/social banner | `1536x1024` | `high` | `auto` | `png` | Sharp text |
| Icon/logo (transparency required) | `1024x1024` | `high` | — | `png` | Generate with gpt-image-2 → `rembg`, or use `--model gpt-image-1.5 --background transparent` |
| Logo (solid background OK) | `1024x1024` | `high` | `auto` | `png` | Cut out background later if needed |
| Infographic | `1024x1536` | `high` | `auto` | `png` | Lots of text; goal-oriented prompt |
| Iterating | `1024x1024` | `low` | `auto` | `png` | Cost control |
| Edit existing | match reference | `high` | `auto` | match reference | `--reference` + spell out what to preserve |

## Sister skill comparison

| Use case | First choice | Reason |
|---|---|---|
| Posters with Japanese/kanji text | **ccskill-gptimage** | Strongest text rendering |
| Business infographics | **ccskill-gptimage** | Strong instruction following + text layout |
| Editing / partial modification | **ccskill-gptimage** | Always processes input at max fidelity |
| Photo / illustration single visuals | Either | Comparable cost |
| Transparent PNG (logos, icons, sprites) | Either | gpt-image-2 can't, but (a) `--model gpt-image-1.5`, (b) `rembg` post-processing, (c) ccskill-nanobanana all work |
| 4K output | ccskill-nanobanana | gpt-image-2 caps at 2K |

## Output files

Each image is saved alongside a **metadata JSON sidecar** (`{name}.{ext}.json`) containing the prompt, `revised_prompt`, parameters and timestamp — useful for reproduction and refinement.

## Constraints

- **Organization Verification required**
- **No transparent background** (`background: transparent` returns 400). Use `--model gpt-image-1.5` or `rembg` post-processing.
- **`input_fidelity` is unnecessary (always max fidelity)** — specifying returns 400, but this is "always on", not "missing"
- Editing with reference images uses more input-image tokens (trade-off of automatic max fidelity)
- Rate limits: Tier 1 = 5 IPM (production needs Tier 3+)
- Timeout: up to 2 minutes for high quality / complex prompts
- No function calling, no structured outputs
- Response is `b64_json` only (no URLs)

## Troubleshooting

- 403 Forbidden → check Org Verification
- Rate limit → upgrade tier or use `--quality low` while iterating
- Timeout → check network; set SDK timeout ≥ 120 s
- Japanese text broken → wrap exactly in quotes; specify font (e.g. `serif Japanese font`)
