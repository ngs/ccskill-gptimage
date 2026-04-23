# Scene Transform — 天候・時間帯変更 / オブジェクト削除

Cookbook 5.6(Lighting and Weather Transformation)と 5.7(Object Removal)を統合。**「構図・アイデンティティ・ジオメトリは保持、環境条件や特定要素だけ変更」** が共通設計。

## 使い所

- 観光地写真を「夕方」「雪の日」「雨の日」に再ライティング
- 広告ビジュアルの季節変種(春/夏/秋/冬)
- 不要な被写体の消去(通行人、電線、散らかり物、不要オブジェクト)
- 製品写真のクリーンアップ(テーブル上の余計な物を消す)

## Cookbook 引用

### 5.6 Lighting / Weather Transformation

> "Re-stage photos for different moods, seasons, or times of day while preserving scene composition, identity, geometry, camera angle, and object placement."

### 5.7 Object Removal

> "Object removal is the edit use case for deleting specific elements from a scene while completely preserving everything else (person identity, lighting, background)."
> — [Cookbook 5.6 / 5.7](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

**共通原則**:
- **何を変えるか、何を保持するか**を両方書く(Cookbook Section 2 の "Constraints" 原則)
- 5.6 は**環境条件のみ変更**: 光の向き、影、大気、降水、地面の濡れ
- 5.7 は**削除対象のみ変更**: `Do not change anything else`

---

## プロンプト例 1: 天候・時間帯変更(Cookbook 5.6)

```
Make it look like a winter evening with snowfall.
```

**パラメータ**: `size=1024x1536`, `quality=medium`、入力画像 1 枚

**CLI 例**:
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Make it look like a winter evening with snowfall." \
  --reference ./sunset_scene.jpg --quality high
```

> **注意**: Cookbook 原文は `input_fidelity="high"` を明記しているが、gpt-image-2 では**指定不可**(自動最大忠実度、本スキル main が自動除去)。

### より具体的に指定するパターン

```
Transform this scene to a snowy winter evening at dusk:
- Time of day: shortly after sunset, cool blue hour
- Weather: gentle snowfall, snow accumulating on flat surfaces
- Lighting: cool ambient light with warm interior windows
- Atmosphere: slight haze, soft bokeh from streetlamps
Preserve: camera angle, composition, subject positions, buildings and landmarks, people's identities and clothing silhouettes.
Do not add or remove any elements. No watermarks.
```

---

## プロンプト例 2: オブジェクト削除(Cookbook 5.7)

```
Remove the flower from man's hand. Do not change anything else.
```

**パラメータ**: `size=1024x1536`, `quality=medium`、入力画像 1 枚

**CLI 例**:
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Remove the flower from man's hand. Do not change anything else." \
  --reference ./man_with_flower.jpg --quality high
```

### より強い「保持」指示

```
Remove the cardboard box on the left side of the kitchen counter. Fill the area naturally with the same counter material (white marble) that is visible elsewhere in the scene. Do not change any other object, person, lighting, shadow, or background element. Preserve the exact camera angle, framing, and all labels/texts on visible products and appliances.
```

---

## 応用: 大人数の観光地から群衆を消す

```
Remove all the tourists from the scene while keeping the landmark, architecture, foreground, sky, and lighting exactly as is. Fill the cleared ground naturally with the same pavement/floor material visible nearby. Do not change time of day, weather, or camera angle. No watermarks.
```

---

## 応用: 春→秋、昼→夜 のような大変化

```
Change this scene from summer daytime to autumn early evening (golden hour).
- Foliage: transform the green trees to warm autumn colors (red, orange, yellow)
- Light: warm golden hour light from low sun on the right
- Ground: add a few scattered fallen leaves on the path
Preserve: camera angle, composition, all buildings, all people's positions and clothing silhouettes, road layout.
No watermarks.
```

---

## gpt-image-2 固有の注意

- **自動最大忠実度**のため、人物・建物・看板文字などは強く保たれる
- ただし edit 経路は**全画面再描画**。細部のピクセル完全一致はない
- 天候変更では**影の方向が変わる** — 物理的に整合した影を自動調整してくれる
- オブジェクト削除では**背景の fill が Cookbook の本領**。複雑背景でも上手く埋める
- 複数の変更(天候 + 人物追加 + 削除)を同時に指示すると精度が落ちる。**1 回 1 変更**が公式推奨(Section 2 "Iterate Instead of Overloading")

## 出典

- Cookbook 5.6 Lighting and Weather Transformation / 5.7 Object Removal
- URL: https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide
- 取得日: 2026-04-23
