# Logo

Cookbook 4.5。ブランドロゴ生成、**変種を `n=4` で並行生成**して選ぶワークフローが公式推奨。

## 使い所

- 新ブランド・サービスのロゴ案
- 既存ブランドのリフレッシュ候補
- アイコン、マーク、シンボルマーク

## Cookbook 引用

> "Strong logo generation comes from clear brand constraints and simplicity. Describe the brand's personality and use case, then ask for a clean, original mark with strong shape, balanced negative space, and scalability across sizes."
> "You can specify parameter 'n' to denote the number of variations you would like to generate."
> — [Cookbook 4.5](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

**設計原則**:
- ブランドの人格(warm, timeless, minimal, technical, playful…)を言葉で
- 使用文脈(local bakery, SaaS product, tech startup…)
- 形状の性質(vector-like shapes, strong silhouette, balanced negative space)
- スケーラビリティ要件(`reads clearly at small and large sizes`)
- 禁止要素(gradients, watermarks, copyright infringement)

---

## プロンプト例(Cookbook 4.5)

```
Create an original, non-infringing logo for a company called Field & Flour, a local bakery.
The logo should feel warm, simple, and timeless. Use clean, vector-like shapes, a strong silhouette, and balanced negative space.
Favor simplicity over detail so it reads clearly at small and large sizes. Flat design, minimal strokes, no gradients unless essential.
Plain background. Deliver a single centered logo with generous padding. No watermark.
```

**パラメータ**: `size=1024x1536`, `quality=medium`, **`n=4`**(4 バリエーション並列生成)

---

## CLI 例

**注意**: 現 `generate_image.py` は `n=1` ハードコードのため、`n=4` を直接指定する手段が無い。**4 回呼ぶ**のが現実的:

```bash
for i in 1 2 3 4; do
  $CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
    "Create an original, non-infringing logo for a company called Field & Flour, a local bakery. The logo should feel warm, simple, and timeless. Use clean, vector-like shapes, a strong silhouette, and balanced negative space. Favor simplicity over detail so it reads clearly at small and large sizes. Flat design, minimal strokes, no gradients unless essential. Plain background. Deliver a single centered logo with generous padding. No watermark." \
    --size 1024x1024 --quality high \
    --output-name "logo_fieldflour_v${i}"
done
```

将来 `--n` オプションを追加する案は別 issue 扱い。

---

## 透過 PNG が必要な場合

gpt-image-2 は **`background: transparent` を受け付けない**(400 エラー)。ロゴ用途で透過が必要ならいずれかを選択:

1. **rembg 後処理(推奨)** — gpt-image-2 で plain background ロゴを生成後に背景除去:
   ```bash
   $CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
     "...logo prompt... plain white background..." --output-name logo_raw
   rembg i generated_images/logo_raw.png generated_images/logo_alpha.png
   ```

2. **`gpt-image-1.5` に切替**(透過対応の旧モデル):
   ```bash
   $CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
     "minimalist fox logo, flat vector, navy and gold" \
     --model gpt-image-1.5 --background transparent --output-format png
   ```

3. **`ccskill-nanobanana` に切替**(姉妹スキル)

## gpt-image-2 固有の注意

- 正方形(`1024x1024`)がロゴには自然だが、**縦長 `1024x1536` を使うとパディングも含めて安く生成**できる(コスト罠)
- Cookbook は `n=4` を推奨するが、**同じプロンプトで 4 回生成するワークフロー**でも代替可能
- 変種を並べて比較したい時は `--output-name` でファイル名を区別するのが重要
- 著作権リスクを避ける公式フレーズ: `original, non-infringing` / `No watermark`

## 出典

- Cookbook 4.5 Logo Generation
- URL: https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide
- 取得日: 2026-04-23
