# Image Translation — 画像内テキストの多言語翻訳

Cookbook 4.2。広告、UI スクリーンショット、パッケージ、インフォグラフィック等の**既存デザインを、レイアウトを組み直さずに別言語にローカライズ**する用途。

## 使い所

- 海外展開時のマーケティング素材ローカライズ
- 多言語ドキュメント・UI スクリーンショット
- 既存インフォグラフィックを別言語版に

## Cookbook 引用

> "Used for localizing existing designs (ads, UI screenshots, packaging, infographics) into another language without rebuilding the layout from scratch."
>
> "The key is to preserve everything except the text—keep typography style, placement, spacing, and hierarchy consistent—while translating verbatim and accurately, with no extra words, no reflow unless necessary, and no unintended edits to logos, icons, or imagery."
> — [Cookbook 4.2](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

**書き方のコツ**:
- 翻訳先言語だけ指定すれば十分。Cookbook 原文は非常にシンプル
- 「テキスト以外は一切変えるな」の明示が重要
- ロゴ、アイコン、写真は原則保持される(明示しなくても)

---

## プロンプト例(Cookbook 4.2)

```
Translate the text in the infographic to Spanish. Do not change any other aspect of the image.
```

**パラメータ**: `size=1024x1536`, `quality=medium`、`images.edit`、入力画像 1 枚

**CLI 例**:
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Translate the text in the infographic to Spanish. Do not change any other aspect of the image." \
  --reference ./infographic_en.png --quality high
```

---

## 応用: 英語 → 日本語化

日本語は gpt-image-2 の得意領域。英語オリジナル資料を日本語化する実用パターン。

```
Translate all the English text in the image to natural Japanese. Keep the typography style (serif vs sans-serif), sizes, colors, and positions as close to the original as possible. Do not change any logos, icons, photos, colors, or layout. Render Japanese text with appropriate font style that matches the tone of the original (e.g., serif English → Mincho-like Japanese serif; sans-serif English → Gothic-like Japanese sans-serif).
```

**CLI 例**:
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Translate all the English text in the image to natural Japanese. Keep the typography style ..." \
  --reference ./pitch_deck_en.png --quality high
```

---

## 応用: 特定の固有名詞は保持したい場合

```
Translate the body text to Japanese, but keep the product name "MailGuard" and the company name "feedtailor" in the original English spelling. Do not change layout, colors, or imagery.
```

---

## 注意点と限界

- **Cookbook 明記**: "with no extra words, no reflow unless necessary, and no unintended edits to logos, icons, or imagery" — 翻訳語が長くなる場合は勝手に reflow されることがある
- 日本語への翻訳は**自動で縦書き/横書き判定はしない**。必要ならプロンプトで指定(`keep horizontal text layout` or `convert to vertical Japanese text layout`)
- 画像が小さな本文テキストを含む場合は `quality=high` 推奨(低品質だと日本語の字形が崩れる)
- edit 経路のため**全画面再描画**される。ロゴやアイコンは gpt-image-2 の自動最大忠実度で強く保たれるが、**ピクセル単位同一の保証はない**

## gpt-image-2 固有の注意

- **`input_fidelity` は指定不要**(自動最大)。Cookbook には書かれていないが gpt-image-2 では常に効いている
- 複数ページをまとめて翻訳するときは**1 枚ずつ個別に edit する**のが安定(1 回の API call は 1 画像)
- 翻訳後の文字長が大きく変わる(英→日は短く、日→英は長くなりがち)場合は、**レイアウトの崩れを受け入れるか、該当テキストだけ短く言い換えるよう指示**する

## 出典

- Cookbook 4.2 Translation in Images
- URL: https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide
- 取得日: 2026-04-23
