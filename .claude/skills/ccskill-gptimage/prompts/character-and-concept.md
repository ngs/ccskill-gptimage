# Character Consistency / Concept Art / Keepsake

Cookbook 6.2(3D Pop-Up Holiday Card)、6.3(Collectible Action Figure)、6.4(Children's Book Art with Character Consistency)を統合。**プレミアム素材感の演出** と **複数画像を跨ぐキャラの同一性維持** が共通テーマ。

## 使い所

- 絵本・マルチシーンのイラスト連作(同一キャラが複数シーンに登場)
- 玩具・コレクタブルの**コンセプト画像**(物理製品の広告用)
- 季節のグリーティングカード(年賀状・クリスマスカード)
- 企業マスコット・ブランドキャラクターのシーン展開

## 共通設計原則

### プレミアム素材感(6.2, 6.3)

> "Premium concept artwork benefits from describing it like product photography: tactile materials (paper layers, textures, worn fur, plastic edges), soft studio lighting, shallow depth of field. The result should feel like a photo of a physical keepsake or retail product, not an illustration."
> — [Cookbook 6.2 / 6.3](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

### キャラクター一貫性(6.4)

> "Multi-scene character workflows require an 'anchor image' first, then edit-based continuations that explicitly repeat the character's outfit, facial features, and style. Restate 'do not redesign the character' in every follow-up."

**設計の 3 本柱**:
- **Scene / Mood / Style / Constraints の 4 ブロック分割**(6.2 / 6.3 の標準フォーマット)
- **Anchor-first アプローチ**(6.4): 最初に 1 枚で設定を固定 → 以降は edit で派生
- **素材感の語彙**: `tactile`, `worn`, `stitched`, `realistic plastic`, `painted metal`, `bokeh`

---

## プロンプト例 1: ホリデーカード(Cookbook 6.2)

```
Create a Christmas holiday card illustration.

Scene:
a cozy Christmas scene with an old teddy bear sitting inside a keepsake box,
slightly worn fur, soft stitching repairs, placed near a window with falling snow outside.
The scene suggests the child has grown up, but the memories remain.

Mood:
Warm, nostalgic, gentle, emotional.

Style:
Premium holiday card photography, soft cinematic lighting,
realistic textures, shallow depth of field,
tasteful bokeh lights, high print-quality composition.

Constraints:
- Original artwork only
- No trademarks
- No watermarks
- No logos

Include ONLY this card text (verbatim):
"Merry Christmas — some memories never fade."
```

**パラメータ**: `size=1024x1536`, `quality=medium`(テキスト重視なら `high`)

---

## プロンプト例 2: コレクタブル玩具のパッケージ写真(Cookbook 6.3)

Python f-string テンプレートとして:

```python
prompt = f"""
Create a collectible action figure of {character_description}, in blister packaging.

Concept:
A nostalgic holiday collectible inspired by the simple toy airplanes
children used to play with during winter holidays.
Evokes warmth, imagination, and childhood wonder.

Style:
Premium toy photography, realistic plastic and painted metal textures,
studio lighting, shallow depth of field,
sharp label printing, high-end retail presentation.

Constraints:
- Original design only
- No trademarks
- No watermarks
- No logos

Include ONLY this packaging text (verbatim):
"{short_copy}"
"""
```

**パラメータ**: `size=1024x1536`, `quality=medium`

**CLI 例**(変数置換済み):
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Create a collectible action figure of a retro Japanese shiba inu mascot with a red scarf, in blister packaging. Concept: ..." \
  --size 1024x1536 --quality high \
  --output-name shiba_figure_mock
```

---

## プロンプト例 3: キャラクター Anchor + 派生シーン(Cookbook 6.4、**2 段ワークフロー**)

### Part 1: Anchor 生成(text → image)

```
Create a children's book illustration introducing a main character.

Character:
A young, storybook-style hero inspired by a little forest outlaw,
wearing a simple green hooded tunic, soft brown boots, and a small belt pouch.
The character has a kind expression, gentle eyes, and a brave but warm demeanor.
Carries a small wooden bow used only for helping, never harming.

Theme:
The character protects and rescues small forest animals like squirrels, birds, and rabbits.

Style:
Children's book illustration, hand-painted watercolor look,
soft outlines, warm earthy colors, whimsical and friendly.
Proportions suitable for picture books (slightly oversized head, expressive face).

Constraints:
- Original character (no copyrighted characters)
- No text
- No watermarks
- Plain forest background to clearly showcase the character
```

**パラメータ**: `size=1024x1536`, `quality=medium`、入力画像なし

**CLI 例**:
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Create a children's book illustration introducing a main character. Character: ..." \
  --size 1024x1536 --quality high \
  --output-name forest_hero_anchor
```

### Part 2: 派生シーン(edit 経路、anchor を `--reference` に)

```
Continue the children's book story using the same character.

Scene:
The same young forest hero is gently helping a frightened squirrel
out of a fallen tree after a winter storm.
The character kneels beside the squirrel, offering reassurance.

Character Consistency:
- Same green hooded tunic
- Same facial features, proportions, and color palette
- Same gentle, heroic personality

Style:
Children's book watercolor illustration,
soft lighting, snowy forest environment,
warm and comforting mood.

Constraints:
- Do not redesign the character
- No text
- No watermarks
```

**パラメータ**: `size=1024x1536`, `quality=medium`、入力画像 1 枚(Part 1 の出力)

**CLI 例**:
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Continue the children's book story using the same character. Scene: ..." \
  --reference ./generated_images/forest_hero_anchor.png \
  --size 1024x1536 --quality high \
  --output-name forest_hero_scene01
```

---

## gpt-image-2 固有の注意

- **edit 経路は常に全画面再描画**だが、gpt-image-2 は自動最大忠実度のためキャラ外観は強く保たれる
- `Do not redesign the character` は**毎ターン書く**のが drift 防止の鉄則
- キャラの外観特徴(服の色、髪型、顔立ち)を**繰り返し言語化**する(`Same green hooded tunic` など)
- 6.4 のワークフローは Phase 0.5 で予定されている Responses API(`previous_response_id`)移行時にさらに安定する — 現状は edit + `--reference` で代替

## 出典

- Cookbook 6.2 3D pop-up holiday card / 6.3 Collectible Action Figure / 6.4 Children's Book Art with Character Consistency
- URL: https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide
- 取得日: 2026-04-23
