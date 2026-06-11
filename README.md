# ccskill-gptimage Image Generation Skill

[日本語 README](README.ja.md)

A Claude Code image-generation skill powered by OpenAI **gpt-image-2** (ChatGPT Images 2.0). Also usable as a standalone image-generation script.

## Features

You don't need to write prompts explicitly: the skill composes an optimal prompt from your project information and context, and generates images with ChatGPT Images 2.0 inside a Claude Code session.

- **No API key needed** — works in association with your ChatGPT subscription
- **Multilingual text rendering** — strong at non-Latin scripts like Japanese (kanji/kana/vertical), Korean, and Chinese
- **Reference-image editing** — composite and partially edit existing images via `--reference`
- **Auto metadata sidecar** — prompt, `revised_prompt`, and parameters saved as JSON

## Examples

Examples generated with this skill. See the full gallery at [`docs/gallery.md`](docs/gallery.md).

<table>
<tr>
  <td align="center" width="33%"><img src="assets/capability-survey/categories/01_photorealistic_portrait_woman.png" width="260" alt="Photoreal portrait"></td>
  <td align="center" width="33%"><img src="assets/capability-survey/categories/19_urban_tokyo_rainy_night_v3.png" width="260" alt="Tokyo Shibuya scramble crossing at rainy night"></td>
  <td align="center" width="33%"><a href="docs/gallery.md"><img src="assets/capability-survey/categories/18_nature_volcanic_coast_dawn.png" width="260" alt="See the full gallery"></a></td>
</tr>
<tr>
  <td align="center" width="33%"><img src="assets/capability-survey/categories/24_line_drawing_fashion_sketch.png" width="260" alt="Fashion line drawing"></td>
  <td align="center" width="33%"><img src="assets/capability-survey/categories/14_japanese_poster_vertical_tategaki.png" width="260" alt="Vertical Japanese poster"></td>
  <td align="center" width="33%"><img src="assets/capability-survey/categories/06_ui_mockup_settings_japanese.png" width="260" alt="iOS Settings UI in Japanese"></td>
</tr>
</table>

## Requirements

This skill can be used in two ways, each with different requirements. You can also switch between the two backends with the `--backend` parameter.

**Option A: ChatGPT subscription + Codex CLI**
- Python 3.10+
- ChatGPT subscription (Plus or higher)
- [Codex CLI](https://github.com/openai/codex) installed and logged in via `codex login`

**Option B: OpenAI API key**
- Python 3.10+
- OpenAI API key
- Organization Verification

## Setup
### 1. Clone the repository
Clone anywhere you prefer.

```bash
cd /path/to/projects
git clone https://github.com/feedtailor/ccskill-gptimage.git
```

### 2. Prepare image generation

Make ChatGPT Images 2.0 available. The preparation differs by usage, so pick the one that fits — Option A is recommended.

#### Option A: ChatGPT subscription + Codex CLI
(Skip this if you already use the Codex CLI linked to your subscription.)

```bash
# Install Codex CLI (Homebrew)
brew install codex
# Log in with your ChatGPT account
codex login
```

#### Option B: OpenAI API key
Configure the following on the [OpenAI Platform](https://platform.openai.com/):

- (a) Issue an API key at [API keys](https://platform.openai.com/api-keys)
- (b) Complete verification at [Organization Settings](https://platform.openai.com/settings/organization/general) → General → Verification

Once configured, write the API key into a `.env` file.

```bash
cp .env.example .env
```

Add the API key from (a) to `.env`:
```
OPENAI_API_KEY=sk-...
```

### 3. Install dependencies
Move into the directory where you cloned ccskill-gptimage and run:

```bash
cd /path/to/ccskill-gptimage
python3 -m venv venv
source venv/bin/activate
python -m pip install -r requirements.txt
```

### 4. Set the command path environment variable

Add the following to `.bashrc` / `.zshrc`:

```bash
export CCSKILL_GPTIMAGE_DIR="/path/to/ccskill-gptimage"
```

## Usage
First, create a symbolic link under the `.claude/skills` directory of the project where you want to use the skill.

```bash
# Create the .claude/skills directory under your project if it doesn't exist
cd /path/to/your-project/
mkdir -p .claude/skills

# Create the symbolic link so the skill is recognized
ln -s $CCSKILL_GPTIMAGE_DIR/.claude/skills/ccskill-gptimage .claude/skills/ccskill-gptimage
```

In Claude Code, ask it to generate images with ChatGPT Images 2.0, or invoke the skill explicitly by typing `/ccskill-gptimage` in your prompt.

You don't need to write a detailed prompt yourself — Claude Code composes the optimal prompt from the conversation context and project information, and picks appropriate options for the task.


## Updating
Run `git pull` where you cloned this skill:

```bash
cd $CCSKILL_GPTIMAGE_DIR
git pull
```

## Using the CLI standalone

You can also use it as an image-generation command.

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
| `--background` | Background (`auto`/`opaque`). `transparent` is unsupported by gpt-image-2 (use `gpt-image-1.5`) | `auto` |
| `--output-format` | Output format (`png`/`jpeg`/`webp`) | `png` |
| `--output-compression` | Compression for jpeg/webp (0-100) | none |
| `--output` | Output directory | `./generated_images` |
| `--reference` | Reference image (repeatable) | none |
| `--mask` | Mask image (transparent area = editable) | none |
| `--input-fidelity` | Not needed for gpt-image-2 (always max fidelity). For `gpt-image-1.5` | none |
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

# Mask-guided editing (requires --backend api)
python generate_image.py "A sunlit indoor lounge with a pool" --reference ./lounge.png --mask ./mask.png --backend api
```

### Output files

Each image is saved with a **metadata JSON** sidecar (`{name}.{ext}.json`) for reproduction and refinement:
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
- **Max resolution**: the model supports up to 4K (longest side ≤ 3840px). This skill's CLI `--size` is currently limited to safe values (max `1536x1024`); use a direct API call for 4K
- **Endpoints**: `/v1/images/generations`, `/v1/images/edits`
- **Filename**: timestamp form (e.g. `20260423_153045.png`)
- **Moderation**: `auto` (default) / `low`

## Troubleshooting

| Symptom | Fix |
|---|---|
| `403 Forbidden` | OpenAI Organization Verification is incomplete → complete it in Organization Settings |

## License

MIT
