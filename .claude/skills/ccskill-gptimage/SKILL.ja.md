---
name: ccskill-gptimage
description: |
  画像生成が必要な時に使用するスキル。
  OpenAI gpt-image-2 (ChatGPT Images 2.0) で高品質な画像を生成・編集します。
  日本語/多言語テキスト描画、複雑指示の解釈、参照画像保持、透過 PNG、複雑インフォグラフィックに強い。
---

# ccskill-gptimage 画像生成スキル

## 概要

このスキルは OpenAI gpt-image-2 を使って画像を生成・編集します。**ユーザは「こんな画像が欲しい」と意図を伝えるだけで十分** で、プロンプトの構造化・スタイル指定・コスト最適化は Claude が会話履歴とプロジェクト文脈から自動で組み立てます。姉妹スキル `ccskill-nanobanana` (Gemini 3 Pro Image) と用途で使い分けます。

## 前提条件

- 環境変数 `CCSKILL_GPTIMAGE_DIR` にこのスキルのリポジトリパスを設定
  ```bash
  export CCSKILL_GPTIMAGE_DIR="$HOME/projects/ccskill-gptimage"
  ```
- 環境変数 `OPENAI_API_KEY` が設定されていること(または `$CCSKILL_GPTIMAGE_DIR/.env` に記載)
- **OpenAI Organization Verification 済みであること**(未検証 Org では 403 になる)

## 使い方

```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py "プロンプト"
```

### オプション

| オプション | 説明 | デフォルト | 値 |
|---|---|---|---|
| `--size` | 出力サイズ | `1024x1024` | `auto` / `1024x1024` / `1024x1536` (縦長) / `1536x1024` (横長) |
| `--quality` | 品質 | `auto` | `auto` / `low` / `medium` / `high` |
| `--background` | 背景 | `auto` | `auto` / `opaque` (`transparent` は gpt-image-2 非対応 — `--model gpt-image-1.5` で対応可) |
| `--output-format` | 出力形式 | `png` | `png` / `jpeg` / `webp` |
| `--output-compression` | 圧縮率(jpeg/webp) | なし | 0-100 |
| `--output` | 出力ディレクトリ | `./generated_images` | 任意のパス |
| `--output-name` | 出力ファイル名(拡張子は output-format から決定) | タイムスタンプ | 任意の文字列 |
| `--reference` | 参照画像(複数指定可) | なし | 画像ファイルパス |
| `--mask` | マスク画像(透明部分が編集対象、`--reference` 必須) | なし | 画像ファイルパス |
| `--input-fidelity` | gpt-image-2 では指定不要(常に最大忠実度)。`--model gpt-image-1.5` 用 | なし | `high` / `low` |
| `--moderation` | モデレーション | `auto` | `auto` / `low` |
| `--model` | モデル ID | `gpt-image-2` | 任意のモデル ID |

### 使用例

#### 基本

```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "A children's book illustration of a veterinarian listening to a baby otter's heartbeat"
```

#### 日本語テキスト入りポスター

```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "A minimalist editorial poster with the exact title \"腹落ちDMARC\" in large serif Japanese font at the top, dark navy background, subtle terminal motif" \
  --size 1024x1536 --quality high
```

#### 透過 PNG が必要な場合

gpt-image-2 は透過背景に対応していません。以下のいずれかを使ってください:

1. **後処理で切り抜く(推奨、gpt-image-2 のテキスト描画/レイアウトの強みを保持)**
   ```bash
   $CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py "minimalist fox logo on plain white background"
   rembg i generated_images/<画像>.png generated_images/<画像>_alpha.png
   ```
2. **`gpt-image-1.5` に切替(透過対応の旧モデル)**
   ```bash
   $CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
     "minimalist fox logo, flat vector, navy and gold" \
     --model gpt-image-1.5 --background transparent --output-format png
   ```
3. **姉妹スキル `ccskill-nanobanana` を使う**

#### 参照画像をベースに編集(背景置換)

```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Place the same fox logo on a deep navy background with subtle gold sparkles, ready for use as a hero image. Preserve the fox's pose and proportions from the reference." \
  --reference ./logo.png --quality medium
```

> **入力画像の忠実度について**: gpt-image-2 は **常に自動で最大忠実度** で入力画像を処理するため、`input_fidelity` パラメータは指定不要(指定すると 400 エラー)。これは欠落ではなく自動仕様。構図保持はプロンプト内で「Preserve the … from the reference」と書くだけで十分強く効きます。トレードオフは編集時の入力画像トークンが多くなる(コスト増)ことなので、**不要な参照画像は渡さない** のがコスト最適化のコツ。

#### 複数参照を合成

```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Photorealistic gift basket on white, labeled 'Relax & Unwind', containing all items" \
  --reference ./body-lotion.png --reference ./bath-bomb.png --reference ./soap.png
```

#### マスク編集(inpainting)

```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "A sunlit indoor lounge area with a pool containing a flamingo" \
  --reference ./lounge.png --mask ./mask.png
```

> マスクの透明部分が置換対象。プロンプトには **新しい完全な画像全体** を記述する(消した部分だけではない)。

#### 局所編集(スクリーンショットの一部だけ差し替えたい場合)

OpenAI 公式ガイドに明記:*"Masking with GPT Image is entirely prompt-based. The model uses the mask as guidance, but may not follow its exact shape with complete precision."*。つまり **edits API はマスクの有無に関わらず常に全画面を再描画** する(マスクは "ガイダンス" に過ぎない)。用途に応じて戦略を選ぶ:

| 目的 | 推奨アプローチ |
|---|---|
| 一部だけ差し替えればよく、それ以外が **「ピクセル単位で同一」ではないが極めて高い忠実度で再描画される」のは許容**(常時最大忠実度のおかげで UI テキスト・シリアル番号などはほぼ保持される) | `--reference` + 強い `Preserve absolutely everything else exactly as in the reference: …` プロンプト。**保持する要素を列挙** する(サイドバー項目、ヘッダ、日付、ボタンラベル、ロゴ等)。ブログ・ドキュメント用途ならこれで十分なことが多い |
| 編集領域以外を **ピクセル単位で完全保持** したい(法的証拠、規制対象スクリーンショット等) | **ハイブリッド: クロップ → 編集 → 貼り戻し**。Pillow / ImageMagick で対象領域だけ切り出して gpt-image-2 で同サイズに生成し、元画像にペースト。それ以外は元画像のビット列がそのまま残る |
| 複数参照画像の合成 | `--reference a.png --reference b.png …`(マスクなし)+ ゴール志向プロンプト |

アプローチ 1 で効くプロンプトパターン:

- **保持要素を列挙する**: 引用符付きで具体テキストを書き並べる — `"the dialog header '所在地を表示', the date 'これは…の最後の位置情報です。', the '閉じる' button, the central green location dot, ..."`。列挙が多いほど保持が強くなる
- **「X だけ差し替えろ」と書く**: `"Replace ONLY the map area with [架空内容]. Do not change any layout, font, color, or text outside the map rectangle."`

実例: Apple Business「紛失モード位置確認」スクリーンショットの匿名化。`--reference --quality high` 一発で、地図(街路網・河川・国道番号・実在 POI ラベル)を架空の住宅地に差し替えつつ、周辺 UI は **デバイスシリアル番号まで含めて** 再現された。出力解像度は 1536×1024(元 2000×1305 から縮小)で、小さな UI 文字は再描画されたが可読性は維持。詳細: `docs/dogfooding-log.md`

## プロンプト設計ガイド (gpt-image-2 向けベストプラクティス)

> 一次資料: [OpenAI Image generation guide](https://developers.openai.com/api/docs/guides/image-generation), [gpt-image-2 model page](https://developers.openai.com/api/docs/models/gpt-image-2)

OpenAI は gpt-image-2 を「state-of-the-art image generation model for fast, high-quality image generation and editing」と表現しています([モデルページ](https://developers.openai.com/api/docs/models/gpt-image-2))。従来世代より強い指示追従性・改善されたレイアウト・向上したテキスト描画が特徴です。タグ羅列ではなく **クリエイティブディレクターの指示書**(順序立てて、具体的に、制約を明示して)のようなプロンプトが最大の効果を発揮します。

### 構造化プロンプト: 順序を揃える、フォーマットは自由

Cookbook 原文:

> "Write prompts in a consistent order (background/scene → subject → key details → constraints)"
> "Use the format that is easiest to maintain. Minimal prompts, descriptive paragraphs, JSON-like structures, instruction-style prompts, and tag-based prompts can all work well"
> — [OpenAI Cookbook](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

要は **4 区分の順序** を守ること。フォーマット(段落・JSON・tag・箇条書き)は自由で、維持しやすいものを選べばよい:

```
背景/シーン → 被写体 → 細部の要素 → Constraints(制約)
```

段落形式での例:
```
A dim Kyoto ryokan tatami room at dusk, rain against shoji screens (背景/シーン) —
an elderly potter in indigo kimono kneading clay at a low wooden wheel (被写体),
warm paper-lantern light catching the wet clay and his silver hair (細部).
No modern objects, no text. (Constraints)
```

**追加の軸**(必要に応じて使う / 必須ではない): スタイル、構図、ライティング、カメラ、カラーパレット。Cookbook はこれらを固定スロットではなく「順序の規律」として扱っているので、過度に形式化しないこと。

### テキスト描画(gpt-image-2 の最大の強み)

Cookbook 原文:

> "Put literal text in **quotes** or **ALL CAPS** and specify typography details (font style, size, color, placement)"
> — [OpenAI Cookbook](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

描画したい文字列のマークアップは 2 通り、どちらでも OK:

- **引用符**(スペースや多言語混在を含む場合はこちら推奨):
  ```
  ...poster with the exact title "腹落ちDMARC" in large serif Japanese font at the top
  ```
- **ALL CAPS**(短い単言語の見出し向け):
  ```
  ...bold block typography reading SHIPPING TODAY across the center
  ```

**タイポグラフィ詳細は必ず添える** — フォントスタイル・サイズ・色・配置。文字列だけ指定してもうまく描画されません:

```
...headline "Email Authentication for SaaS" in medium-weight English sans-serif,
white on dark navy, centered at the top one-third of the canvas.
```

gpt-image-2 は多言語テキスト、特に **日本語(漢字/かな)・絵文字の混在表現** が大幅に強化されており、日本語ポスター・SNS バナー・スライド見出しが一発で読める品質で出ます。

### 本文は肯定形 + Constraints で除外を明示

公式 Cookbook は **2 つのパターンを併用** することを推奨しています。

**本文描写**: 「あるもの」を肯定形で書く。

| 曖昧 | 具体的 |
|---|---|
| `a room without furniture` | `an empty room with bare walls and polished concrete floor` |

**Constraints 節**: 除外したいもの・不変にしたいものは明示的に列挙する。Cookbook 原文:

> "State exclusions and invariants explicitly (e.g., 'no watermark,' 'no extra text,' 'no logos/trademarks')"
> — [OpenAI Cookbook: GPT Image Generation Models Prompting Guide](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

組み合わせた例:
```
A flat-lay desk scene with a leather notebook, a brass pen, and a ceramic coffee mug
on warm oak wood, soft window light from the left.
Constraints: no watermark, no extra text, no logos.
```

本文は肯定形、Constraints 行で除外要素を列挙、という書き分けがポイントです。

### 編集時は「保持するもの」を明示

```
Preserve the woman's face, hair, and pose exactly as in the reference.
Replace only the background with a neon Tokyo street at night.
```

> gpt-image-2 は **常に自動で最大忠実度** で入力画像を処理するので、`input_fidelity` パラメータの指定は不要(指定するとエラー)。**プロンプト内で「保持したい要素」を文章で明示** すれば、自動高忠実度処理と組み合わさって構図保持はかなり強く効きます。

### ゴール指示 + 小さく段階的に refine

Cookbook が推奨する 2 つのパターンを**併用**します。

**1. 手順ではなくゴールを伝える**。最終成果物が「何のために」あるかを書くと、構造的な選択はモデルが行います:

| ❌ 手順指示型 | ✅ ゴール伝達型 |
|---|---|
| `draw a bar chart of 4 bars with values 10 20 30 40 colored blue` | `Create an infographic comparing Q1-Q4 revenue (10, 20, 30, 40 million yen) for a board deck. Use a clean dark tech aesthetic with neon blue accents. Include title, axis labels, and value labels on each bar.` |

**2. 小さな単一変更で段階的に refine する**。Cookbook 原文:

> "Long prompts can work well, but debugging is easier when you start with a clean base prompt and refine with small, single-change follow-ups"
> — [OpenAI Cookbook](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

実践: ゴール指向のベースプロンプトで 1 回生成 → 1 点ずつ修正指示(「棒を太く」「アクセントをオレンジに」)。巨大な一発プロンプトに全調整を詰め込むより安く、デバッグしやすい。

### スタイル指定は固有名詞より視覚特徴の分解で

版権リスクを避け再現性を上げるため、`Studio Ghibli風` より `hand-painted watercolor, soft pastel palette, cel-shaded` のように視覚的特徴を分解します。

### `revised_prompt` を観察する

レスポンスにはモデルが内部で書き換えた `revised_prompt` が含まれることがあり、本スキルは stdout に `[Revised] ...` として出力 + メタデータ JSON にも保存します。Claude は次の生成ターンでこれを見て、より精緻なプロンプトを組み立て直すフィードバックループを使えます。

## コスト最適化

実装/テスト時は `--quality low` を使う($0.006/枚)。本番出力は `medium`($0.053) または `high`($0.211)。

**非自明なコスト罠**: `1024×1536`(縦長) の `high` は `$0.165` で、**正方形 `1024×1024` の `high`($0.211) より安い**。ポートレート用途は意図的に縦長を選ぶとコストが下がります。

| 品質 | 1024×1024 | 1024×1536 | 1536×1024 |
|---|---|---|---|
| low | $0.006 | $0.011 | $0.011 |
| medium | $0.053 | $0.080 | $0.079 |
| high | $0.211 | **$0.165** | $0.210 |

## 用途別ワークフロー(Claude が判断する基準)

ユーザの依頼に対して、Claude は以下のテーブルでパラメータを自動選択します。

| ユーザの意図 | size | quality | background | output-format | 備考 |
|---|---|---|---|---|---|
| ブログ/記事のヒーロー画像 | `1024x1536` | `medium` | `auto` | `webp` (圧縮 80) | 縦長で映える |
| OGP/SNS バナー | `1536x1024` | `high` | `auto` | `png` | 横長、文字くっきり |
| アイコン/ロゴ(透過必要) | `1024x1024` | `high` | — | `png` | gpt-image-2 で生成 → `rembg` で切り抜き、または `--model gpt-image-1.5 --background transparent` |
| ロゴ(白背景許容) | `1024x1024` | `high` | `auto` | `png` | 後で外部ツールで切り抜く前提 |
| 概念図/インフォグラフィック | `1024x1536` | `high` | `auto` | `png` | テキスト多め、ゴール伝達型プロンプト |
| 試行錯誤中 | `1024x1024` | `low` | `auto` | `png` | コスト圧縮 |
| 既存画像の部分修正 | (参照に合わせる) | `high` | `auto` | (参照に合わせる) | `--reference` + プロンプトで保持要素明示(自動高忠実度なので強い) |

## 姉妹スキルとの使い分け

| 用途 | 第一選択 | 理由 |
|---|---|---|
| 日本語/漢字テキスト入りポスター | **ccskill-gptimage** | gpt-image-2 のテキスト描画が最強 |
| 概念図/業務インフォグラフィック | **ccskill-gptimage** | 強い指示追従とテキストレイアウト |
| 既存画像の編集・部分修正 | **ccskill-gptimage** | 入力画像を常に最大忠実度で処理 |
| 写真風/イラスト風の単体ビジュアル | どちらでも | コスト感は近い |
| 透過 PNG (ロゴ・アイコン・スプライト) | どちらでも | gpt-image-2 は不可だが (a) `--model gpt-image-1.5` 切替、(b) `rembg` 後処理、(c) ccskill-nanobanana のいずれも実用 |
| 4K 出力 | ccskill-nanobanana | gpt-image-2 は 2K まで |

## 出力ファイル

各画像と並列に **メタデータ JSON サイドカー**(`{画像名}.{ext}.json`)が保存されます。プロンプト・revised_prompt・パラメータ・タイムスタンプを含み、後で再現/微調整するときに使います。

## 制約

- **Organization Verification 必須**(未検証なら 403)
- **透過背景 (`background: transparent`) 非対応** — 指定すると 400。透過が必要なら `--model gpt-image-1.5` か rembg 後処理
- **`input_fidelity` は指定不要(常に最大忠実度で動作)** — 指定すると 400。これは欠落ではなく自動仕様
- 編集時の入力画像トークンが多くなりやすい(自動高忠実度処理のトレードオフ) — 不要な参照画像は渡さない
- レート制限: Tier 1 で 5 IPM(本番運用は Tier 3+ 推奨)
- タイムアウト: 高品質・複雑プロンプトは最大 2 分
- Function calling / Structured outputs 非対応
- レスポンスは `b64_json` のみ(URL は返らない)

## トラブルシューティング

- 403 Forbidden → Org Verification を確認
- レート超過 → Tier を上げる、または `--quality low` で間引く
- タイムアウト → ネットワーク確認、SDK のタイムアウトを 120 秒以上に
- 日本語が崩れる → 引用符 `" "` で正確に囲む、フォント指定(`serif Japanese font` 等)を明示
