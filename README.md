# インフォグラフィック生成ガイド

Antigravity（画像生成ツール）に渡す英語プロンプトの生成方法。

---

## 前提

Antigravityはテキストプロンプトしか受け取れないため、**YAMLファイルを直接参照することはできない**。「prompt.yamlを使って生成して」と伝えても機能しない。

---

## 方法A：Claudeに生成させてAntigravityに貼る（推奨）

Claudeに依頼すると、prompt.yaml + session yamlを読み合わせて英語プロンプトを出力する。
それをそのままAntigravityに1つのテキストとして貼り付けて生成する。

```
「session1 の id:4 のプロンプトを出して」
         ↓
Claudeが英語プロンプトを生成
         ↓
AntigravityにコピペしてPost
```

---

## 方法B：手でAntigravityに渡す

以下のテンプレートの `{{ }}` 部分だけ書き換えて、Antigravityにそのまま貼る。

```
Create a 16:9 landscape infographic at 2560×1440px. White background with a thin
light gray grid. Black text throughout. Pastel colors for sticky notes and highlights.

Canvas instruction: If the output canvas is square, place the 16:9 image centered
in the white square with exactly 315px white margins top and bottom. All content
must stay within the central 1440×810px area. The image must not touch the top or
bottom edges of the canvas.

Header (top of slide, no background band):
- Top-left: "{{ heading }}" — 24px bold, pastel highlighter color block directly
  beneath the text
- Top-right: "Git / GitHub 実践講座" — 16px light weight, pastel highlighter
  color block directly beneath the text

{{ description を英語に直した内容 }}

ALL text must be EXACTLY as written in Japanese.
```

書き換えるのは2箇所のみ：
- `{{ heading }}` → session yaml の `heading` の値
- `{{ description を英語に直した内容 }}` → session yaml の `description` を英語に展開した内容
