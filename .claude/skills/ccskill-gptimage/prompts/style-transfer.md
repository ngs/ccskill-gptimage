# Style Transfer — 参照画像のビジュアル言語を別コンテンツへ

Cookbook 5.1。参照画像の**パレット・テクスチャ・筆致**を保ちつつ、被写体やシーンを差し替える。

## 使い所

- アートワークのスタイルを統一してシリーズ化
- ブランドビジュアルガイドに沿った追加素材生成
- 参照作品の「雰囲気」を再利用して別構図のイラストを作る

## Cookbook 引用

> "Preserves the visual language (palette, texture, brushwork) from a reference image while changing the subject or scene. Success requires specifying what stays consistent and what changes, with hard constraints to prevent drift."
> — [Cookbook 5.1](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

**書き方のコツ**:
- 「保持するスタイル要素」と「変えるコンテンツ」を分けて明示
- 保持するスタイルを言語化: `palette, texture, brushwork, film grain, line thickness`
- **反復時は保持制約を毎回繰り返す**(drift 防止)

---

## プロンプト例(Cookbook 5.1)

```
Use the same style from the input image and generate a man riding a motorcycle on a white background.
```

**パラメータ**: `size=1024x1536`, `quality=medium`、入力画像 1 枚(スタイル参照)

**CLI 例**:
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Use the same style from the input image and generate a man riding a motorcycle on a white background." \
  --reference ./style_reference.png --quality high
```

---

## 応用: スタイル要素を明示化してより強く反映

Cookbook 原文はシンプルだが、スタイル要素を具体化するとブレにくい。

```
Use the same visual style as the input image — specifically:
- the muted watercolor palette (dusty pinks, soft grays, pale blues)
- visible paper texture and subtle grain
- soft, slightly feathered line work
- painterly brush strokes rather than sharp edges
Apply this style to a new subject: a young woman sitting at a cafe window in the rain, looking out, cup of coffee in hand.
Preserve the exact style properties listed above. Do not shift to photorealism.
```

**CLI 例**:
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Use the same visual style as the input image — specifically: ..." \
  --reference ./watercolor_ref.png --quality high
```

---

## 連作のシリーズ化パターン

最初の 1 枚を **anchor** として、シリーズ化したい時は character-and-concept.md の Anchor-first 技法とも組み合わせる。

```
Use the same style and color palette as the input image.
Generate the next scene in the series: [シーンを記述].
Style Consistency:
- Same line weight, color saturation, and texture as the reference
- Same illustration technique (do not shift to digital painting or photorealism)
Constraints:
- No text, no watermarks
- Match the reference's aspect ratio
```

---

## gpt-image-2 固有の注意

- **自動最大忠実度**で入力画像のスタイルも強く拾われる(`input_fidelity` 不要)
- 複数のスタイル参照を合成したい場合は **2 枚以上 `--reference`** できる(Multi-Image Inputs パターン)
- スタイル転写は**完璧な複製ではない**。「雰囲気の継承」として運用する
- 写真→イラスト化、イラスト→写真化、のような**メディウム変換**は style transfer よりも sketch-to-render.md のパターンが適切

## 出典

- Cookbook 5.1 Style Transfer
- URL: https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide
- 取得日: 2026-04-23
