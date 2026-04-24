# Virtual Try-On — 人物の服装変更(アイデンティティ保持)

Cookbook 5.2。**人物の同一性を厳格にロック**したまま、服装だけ置換する編集ワークフロー。

## 使い所

- EC サイトの仮想試着機能
- ファッション提案のビジュアライゼーション
- ルックブック生成(モデル 1 人で多数コーディネート)
- キャラクターの衣装バリエーション

## Cookbook 引用

> "Virtual try-on preserves the person's identity while replacing garments with realistic fabric behavior and integrated lighting. Success requires explicit locks on facial features, body geometry, and pose while allowing only clothing modifications."
> — [Cookbook 5.2](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

**書き方のコツ**:
- **ロック要素を列挙**: 顔・表情・肌色・体型・ポーズ・髪・髪型・プロポーション
- 置換範囲を限定: `Replace only the clothing`
- フィット感を指定: `fitting the garments naturally to her existing pose and body geometry with realistic fabric behavior`
- 統合感を要求: `Match lighting, shadows, and color temperature to the original photo`
- 貼り付け感を排除: `without looking pasted on`

---

## プロンプト例(Cookbook 5.2)

```
Edit the image to dress the woman using the provided clothing images. Do not change her face, facial features, skin tone, body shape, pose, or identity in any way. Preserve her exact likeness, expression, hairstyle, and proportions. Replace only the clothing, fitting the garments naturally to her existing pose and body geometry with realistic fabric behavior. Match lighting, shadows, and color temperature to the original photo so the outfit integrates photorealistically, without looking pasted on. Do not change the background, camera angle, framing, or image quality, and do not add accessories, text, logos, or watermarks.
```

**パラメータ**: `size=1024x1536`, `quality=medium`、入力画像 **5 枚**
- Image 1: 全身モデル写真
- Image 2-5: 衣装アイテム(タンクトップ、ジャケット、ブーツ など、個別)

**CLI 例**:
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Edit the image to dress the woman using the provided clothing images. Do not change her face ..." \
  --reference ./model.png \
  --reference ./item_tanktop.png \
  --reference ./item_jacket.png \
  --reference ./item_boots.png \
  --quality high
```

> **注意**: Cookbook 原文は `input_fidelity="high"` を明記しているが、gpt-image-2 では**指定不可**(自動で最大忠実度)。本スキルの main validation が自動除去する。

---

## 応用: 1 着だけ差し替え

```
Edit the image to change only the woman's top to a beige cashmere sweater. Keep her pants, shoes, hair, face, identity, pose, and background exactly as in the reference. The sweater should fit naturally with realistic fabric texture and match the lighting of the scene. Do not add accessories, text, or watermarks.
```

**CLI 例**:
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Edit the image to change only the woman's top to a beige cashmere sweater. ..." \
  --reference ./model.png --quality high
```

---

## 応用: テキストで衣装を指定(衣装画像なし)

衣装画像がなくてもテキストだけで試着できる。

```
Edit the image: keep the person's face, identity, body, pose, hair, and background exactly unchanged. Replace only her outfit with a tailored navy double-breasted suit, white dress shirt, and brown leather oxford shoes. Realistic fabric behavior, matching the original photo's lighting and color temperature, not pasted-on. No accessories, no text, no watermarks.
```

---

## Multi-Image Inputs の書き方(Cookbook Section 2)

複数画像を使う編集では、**インデックス + 役割**を明示するのが公式推奨。

Cookbook Section 2 原文:
> "Reference each input by **index and description** ('Image 1: product photo… Image 2: style reference…') and describe how they interact ('apply Image 2's style to Image 1'). When compositing, be explicit about which elements move where."

適用例:
```
Image 1: the full-body reference photo of the woman.
Images 2-5: individual clothing items (top, jacket, pants, shoes).
Dress the woman in Image 1 with all the clothing shown in Images 2-5.
Preserve the woman's identity, face, body, and pose from Image 1.
```

---

## gpt-image-2 固有の注意

- 顔の同一性は自動で最大忠実度で保たれる(Cookbook の `input_fidelity=high` 指定は gpt-image-2 では不要)
- 入力画像が 5 枚を超えると**API のコンテキスト限界**に近づく(Cookbook 推奨は最大 5 枚)
- 画像入力トークンの増加はコスト増に直結。**不要な参照画像は渡さない**
- edit 経路は全画面再描画のため**背景もわずかに変化**することがある。厳密保持が必要ならハイブリッド方式(Pillow で人物領域だけクロップ→編集→貼り戻し)

## 出典

- Cookbook 5.2 Virtual Clothing Try-On
- URL: https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide
- 取得日: 2026-04-23
