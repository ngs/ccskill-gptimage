# Comic / Storyboard

Cookbook 4.7。物語を**複数パネル**の視覚表現に変換。各パネルを独立した visual beat として具体的・アクション中心に記述する。

## 使い所

- 4 コマ漫画 / 縦型 SNS リール用ストリップ
- 映像コンテツの絵コンテ
- プロダクト紹介のステップ解説(Step 1/2/3...)
- 小説・エッセイの挿絵連作

## Cookbook 引用

> "Story-to-comic-strip workflows benefit from defining each beat as concrete, action-focused descriptions. Maintains readability and pacing across panels."
> — [Cookbook 4.7](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

**書き方のコツ**:
- **パネル番号を明示**(`Panel 1:`, `Panel 2:`, …)
- 各パネルで「何が起きているか」を具体的な動詞で
- キャラの**外観・服装・視線**を**毎パネル繰り返す**(drift 防止、Cookbook Section 2 "Iterate" 原則)
- 「同一キャラ」であることを明言(`The same pet`, `The same protagonist`)

---

## プロンプト例(Cookbook 4.7)

```
Create a short vertical comic-style reel with 4 equal-sized panels.
Panel 1: The owner leaves through the front door. The pet is framed in the window behind them, small against the glass, eyes wide, paws pressed high, the house suddenly quiet.
Panel 2: The door clicks shut. Silence breaks. The pet slowly turns toward the empty house, posture shifting, eyes sharp with possibility.
Panel 3: The house transformed. The pet sprawls across the couch like it owns the place, crumbs nearby, sunlight cutting across the room like a spotlight.
Panel 4: The door opens. The pet is seated perfectly by the entrance, alert and composed, as if nothing happened.
```

**パラメータ**: `size=1024x1536`(縦長で SNS Reel 互換), `quality=medium`

**CLI 例**:
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Create a short vertical comic-style reel with 4 equal-sized panels. Panel 1: ..." \
  --size 1024x1536 --quality high \
  --output-name petstory_reel
```

---

## 応用パターン: プロダクト紹介の 3 ステップ

```
Create a horizontal 3-panel infographic illustrating how to use the product.
Panel 1: A woman opens the box, smiling with anticipation. Clean white studio background.
Panel 2: Same woman is fitting the device on her wrist, focused and pleased.
Panel 3: Same woman is walking outdoors, wearing the device visibly on her wrist, with morning sunlight.
Character Consistency: Same woman, same hair, same skin tone, same face across all panels.
Flat modern illustration style, soft pastel colors, minimal decoration.
No text on panels. No watermarks.
```

**パラメータ**: `size=1536x1024`(横長), `quality=high`

**重要**: `Character Consistency:` ブロックで**同一人物**であることを明示。Cookbook 6.4(キャラクター一貫性)の技法と組み合わせる。

---

## 日本語漫画風(縦書き吹き出し付き)

gpt-image-2 は日本語テキスト描画が強いので、セリフ入り漫画も作れる。

```
Create a 4-panel Japanese manga-style short story, arranged top-to-bottom.
Panel 1: A tired salaryman on the last train, staring out the window. Speech bubble (exact Japanese, vertical right-to-left text): "もう終電か..."
Panel 2: His phone vibrates. Text message balloon: "今日もおつかれ！"
Panel 3: He smiles slightly, looking at the phone.
Panel 4: He closes his eyes, the train rocking gently.
Style: clean black-and-white manga inking, screentone shading, standard manga panel borders.
Keep the same salaryman character across all panels. No watermarks.
```

> **注意**: 縦書き・右から左のコマ順は必ずしも再現されない。読み順の厳密性が必要なら**生成後に手で並べ替える**前提で運用。

---

## gpt-image-2 固有の注意

- **パネル数は 4 が安定**。6 以上になると各コマの描写が甘くなる(縦横比を圧迫されるため)
- 同一キャラの**外観 drift** は発生しやすい。公式 Cookbook 4.7 の推奨は「**毎パネルでキャラの外見を繰り返し書く**」
- 縦型(`1024x1536`)は TikTok/Reels サイズ、横型(`1536x1024`)はスライド・横スクロール向け
- コスト: 4 コマ入りポスターを 1 枚生成なので、`high` でも 1 回分($0.165〜$0.211)

## 出典

- Cookbook 4.7 Story-to-Comic Strip
- URL: https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide
- 取得日: 2026-04-23
