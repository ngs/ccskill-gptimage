# Scene Composite — 人物を新シーンへ / 複数画像合成

Cookbook 5.8(Insert the Person Into a Scene)と 5.9(Multi-Image Referencing and Compositing)を統合。**入力画像から要素を抽出して新シーンに合成**する用途。

## 使い所

- キャンペーン撮影(モデル写真 + 背景写真を合成)
- ストーリーボード制作(人物を様々なロケに置く)
- 製品とライフスタイル写真の合成
- ペットとユーザーの写真合成
- グループ写真の再構成

## Cookbook 引用

### 5.8 Insert Person Into Scene

> 人物を新しいシーンに合成するワークフロー。ストーリーボードやキャンペーン制作で、人物の顔・アイデンティティを保持しながら背景や状況を変更する用途に適している。

### 5.9 Multi-Image Compositing

> "Combines elements from multiple input images into a single believable composite—ideal for 'insert object/person into scene' workflows. Key is specifying what to transplant, where it goes, and what must remain unchanged while matching lighting, perspective, scale, and shadows."
> — [Cookbook 5.8 / 5.9](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

**共通原則**:
- **何を移植するか、どこに置くか、何を保持するか**を明示
- 光・遠近・スケール・影のマッチを要求(`match lighting, perspective, scale, shadows`)
- 「貼り付け感」を排除: `avoid pasted-on appearance`
- 背景・フレーミングの保持を明示

## Multi-Image Inputs の書き方(Cookbook Section 2)

> "Reference each input by **index and description** ('Image 1: product photo… Image 2: style reference…') and describe how they interact ('apply Image 2's style to Image 1'). When compositing, be explicit about which elements move where."

**必須パターン**:
- 各入力画像を `Image N: 説明` と**インデックス付きで定義**
- どの要素がどの画像から来るかを**文中で指し示す**
- 移植先の位置関係を具体的に(`right next to the woman`, `in the background behind the table`)

---

## プロンプト例 1: 人物を新シーンに挿入(Cookbook 5.8)

```
Generate a highly realistic action scene where this person is running away from a large, realistic brown bear attacking a campsite. The image should look like a real photograph someone could have taken, not an overly enhanced or cinematic movie-poster image.
She is centered in the image but looking away from the camera, wearing outdoorsy camping attire, with dirt on her face and tears in her clothing. She is clearly afraid but focused on escaping, running away from the bear as it destroys the campsite behind her.
The campsite is in Yosemite National Park, with believable natural details. The time of day is dusk, with natural lighting and realistic colors. Everything should feel grounded, authentic, and unstyled, as if captured in a real moment. Avoid cinematic lighting, dramatic color grading, or stylized composition.
```

**パラメータ**: `size=1024x1536`, `quality=medium`、入力画像 1 枚(人物ソース)

> **注意**: Cookbook 原文は `input_fidelity="high"` を明記しているが、gpt-image-2 では**指定不可**(自動最大忠実度、本スキル main が自動除去)。

**CLI 例**:
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Generate a highly realistic action scene where this person is ..." \
  --reference ./person_source.jpg --quality high
```

---

## プロンプト例 2: 2 枚合成(Cookbook 5.9)

```
Place the dog from the second image into the setting of image 1, right next to the woman, use the same style of lighting, composition and background. Do not change anything else.
```

**パラメータ**: `size=1024x1536`, `quality=medium`、入力画像 2 枚

**CLI 例**:
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Place the dog from the second image into the setting of image 1, right next to the woman, use the same style of lighting, composition and background. Do not change anything else." \
  --reference ./scene_with_woman.jpg \
  --reference ./dog.jpg \
  --quality high
```

---

## 応用: 3 枚以上を明示インデックスで

Cookbook Section 2 の推奨パターン(index + description)。

```
Image 1: a dining room with a wooden table and warm evening lighting.
Image 2: a ceramic coffee mug with a logo.
Image 3: a plated croissant on a white plate.
Generate a photorealistic composite: place the coffee mug from Image 2 and the croissant from Image 3 on the table in the setting of Image 1. Match the lighting, shadow direction, and color temperature of Image 1. Preserve the mug's logo and the croissant's shape exactly.
Do not change the dining room, the table, the window, or other existing objects. No watermarks.
```

---

## 応用: 人物を別イベントに配置

キャンペーン制作で同じモデルを複数ロケに置く。

```
Image 1: the model's reference portrait (studio shot).
Generate a highly realistic photograph of this same person standing on a coastal cliff at sunset, wearing a cream linen shirt and khaki trousers, looking out at the sea. Shot at a low angle with a 35mm lens, warm golden hour light, natural skin texture, no heavy retouching.
Preserve her exact facial identity, hair, and proportions from Image 1. Replace only her outfit (to the described attire) and place her in the new setting with matching lighting.
No watermarks, no text.
```

---

## gpt-image-2 固有の注意

- **最大 5 枚の参照画像**が実用上限。それ以上は文脈が散る
- Cookbook が頻用する `input_fidelity="high"` は gpt-image-2 では**自動で効いている**ため指定不要
- 合成時に **drift しやすい要素**: 人物の表情、小物の細部、背景の深度
- 「貼り付け感」を避けるには、**影・光・色温度のマッチ**を明示的に指示
- 複雑な合成(3 枚以上 + 多数要素の再配置)は `quality=high` が必須。`medium` だと破綻しやすい

## 出典

- Cookbook 5.8 Insert the Person Into a Scene / 5.9 Multi-Image Referencing and Compositing
- URL: https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide
- 取得日: 2026-04-23
