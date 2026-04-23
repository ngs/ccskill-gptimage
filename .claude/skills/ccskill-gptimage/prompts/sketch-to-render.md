# Sketch → Photorealistic Render

Cookbook 5.3。ラフスケッチを写真的リアリズムに変換。**レイアウト・遠近法・プロポーションを保持**しながら、素材と光だけ写実化。

## 使い所

- デザインスケッチの概念検証 (コンセプト → 写真ふう)
- 建築・インテリアの手描きラフをフォトリアル化
- 絵コンテ → 実写ビジュアライゼーション
- 子供のお絵かきを写真化するおもしろ用途

## Cookbook 引用

> "Convert rough sketches into photorealistic images while preserving layout and perspective. Add realism through materials and lighting without introducing new elements."
> — [Cookbook 5.3](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

**書き方のコツ**:
- **Preserve the exact layout, proportions, and perspective** を明示的に書く
- 素材(materials)と照明(lighting)を指定する自由度を与える
- **新要素の追加を禁止**: `Do not add new elements or text.`

---

## プロンプト例(Cookbook 5.3)

```
Turn this drawing into a photorealistic image.
Preserve the exact layout, proportions, and perspective.
Choose realistic materials and lighting consistent with the sketch intent.
Do not add new elements or text.
```

**パラメータ**: `size=1024x1536`, `quality=medium`、入力画像 1 枚(スケッチ)

**CLI 例**:
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Turn this drawing into a photorealistic image. Preserve the exact layout, proportions, and perspective. Choose realistic materials and lighting consistent with the sketch intent. Do not add new elements or text." \
  --reference ./sketch.png --quality high
```

---

## 応用: 素材・光・設定を少しヒント

スケッチの意図をもっと具体的に伝えたい時:

```
Turn this architectural sketch into a photorealistic rendering.
Preserve the exact layout, proportions, camera angle, and perspective from the sketch.
Add realism:
- Materials: warm oak floorboards, white matte walls, brushed brass fittings, natural linen curtains
- Lighting: soft afternoon daylight from the left window, with subtle warm ambient fill
- Atmosphere: slight dust particles in the sunbeam, shallow depth of field
Do not add new furniture, people, or text that is not in the sketch.
```

---

## 応用: 子供のお絵かき → 写実

エンタメ用途(家族用ギフトなど)。Cookbook 本家にはないが、本スキル特有のおもしろ使い方。

```
Turn this child's drawing of an imaginary creature into a photorealistic creature portrait.
Preserve the exact pose, body proportions, relative size of features, and color palette from the drawing — even if anatomically unusual.
Render as if it were a real animal photographed in natural habitat: realistic fur or scales texture, natural lighting, soft bokeh background.
Do not simplify or correct the creature's design; keep the original's spirit.
No text, no watermarks.
```

---

## gpt-image-2 固有の注意

- **自動最大忠実度**のおかげで「スケッチの構図・ライン」が強く保たれる(`input_fidelity` 指定は不要・エラー)
- ただし edit 経路の常として**全画面再描画**されるため、細部のラインが 1 対 1 で一致するわけではない
- スケッチが**薄い/雑**だと写実化の解釈余地が大きくなる。明度・コントラストが高いスケッチほど保持されやすい
- 新要素を勝手に追加することがあるので `Do not add new elements or text.` は必ず入れる
- `quality=high` が素材感の再現には効く

## 出典

- Cookbook 5.3 Drawing → Image (Rendering)
- URL: https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide
- 取得日: 2026-04-23
