# Product Mockup / Interior Swap — 対象だけ精密置換

Cookbook 5.4(Product Mockups)と 6.1(Interior design "swap")を統合。**「あるべきものだけをクリーンに抽出 or 置換、周囲は完全保持」** が共通設計。

## 使い所

- EC サイト用のカタログ画像(商品だけ切り出して白背景に)
- 設計仕様書・デザインシステム用の素材整理
- インテリア写真で家具だけ別モデルに差し替え
- 商品パッケージの配置バリエーション

## Cookbook 引用

### 商品抽出(5.4)

> "Product extraction and background removal for catalogs and design systems, emphasizing edge quality and label preservation while maintaining opaque backgrounds."

### 室内オブジェクトの精密置換(6.1)

> "Surgical object replacement in real architectural photography while preserving lighting, shadows, camera angle, and surrounding context for photorealistic results."
> — [Cookbook 5.4 / 6.1](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

**共通コツ**:
- 対象を具体的に指定(`the product` / `replace ONLY the white chairs`)
- エッジ品質の要求: `crisp silhouette, no halos/fringing`
- ラベル/文字の可読性保持: `Preserve product geometry and label legibility exactly`
- 周囲の保持リスト: `Preserve camera angle, room lighting, floor shadows, and surrounding objects`
- スタイル再解釈禁止: `Do not restyle the product; only remove background and lightly polish.`

---

## プロンプト例 1: 商品抽出(Cookbook 5.4)

```
Extract the product from the input image and place it on a plain white opaque background.
Output: centered product, crisp silhouette, no halos/fringing.
Preserve product geometry and label legibility exactly.
Add only light polishing and a subtle realistic contact shadow.
Do not restyle the product; only remove background and lightly polish.
```

**パラメータ**: `size=1024x1536`, `quality=medium`、入力画像 1 枚、**`--background opaque`**

**CLI 例**:
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Extract the product from the input image and place it on a plain white opaque background. ..." \
  --reference ./product_raw.jpg \
  --background opaque --quality high
```

### 透過 PNG が必要な場合

gpt-image-2 は transparent 非対応。商品を透過 PNG として欲しいなら:

1. **白背景で抽出 → rembg で背景除去**(推奨、gpt-image-2 のエッジ品質を活かせる):
   ```bash
   $CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
     "Extract the product..." --reference ./product_raw.jpg \
     --output-name product_white_bg
   rembg i generated_images/product_white_bg.png generated_images/product_alpha.png
   ```
2. `--model gpt-image-1.5 --background transparent`(旧モデル)に切替
3. `ccskill-nanobanana` に切替

---

## プロンプト例 2: 室内家具の精密置換(Cookbook 6.1)

```
In this room photo, replace ONLY white with chairs made of wood.
Preserve camera angle, room lighting, floor shadows, and surrounding objects.
Keep all other aspects of the image unchanged.
Photorealistic contact shadows and fabric texture.
```

**パラメータ**: `size=1536x1024`, `quality=medium`、入力画像 1 枚

**CLI 例**:
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "In this room photo, replace ONLY the white chairs with chairs made of warm oak wood with natural grain. Preserve camera angle, room lighting, floor shadows, and surrounding objects. Keep all other aspects of the image unchanged. Photorealistic contact shadows and wood texture." \
  --reference ./living_room.jpg \
  --size 1536x1024 --quality high
```

---

## 応用: 商品を別シーンに配置

スタジオ白背景の商品を、**生活シーン**に置くパターン。

```
Place the product from the reference image on a warm oak dining table, next to a cup of black coffee and a linen napkin, in a modern Scandinavian kitchen with soft morning light from a window on the left.
Preserve the product's exact shape, label, color, and proportions from the reference.
Do not change the product in any way — only integrate it into the new scene with realistic lighting and contact shadows.
No watermarks, no extra text.
```

---

## gpt-image-2 固有の注意

- **Preserve product geometry and label legibility exactly** は必ず書く。これが無いとラベル文字がモデルの創作で書き換えられる
- **自動最大忠実度**なので商品形状は強く保たれる(`input_fidelity` 指定は不要・エラー)
- 商品抽出用途では `--background opaque` を使い、必要に応じて後工程で透過化する方がきれい
- 6.1 の家具置換のような局所編集は edit API の**全画面再描画**特性を理解して運用。厳密保持が必要なら Pillow ハイブリッド(SKILL.md の「局所編集」セクション参照)
- インテリア系は **1536x1024(横長)** が自然

## 出典

- Cookbook 5.4 Product Mockups / 6.1 Interior design "swap"
- URL: https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide
- 取得日: 2026-04-23
