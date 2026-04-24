# UI Mockups

Cookbook 4.8。モバイル/Web アプリの UI モックアップ。**「実装済み製品として記述する」**(コンセプトアート用語を避ける)のが最大のコツ。

## 使い所

- モバイルアプリの設計スケッチ(iPhone / Android フレーム付き)
- Web サービスのダッシュボード
- プロダクトデモ用のスクリーンモック
- 営業資料用の UI ビジュアライゼーション

## Cookbook 引用

> "UI mockups should be described as if you're describing an implemented product: focus on layout, hierarchy, spacing, and actual interface elements. Avoid 'concept art' or 'design sketch' language—instead, describe it as if the app already ships."
> — [Cookbook 4.8](https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide)

**書き方のコツ**:
- `design sketch` / `mockup concept` は避ける → `real, well-designed, beautiful app` と書く
- レイアウトを具体的に: ヘッダ、リスト、セクション、情報階層
- 実在の UI 要素(ベンダー一覧、Today's specials、location/hours)を仮想のブランド名付きで
- デバイスフレーム指示(`Place the UI mockup in an iPhone frame.`)があると最終成果物が使いやすい

---

## プロンプト例(Cookbook 4.8)

```
Create a realistic mobile app UI mockup for a local farmers market.
Show today's market with a simple header, a short list of vendors with small photos and categories, a small "Today's specials" section, and basic information for location and hours.
Design it to be practical, and easy to use. White background, subtle natural accent colors, clear typography, and minimal decoration.
It should look like a real, well-designed, beautiful app for a small local market.
Place the UI mockup in an iPhone frame.
```

**パラメータ**: `size=1024x1536`(モバイル縦型), `quality=medium`(細かいラベル多用なら `high`)

**CLI 例**:
```bash
$CCSKILL_GPTIMAGE_DIR/venv/bin/python $CCSKILL_GPTIMAGE_DIR/generate_image.py \
  "Create a realistic mobile app UI mockup for a local farmers market. ..." \
  --size 1024x1536 --quality high \
  --output-name farmers_app_mock
```

---

## 応用: Web ダッシュボード

```
Create a realistic desktop web app dashboard UI for a DMARC monitoring SaaS called "MailGuard".
Layout: left sidebar with navigation items ("Overview", "Reports", "Domains", "Alerts", "Settings"), top bar with search and user avatar, main content area with:
- 4 small KPI cards (Pass Rate 94%, Fail 6%, Quarantine 0%, Total Messages 1.2M)
- A line chart showing pass rate trends over 30 days
- A table of recent domains with status badges
Design it like a real, well-designed, shipping product. Dark theme with teal accents, clean sans-serif typography (like Inter), ample whitespace.
No watermarks, no extra text, no stock imagery.
Render at standard desktop browser proportions.
```

**パラメータ**: `size=1536x1024`(横長 PC 画面), `quality=high`(ダッシュボードは小さなテキストが多いため必須)

---

## iPhone Frame 付きでより映えるパターン

Cookbook 原文の `Place the UI mockup in an iPhone frame.` は、**完成スクリーンショットではなく端末モックアップ**を作りたい時に効く。

```
Create a realistic product hero image showing the MailGuard mobile app UI inside an iPhone 15 Pro frame.
The phone is standing slightly tilted on a minimal desk, with soft morning light from a window.
Inside the screen: the app's main dashboard with DMARC alerts and pass-rate visualization.
Background: clean Scandinavian office, shallow depth of field, photorealistic.
```

**パラメータ**: `size=1024x1536`, `quality=high`

---

## gpt-image-2 固有の注意

- **UI モックアップには `quality=high` がほぼ必須**。`medium` だと小さなテキスト・アイコンが崩れる
- モバイル UI は縦長(`1024x1536`)、Web UI は横長(`1536x1024`)が自然
- **実在ブランドの UI を模倣するプロンプトは避ける**(`like Instagram`, `like Slack` など)— 独自ブランドで作る
- 細かい数値(`94%`, `1.2M`)はプロンプトに**直接書く**。モデル任せだと数値が変動する
- Cookbook 4.8 の `in an iPhone frame` 指示は強力 — 実際の端末モックアップに仕立ててくれる

## 出典

- Cookbook 4.8 UI Mockups
- URL: https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide
- 取得日: 2026-04-23
