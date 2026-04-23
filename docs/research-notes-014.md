# research-notes-014: input_fidelity / gpt-image-1.5 一次資料検証

調査日時: 2026-04-23
調査者: Claude Code(WebFetch + 実 API 検証 + 補助エージェント)
調査目的: issue #014 — Web Claude 由来の主張 A / B を一次資料で裏付け or 否定する

---

## 結論サマリ

| 主張 | 判定 | 根拠 |
|------|------|------|
| A: gpt-image-2 は常に自動で最大忠実度で入力画像を処理する | ✅ 確認(公式に明示) | OpenAI Image generation guide の本文 |
| B: gpt-image-1.5 は `background: transparent` をサポートし、フォールバックとして使える | ✅ 確認(一次資料 + 実 API) | 公式ドキュメント + 本リポジトリで実 API 生成成功 |

**結論**: Web Claude の再回答は両方とも正確だった。SKILL.md / README / CLAUDE.md / gptimageguide.md の現行記述(2026-04-23 修正版)は維持して問題ない。codex-reviewer 指摘の周辺整合性のみ別途修正(本 issue 内)。

---

## 主張 A: gpt-image-2 は「常時自動最大忠実度」か → ✅ 確認

### 一次資料

OpenAI 公式ドキュメント `https://developers.openai.com/api/docs/guides/image-generation` を WebFetch で取得(アクセス: 2026-04-23)。本文に以下の **明示的記述** を確認:

> "For `gpt-image-2`, omit this parameter; the API doesn't allow changing it because **the model processes every image input at high fidelity automatically**."

> "`gpt-image-2` always processes image inputs at high fidelity, image input tokens can be higher for edit requests that include reference images."

### 実 API での確認(issue #002 由来)

`input_fidelity` を `client.images.edit()` に渡すと:
- 400: `The model 'gpt-image-2' does not support the 'input_fidelity' parameter.`

→ **公式記述「always processes at high fidelity automatically」と整合**。「機能欠落ではなく常時最大忠実度」という Web Claude の解釈は OpenAI 公式のスタンスそのもの。

### ドキュメント反映

`SKILL.md` / `SKILL.ja.md` / `README.md` / `README.ja.md` / `CLAUDE.md` / `gptimageguide.md` 3-4 節の現行表現は **正確**。維持。

---

## 主張 B: gpt-image-1.5 は透過 PNG フォールバックとして使えるか → ✅ 確認

### 一次資料(根拠の一部)

同じ OpenAI Image generation guide に:

> "`gpt-image-2` doesn't currently support transparent backgrounds. Requests with `background: 'transparent'` aren't supported for this model."

→ "doesn't currently support … for **this model**" の表現により、他モデル(gpt-image-1 / gpt-image-1.5)では透過がサポートされることを示唆。

### 実 API 検証(本日実施)

```bash
venv313/bin/python generate_image.py \
  "A minimalist logo of a fox, flat vector, navy and gold" \
  --model gpt-image-1.5 \
  --background transparent \
  --output-format png \
  --quality medium
```

結果:
- ✅ 200 OK で PNG 生成成功
- ✅ 出力: `generated_images/20260423_111303.png`
- ✅ `file` コマンドで確認: `PNG image data, 1024 x 1024, 8-bit/color RGBA, non-interlaced`(アルファチャネル付き)
- ✅ ファイルサイズ 1.4MB
- ✅ メタデータ JSON サイドカーに `model: gpt-image-1.5` が記録されている
- ✅ 既存 `generate_image.py` の `--model gpt-image-1.5` パススルー経路が問題なく動作

→ Web Claude の主張は実 API でも完全に確認された。

### ドキュメント反映

`SKILL.md` の透過 3 択(rembg / gpt-image-1.5 / nanobanana)は **実証ベースで信頼できる** 状態。

---

## codex-reviewer 由来の整合性指摘と対応

| Severity | 指摘 | 対応 |
|---|---|---|
| High | `generate_image.py` の参照画像/マスクのファイルハンドル管理が壊れやすい(`finally` で `NameError` 可能性、`edit_kwargs["mask"].close()` の脆い参照) | ✅ commit `5194105` で `contextlib.ExitStack` に統一 |
| Med | `--background transparent` のガードが gpt-image-2 のみで他モデルだと裸の 400 になる | ✅ commit `b2590ce` でガードを「gpt-image-1* で始まらないモデルはエラー」に変更、5 ケースのテスト追加 |
| Med | `generate_image()` 関数自体に `input_fidelity` ガードなし(CLI 経由のみ) | ⏭️ Phase 1(#008 dispatcher 化)へ送り。現状ライブラリ用途は想定外 |
| Med | `gpt-image-1.5 + transparent` パススルー回帰テストなし | ✅ commit `b2590ce` の TestMainValidation で追加 |
| Low | `gptimageguide.md` セクション 4 のサンプルで `input_fidelity="high"` が gpt-image-2 と矛盾 | ✅ 本コミットでコメント化(コード例から削除し説明コメントに) |
| Low | README の `--background` 表に `transparent` 値が未記載 | ✅ 既に「`transparent` requires `--model gpt-image-1.5`」記述あり(誤検出) |
| Low | `SKILL.md` (英語版) に frontmatter なし | ✅ 既に frontmatter あり(誤検出) |

---

## 参考リンク一覧

| URL | アクセス日 | 内容 |
|---|---|---|
| https://developers.openai.com/api/docs/guides/image-generation | 2026-04-23 | input_fidelity / background transparent の挙動が明示されている **一次資料** |
| https://developers.openai.com/api/docs/models/gpt-image-2 | 2026-04-23 | モデル概要(パラメータ仕様は guide 側に集約されており、本ページには記載なし) |

---

## 関連 issue

- issue #002: 実 API 確認(background transparent / input_fidelity の 400 確認)→ 本検証で前提が確定。検証保留ノートを解除可
- issue #013: gpt-image-1.5 実 API 動作確認 → 本検証で完了相当(#013 の検証手順と同じコマンドで成功)
- issue #014: 本調査 → 完了

---

## メタ記録

- 一次資料取得手段: WebFetch(直接 https://developers.openai.com にアクセスし、HTML→Markdown 変換後に本文抽出)
- 補助: gemini-researcher(Gemini CLI quota 枯渇のため一次資料アクセスは不成立。判定材料にはせず)
- 補助: codex-reviewer(リポジトリ整合性レビュー、Read ベースで完了)
- 実 API 検証: $0.05 程度(gpt-image-1.5 medium 1024x1024 1 枚)
- 調査日時: 2026-04-23 10:50-11:20 JST
