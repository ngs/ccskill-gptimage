# Infographics / Diagrams / Slides & Charts

Cookbook 4.1, 4.9, 4.10 を統合。**密集レイアウト・画像内の多いテキスト**が共通特徴で、`quality=high` が推奨される用途群。

## 使い所

- 技術フロー図(仕組みを視覚と文章で説明)
- 科学・教育用の概念図(学習教材)
- 事業スライド(ピッチデッキ、ダッシュボード、KPI パネル)
- データ可視化(TAM/SAM/SOM、棒グラフ、タイムライン)

## 共通設計原則(Cookbook 引用)

> "Use infographics to explain structured information for a specific audience: students, executives, customers, or the general public."
> "For dense layouts or heavy in-image text, it's recommended to set output generation quality to 'high'."
> — [Cookbook 4.1](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

**重要な書き方のコツ**:
- 対象オーディエンス(学生/経営層/一般/子供)をプロンプト冒頭に含める
- 具体的なデータ値(例: `TAM: $42B`)を**プロンプトに直接書き込む** — モデルが自動で数値を決めるとブレる
- 除外したい装飾を Constraints で明示(例: `Avoid clip art, stock photography, gradients, shadows, decorative elements`)

---

## プロンプト例 1: 機械の仕組みを説明する技術インフォグラフィック(Cookbook 4.1)

```
Create a detailed Infographic of the functioning and flow of an automatic coffee machine like a Jura.
From bean basket, to grinding, to scale, water tank, boiler, etc.
I'd like to understand technically and visually the flow.
```

**パラメータ**: `size=1024x1536`, `quality=medium`(密集するなら `high`)

**CLI 例**:
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Create a detailed Infographic of the functioning and flow of an automatic coffee machine like a Jura. From bean basket, to grinding, to scale, water tank, boiler, etc. I'd like to understand technically and visually the flow." \
  --size 1024x1536 --quality high
```

---

## プロンプト例 2: 学習用の科学図(Cookbook 4.9)

```
Create a simple biology diagram titled "Cellular Respiration at a Glance" for high school students.

Show how glucose turns into energy inside a cell. Include glycolysis, the Krebs cycle, and the electron transport chain.
Use arrows to connect the steps, and label the main molecules: glucose, pyruvate, ATP, NADH, FADH2, CO2, O2, and H2O.
Make it look like a clean classroom handout or slide, with a white background, simple icons, clear labels, and easy-to-read text.

Avoid tiny text, extra decoration, or anything that makes the diagram hard to understand.
```

**パラメータ**: `size=1536x1024`, `quality=high`(小さなラベル密集のため必須)

---

## プロンプト例 3: ピッチデッキ 1 スライド(Cookbook 4.10)

```
Create one pitch-deck slide titled **"Market Opportunity"** that feels like a real Series A fundraising slide from a YC-backed startup.

Use a clean white background, modern sans-serif typography like Inter, and a crisp, minimal layout. The slide should include:

* A TAM/SAM/SOM concentric-circle diagram in muted blues and grays
* Specific, believable market sizing numbers:

  * **TAM:** $42B
  * **SAM:** $8.7B
  * **SOM:** $340M
* A clean bar chart below showing market growth from **2021 to 2026**, with a subtle upward trend
* Small footnotes: **"AGI Research, 2024"** and **"Internal analysis"**
* A company logo placeholder in the bottom-right corner

The design should look like it belongs in a deck that actually raised money: highly readable text, clear data hierarchy, polished spacing, and professional startup-style visual language.

Avoid clip art, stock photography, gradients, shadows, decorative elements, or anything that feels generic or overdesigned.
```

**パラメータ**: `size=1536x864`, `quality=high`

> **注意**: `1536x864` は現 `generate_image.py` の `SIZE_CHOICES` に無い。近い代替は `1536x1024`(16:10)。Cookbook と厳密に揃えたい場合は別 issue で choices 拡張を検討。

---

## 日本語テキストを含むインフォグラフィック

gpt-image-2 は日本語漢字・かなの描画が強い。SKILL で紹介している `14_tategaki` 作例の系統。

```
Create a clean bilingual infographic titled "DMARC の仕組み" for a Japanese SaaS audience.
Layout the flow left-to-right: 送信者 (Sender) → DNS TXT レコード → 受信サーバー → 認証結果 (Pass / Fail / Quarantine).
Label each step in both Japanese and English, Japanese as the primary label in bold serif font.
White background, navy and teal accent colors, no decorative illustrations.
Avoid watermarks, logos, or stock imagery.
```

---

## gpt-image-2 固有の注意

- **`quality=high` がコスト的に見合う局面**。密なテキストで `medium` だと潰れやすい
- 縦長(`1024x1536`)の `high` は $0.165、正方形 `high` より安い — ポートレート型インフォグラフィックはむしろ縦長が得
- Constraint 節は必ず書く(`Avoid clip art, stock photography, gradients, ...` のようなパターンが公式推奨)

## 出典

- Cookbook 4.1 Infographics / 4.9 Scientific-Educational / 4.10 Slides Diagrams Charts
- URL: https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide
- 取得日: 2026-04-23
