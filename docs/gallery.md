# ccskill-gptimage Gallery

A working gallery showing **what you can actually make** with OpenAI gpt-image-2.
All 35 images here were generated during Phase 4 effect verification (2026-04-24) on **v2 of the SKILL — rebuilt from official primary sources**. **35/35 succeeded in one shot (zero regeneration)** / total cost ¥905 ($6.04).

Every image ships with a metadata JSON sidecar (`{name}.png.json`) containing the prompt, `revised_prompt`, and parameters — fully reproducible.

[日本語版はこちら / Japanese version](gallery.ja.md)

---

## Part 1 — Capability Survey (26 categories × `quality: high`)

A breadth-of-skill showcase. Each category is a single-shot generation from a structured prompt.

### 01. Photorealistic portrait

`1024×1536 / high` — natural light, editorial magazine look, shallow depth of field, `85mm portrait lens look`

<img src="../assets/capability-survey/categories/v2/01_photorealistic_portrait_woman.png" width="500" alt="photorealistic portrait">

---

### 02. Anime character (cel-shaded)

`1024×1024 / high` — clean line art, traditional Japanese armor, falling cherry blossoms

<img src="../assets/capability-survey/categories/v2/02_anime_warrior_character.png" width="500" alt="anime warrior">

---

### 03. Watercolor landscape

`1536×1024 / high` — wet-on-wet bleeding, East Asian composition, paper texture

<img src="../assets/capability-survey/categories/v2/03_watercolor_mountain_landscape.png" width="700" alt="watercolor landscape">

---

### 04. Isometric 3D interior

`1024×1024 / high` — placement, props, and color palette all faithful to the prompt

<img src="../assets/capability-survey/categories/v2/04_isometric_3d_reading_room.png" width="500" alt="isometric reading room">

---

### 05. UI mockup (English login)

`1024×1536 / high` — pixel-perfect inputs, buttons, and links all as specified; typography hierarchy (32pt/14pt/16pt) preserved

<img src="../assets/capability-survey/categories/v2/05_ui_mockup_login_english.png" width="400" alt="login UI mockup">

---

### 06. UI mockup (Japanese iOS Settings) ⚡

`1024×1536 / high` — "設定" / "機内モード" / "Wi-Fi" / "FEEDTAILOR-5G" / "サウンドと触覚" all rendered perfectly. SF-Symbol-style colorful icons match the spec. **Includes iPhone device frame — usable as a real product mockup.**

<img src="../assets/capability-survey/categories/v2/06_ui_mockup_settings_japanese.png" width="400" alt="iOS settings UI in Japanese">

---

### 07. Infographic (bar chart)

`1024×1536 / high` — values 12/18/27/35 retained perfectly, axis labels and the coral "+192% YoY" rendered verbatim. Cookbook 4.10 pitch-deck pattern applied for whiteboard-grade polish.

<img src="../assets/capability-survey/categories/v2/07_infographic_quarterly_revenue.png" width="400" alt="infographic">

---

### 08. System architecture (microservices)

`1024×1024 / high` — API Gateway → 3 services → 3 DBs (`auth_db` / `orders_db` / `payments_db`), Kafka, dashed arrows all as specified

<img src="../assets/capability-survey/categories/v2/08_flowchart_microservices_architecture.png" width="500" alt="microservices architecture">

---

### 09. Food photo (tonkotsu ramen)

`1024×1024 / high` — steam, opaque white broth, soft-boiled egg yolk, red pickled ginger — all photoreal

<img src="../assets/capability-survey/categories/v2/09_food_photo_tonkotsu_ramen.png" width="500" alt="tonkotsu ramen">

---

### 10. Architectural exterior render

`1536×1024 / high` — concrete-and-wood modern house, reflecting pool, golden hour

<img src="../assets/capability-survey/categories/v2/10_architectural_render_modern_house.png" width="700" alt="modern architecture render">

---

### 11. Hand-drawn pen sketch

`1024×1024 / high` — izakaya with paper lanterns, vending machines, overhead wires, a bicycle, urban-sketcher watercolor wash. Excellent depth and perspective in the alley.

<img src="../assets/capability-survey/categories/v2/11_hand_drawn_pen_sketch_tokyo_alley.png" width="500" alt="urban sketch tokyo alley">

---

### 12. Japanese 4-koma manga ⚡⚡

`1024×1536 / high` — **title "プログラマあるある" + all 4 speech bubbles fully readable Japanese.** "なぜか動いた…!" / "なぜ動くんだろう?" / "ちょっと整理しよう" / "なぜ動かない…!?" reproduced perfectly. **The glasses + gray hoodie character stays consistent across all 4 panels** (Cookbook 6.4 character-consistency pattern).

<img src="../assets/capability-survey/categories/v2/12_comic_4koma_japanese_programmer.png" width="400" alt="Japanese 4-koma manga">

---

### 13. Logo (VECTRA, abstract mark)

`1024×1024 / high` — single continuous line forming arrow→circle loop, amber dot, beautifully-spaced "VECTRA" wordmark

<img src="../assets/capability-survey/categories/v2/13_logo_abstract_mark_vectra.png" width="500" alt="VECTRA logo">

---

### 14. Japanese vertical poster (tategaki) ⚡⚡

`1024×1536 / high` — **"未来は描かれるものではない、計画されるものだ。" rendered vertically (tategaki) flawlessly!** Punctuation correctly placed using tatechūyoko. English subtitle and gold rule match the spec. This was unreachable on the gpt-image-1 generation.

<img src="../assets/capability-survey/categories/v2/14_japanese_poster_vertical_tategaki.png" width="400" alt="Japanese vertical poster">

---

### 15. Abstract art

`1024×1024 / high` — flowing coral / peach / lavender / gold ribbons, liquid-mercury feel, gallery quality

<img src="../assets/capability-survey/categories/v2/15_abstract_generative_ribbons.png" width="500" alt="abstract generative art">

---

### 16. Everyday snapshot (morning kitchen)

`1024×1024 / high` — hands pouring drip coffee, peaches / notebook / copper pot, 35mm film tone

<img src="../assets/capability-survey/categories/v2/16_daily_morning_kitchen.png" width="500" alt="morning kitchen snapshot">

---

### 17. Nature (misty old-growth forest)

`1536×1024 / high` — giant cedars, god-rays, ferns and moss, National-Geographic class

<img src="../assets/capability-survey/categories/v2/17_nature_misty_cedar_forest.png" width="700" alt="misty cedar forest">

---

### 18. Nature (coast at dawn)

`1536×1024 / high` — volcanic-rock coast, silk waves (long exposure), lone cypress, pastel sky

<img src="../assets/capability-survey/categories/v2/18_nature_volcanic_coast_dawn.png" width="700" alt="volcanic coast at dawn">

---

### 19. City night (Shibuya scramble after rain) ⚡

`1536×1024 / high` — diagonal scramble crossing, neon reflections in wet pavement, motion blur of umbrellaed crowds, towering vertical Japanese signage (`カラオケ 747` / `居酒屋 2F・3F` / `薬方` / `牛繁 焼肉` / `コンタクトのアイシティ` etc.) emerged on its own. Cinematic teal-orange grade.

<img src="../assets/capability-survey/categories/v2/19_urban_tokyo_rainy_night_v3.png" width="700" alt="Tokyo Shibuya scramble crossing at rainy night">

---

### 20. Vehicle (vintage cafe racer)

`1536×1024 / high` — British racing green cafe racer, brown leather seat, polished chrome exhaust, sunset cobblestones. The `no brand logos on the bike` constraint was honored perfectly.

<img src="../assets/capability-survey/categories/v2/20_vehicle_cafe_racer_motorcycle.png" width="700" alt="vintage cafe racer motorcycle">

---

### 21. Sports dynamic moment (bouldering)

`1024×1536 / high` ⭐ (cost-favorable) — full-body climber on an overhang, sunlit rock face, peak-moment capture

<img src="../assets/capability-survey/categories/v2/21_sports_rock_climbing_action.png" width="400" alt="rock climbing action">

---

### 22. Pet (Shiba Inu in autumn)

`1024×1024 / high` — smiling Shiba full body, autumn leaves, warm bokeh

<img src="../assets/capability-survey/categories/v2/22_pet_shiba_inu_autumn.png" width="500" alt="Shiba Inu in autumn park">

---

### 23. Wildlife (red fox in snow)

`1024×1024 / high` — alert gaze, fur on snow, wildlife-photo quality

<img src="../assets/capability-survey/categories/v2/23_wildlife_red_fox_snow.png" width="500" alt="red fox in snow">

---

### 24. Line drawing (fashion technique)

`1024×1024 / high` — single-weight clean black line, flowing dress, heels, Hermès-grade fashion sketch

<img src="../assets/capability-survey/categories/v2/24_line_drawing_fashion_sketch.png" width="500" alt="fashion line drawing">

---

### 25. Sumi-e (traditional ink wash)

`1536×1024 / high` — dark-ink heron, light-ink water and mist, washi-paper feel, single red seal (per the constraint), orthodox East Asian painting

<img src="../assets/capability-survey/categories/v2/25_sumi_e_heron_ink_wash.png" width="700" alt="sumi-e heron ink wash">

---

### 26. Kid's crayon doodle

`1024×1024 / high` — clumsy lines, family + house + sun + cat, wrinkled paper — authentic child-art recreation

<img src="../assets/capability-survey/categories/v2/26_kids_crayon_doodle_family.png" width="500" alt="kid's crayon doodle">

---

## Part 2 — Resolution × Quality Grid (same prompt × 9 cells)

The same prompt across 3 sizes × 3 quality levels = 9 cells, to visualize cost vs. quality.
**Prompt**: editorial portrait poster with Japanese title "本と珈琲" + English subtitle "Tokyo, Spring 2026"

### Cost table

| | 1024×1024 | 1024×1536 | 1536×1024 |
|---|---|---|---|
| **low** | $0.006 (¥1) | $0.011 (¥2) | $0.011 (¥2) |
| **medium** | $0.053 (¥8) | $0.080 (¥12) | $0.079 (¥12) |
| **high** | $0.211 (¥32) | **$0.165 ⭐ (¥25)** | $0.210 (¥32) |

⭐ = at the same `high`, portrait (1024×1536) is **cheaper** than square (1024×1024)

### 9-cell comparison

<table>
<tr>
  <th></th>
  <th>1024×1024</th>
  <th>1024×1536</th>
  <th>1536×1024</th>
</tr>
<tr>
  <th align="right">low<br><sub>¥1–2</sub></th>
  <td><img src="../assets/capability-survey/grid/v2/grid_book_coffee_1024x1024_low.png" width="200" alt="1024x1024 low"></td>
  <td><img src="../assets/capability-survey/grid/v2/grid_book_coffee_1024x1536_low.png" width="160" alt="1024x1536 low"></td>
  <td><img src="../assets/capability-survey/grid/v2/grid_book_coffee_1536x1024_low.png" width="240" alt="1536x1024 low"></td>
</tr>
<tr>
  <th align="right">medium<br><sub>¥8–12</sub></th>
  <td><img src="../assets/capability-survey/grid/v2/grid_book_coffee_1024x1024_medium.png" width="200" alt="1024x1024 medium"></td>
  <td><img src="../assets/capability-survey/grid/v2/grid_book_coffee_1024x1536_medium.png" width="160" alt="1024x1536 medium"></td>
  <td><img src="../assets/capability-survey/grid/v2/grid_book_coffee_1536x1024_medium.png" width="240" alt="1536x1024 medium"></td>
</tr>
<tr>
  <th align="right">high<br><sub>¥25–32</sub></th>
  <td><img src="../assets/capability-survey/grid/v2/grid_book_coffee_1024x1024_high.png" width="200" alt="1024x1024 high"></td>
  <td><img src="../assets/capability-survey/grid/v2/grid_book_coffee_1024x1536_high.png" width="160" alt="1024x1536 high ⭐"></td>
  <td><img src="../assets/capability-survey/grid/v2/grid_book_coffee_1536x1024_high.png" width="240" alt="1536x1024 high"></td>
</tr>
</table>

### Observations

1. **Japanese text renders almost perfectly even at low quality** ("本と珈琲" / "Tokyo, Spring 2026"). Text-rendering reliability **does not depend on the quality parameter**.
2. **Quality mostly affects detail** (skin texture, bokeh, fabric weave, environmental micro-detail). Composition, palette, and main elements are already locked in at `low`.
3. **`1024×1536 high` is the budget/quality sweet spot, proven in practice.** What you get for $0.165 matches or beats square `high` at $0.211 in many cases.
4. **Cost optimization heuristics (verified)**:
   - **Ideation / prototyping**: `1024×1024 low` (¥1) is enough to validate composition
   - **Blog hero / social banner**: `1024×1536 medium` (¥12) is the realistic pick
   - **Press / landing pages / print**: `1024×1536 high` (¥25) for full quality
   - **Square only when you actually need it**: `1024×1024 high` (¥32)

---

## Meta

- Total images: **35** (success rate 35/35 = **100%**)
- Total cost: about **$6.04 ≈ ¥905**
- Every image was generated in the **"user does not write the prompt"** workflow (intent only → Claude composes the prompt from SKILL.md and prior context)
