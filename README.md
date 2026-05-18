# ccskill-gptimage Image Generation Skill

[日本語 README](README.ja.md)

A Claude Code image-generation skill powered by OpenAI **gpt-image-2** (ChatGPT Images 2.0). Also usable as a standalone CLI.

Sister skill: [ccskill-nanobanana](https://github.com/feedtailor/ccskill-nanobanana) (Gemini 3 Pro Image)

## Features

This skill removes the need to write prompts explicitly: it composes an optimal prompt from your project context and generates images with ChatGPT Image 2.0 inside a Claude Code session.

- **🆕 No API key needed** — with a ChatGPT subscription and [Codex CLI](https://github.com/openai/codex), call gpt-image-2 without `OPENAI_API_KEY` (`--backend codex`)
- **Multilingual text rendering** — Japanese (kanji/kana), emoji, and vertical writing rendered at high quality
- **Agentic reasoning** — plans structure from the prompt before drawing
- **Reference-image editing** — base new images on existing ones via `--reference`
- **Mask editing (inpainting)** — replace parts of existing images (requires `--backend api`)
- **Two backends with auto fallback (`--backend auto`)** — prefers Codex when available, falls back to API on failure
- **Auto metadata sidecar** — prompt, `revised_prompt`, parameters saved as JSON
- **Cost-aware defaults** — built-in knowledge that portrait `high` is cheaper than square `high`

## Examples

A handful of one-shot examples, all generated at `--quality high` with no regeneration and no human prompt-writing (Claude composed the prompts from intent + SKILL.md). The full gallery is at [`docs/gallery.md`](docs/gallery.md) →

<table>
<tr>
  <td align="center" width="33%"><img src="assets/capability-survey/categories/v2/01_photorealistic_portrait_woman.png" width="260" alt="Photoreal portrait"></td>
  <td align="center" width="33%"><img src="assets/capability-survey/categories/v2/19_urban_tokyo_rainy_night_v3.png" width="260" alt="Tokyo Shibuya scramble crossing at rainy night"></td>
  <td align="center" width="33%"><a href="docs/gallery.md"><img src="assets/capability-survey/categories/v2/18_nature_volcanic_coast_dawn.png" width="260" alt="See the full gallery"></a></td>
</tr>
<tr>
  <td align="center" width="33%"><img src="assets/capability-survey/categories/v2/24_line_drawing_fashion_sketch.png" width="260" alt="Fashion line drawing"></td>
  <td align="center" width="33%"><img src="assets/capability-survey/categories/v2/14_japanese_poster_vertical_tategaki.png" width="260" alt="Vertical Japanese poster"></td>
  <td align="center" width="33%"><img src="assets/capability-survey/categories/v2/06_ui_mockup_settings_japanese.png" width="260" alt="iOS Settings UI in Japanese"></td>
</tr>
</table>

## Setup

### Requirements (two options)

This skill runs on either of two backends.

**Option A: ChatGPT subscription + Codex CLI**
- Python 3.10+
- ChatGPT subscription (Plus or higher)
- [Codex CLI](https://github.com/openai/codex) installed and `codex login` completed
- → No API key, no extra billing (covered by your subscription quota)

**Option B: OpenAI API key**
- **Python 3.10+**
- **OpenAI API key** + **Organization Verification completed** (otherwise 403)
- → Pay-as-you-go (a few cents to tens of cents per image)

If both are available, `--backend auto` (default) prefers Codex and falls back to API on failure.

### 1. Clone the repository
Clone anywhere you prefer.

```bash
cd /path/to/projects
git clone https://github.com/feedtailor/ccskill-gptimage.git
```

### 2. Set up the backend

Make gpt-image-2 available. There are two options — Option A is recommended.

**A: Use the Codex CLI**

(Skip this if you already have the Codex CLI linked to your ChatGPT subscription.)

```bash
# Install Codex CLI (Homebrew)
brew install codex
# Log in with your ChatGPT account
codex login
# Verify: should print OK if tokens.access_token is present
cat ~/.codex/auth.json | python -c "import sys,json;print('OK' if json.load(sys.stdin).get('tokens',{}).get('access_token') else 'NG')"
```

**B: Use an API key**

Using gpt-image-2 requires billing to be enabled. Do the following:

- Issue an API key at [OpenAI Platform](https://platform.openai.com/api-keys)
- Set your Organization to **Verified** status<br>(Settings → General → Verifications)

### 3. Set the API key environment variable (Option B only)

```bash
cp .env.example .env
```

Edit `.env`:
```
OPENAI_API_KEY=sk-...
```

### 4. Install dependencies

```bash
cd /path/to/ccskill-gptimage
python3 -m venv venv
source venv/bin/activate
python -m pip install -r requirements.txt
```

### 5. Set the skill path environment variable

Add the following to `.bashrc` / `.zshrc`:

```bash
export CCSKILL_GPTIMAGE_DIR="/path/to/ccskill-gptimage"
```

## Usage
First, create a symbolic link under your target project's `.claude/skills/` directory.

```bash
# Create .claude/skills under your project if it doesn't exist
cd /path/to/your-project/
mkdir -p .claude/skills

# Create the symbolic link so the skill is recognized
ln -s $CCSKILL_GPTIMAGE_DIR/.claude/skills/ccskill-gptimage .claude/skills/ccskill-gptimage
```

In Claude Code, just ask for image generation with gpt-image-2, or invoke the skill explicitly by typing `/ccskill-gptimage` in your prompt.

You don't need to write a detailed prompt yourself — Claude Code composes the optimal prompt from the conversation context and project information, and picks appropriate options for the task.


## Updating
Run `git pull` where you cloned this skill:

```bash
cd $CCSKILL_GPTIMAGE_DIR
git pull
```

## Using the CLI standalone

You can also use this as an image generation CLI, without going through the Claude Code skill.

```bash
cd $CCSKILL_GPTIMAGE_DIR
source venv/bin/activate
python generate_image.py "a coastal sunset"
```

### Options

| Option | Description | Default |
|---|---|---|
| `--size` | Output size (`auto`/`1024x1024`/`1024x1536`/`1536x1024`) | `1024x1024` |
| `--quality` | Quality (`auto`/`low`/`medium`/`high`) | `auto` |
| `--background` | Background (`auto`/`opaque`). `transparent` requires `--model gpt-image-1.5` | `auto` |
| `--output-format` | Output format (`png`/`jpeg`/`webp`) | `png` |
| `--output-compression` | Compression for jpeg/webp (0-100) | none |
| `--output` | Output directory | `./generated_images` |
| `--reference` | Reference image (repeatable) | none |
| `--mask` | Mask image (transparent area = editable) | none |
| `--input-fidelity` | Not needed for gpt-image-2 (always max fidelity). For `gpt-image-1.5` only | none |
| `--moderation` | Moderation (`auto`/`low`) | `auto` |
| `--backend` | Generation backend (`auto`/`codex`/`api`). `auto` prefers Codex, falls back to API on failure | `auto` |

### Examples

```bash
# Basic
python generate_image.py "A minimalist fox logo, flat vector, navy and gold"

# Japanese poster
python generate_image.py 'A minimalist editorial poster with the exact title "腹落ちDMARC" in large serif Japanese font, dark navy background' --size 1024x1536 --quality high

# Edit using a reference (background swap)
python generate_image.py "Place the same fox logo on a deep navy background with subtle gold sparkles. Preserve the fox's pose and proportions from the reference." --reference ./logo.png --quality medium

# Multi-reference composition
python generate_image.py "Photorealistic gift basket on white" --reference ./a.png --reference ./b.png --reference ./c.png

# Mask inpainting
python generate_image.py "A sunlit indoor lounge with a pool" --reference ./lounge.png --mask ./mask.png
```

### Output files

Each image is saved with a **metadata JSON sidecar** (`{name}.{ext}.json`) for reproduction and refinement:
```json
{
  "model": "gpt-image-2",
  "prompt": "...",
  "revised_prompt": "...",
  "size": "1024x1024",
  "quality": "high",
  "timestamp": "2026-04-23T10:00:00"
}
```

## Specs

- **Model**: `gpt-image-2` (snapshot `gpt-image-2-2026-04-21`)
- **Input**: text / images
- **Output**: image only (`b64_json`, no URL is returned)
- **Max resolution**: 2K
- **Endpoints**: `/v1/images/generations`, `/v1/images/edits`
- **Filename**: timestamp form (e.g. `20260423_153045.png`)

## Troubleshooting

| Symptom | Fix |
|---|---|
| `403 Forbidden` | Complete OpenAI Organization Verification |
| `Rate limit exceeded` | Tier 1 is 5 IPM only — production needs Tier 3+ |
| Timeout | Set SDK timeout ≥ 120 s |
| Broken Japanese text | Wrap in `" "`; specify font (`serif Japanese font`) |

## License

MIT
