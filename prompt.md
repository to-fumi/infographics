# インフォグラフィック生成プロンプト

## 使い方

インフォグラフィック1枚につき、以下の手順で生成する。

1. **初回生成**：`[ベースプロンプト]` + `[各インフォグラフィックのコンテンツ]` を組み合わせて生成する
2. **文字清書の再生成**：初回生成画像に対して `[文字清書プロンプト]` を使って文字のみ清書する。**文字以外（レイアウト・キャラクター・背景・付箋の位置・色・絵）は一切変更しない**

---

## ベースプロンプト

```
An educational infographic illustration in 16:9 aspect ratio at 2560x1440 resolution (2K).

BACKGROUND:
Cork board texture as the entire background. Realistic cork board with natural tan/brown tones, visible cork grain and texture. The board fills the entire canvas edge to edge.

CHARACTER:
A cute, round owl mascot wearing round glasses. The owl has big expressive eyes behind the glasses, soft feathers, and a friendly expression. The owl is actively engaged — holding a pen, pointer stick, or Git-related props (git branch diagram cards, commit history scroll, sticky note pad) depending on the scene. The owl stands or sits at the side of the composition, pointing toward or interacting with the content.

LAYOUT:
- Title block: top-left corner, fixed position. White or pale yellow label/tag with a bold Japanese title text. Font size is consistently large and fixed across all infographics.
- Main content area: arranged with colorful sticky notes (付箋) in yellow, blue, pink, and green. Each sticky note contains a key point, short label, or diagram element. Sticky notes are pinned with small push pins or tape.
- Diagrams and illustrations: hand-drawn style sketches, arrows, flowcharts, and icons drawn directly on the cork board or on larger paper sheets pinned to the board. Line art style, clean but slightly sketchy.
- The owl character occupies a natural corner or edge of the composition, integrated with the layout without covering key content.

STYLE:
Warm, approachable, educational illustration style. Slightly hand-crafted feel. All text is in Japanese. Clean readable fonts for labels. Consistent visual hierarchy: title > section headers > body text on sticky notes.

RESOLUTION: 2560x1440, 16:9 ratio, high detail.
```

---

## 文字清書プロンプト

> 初回生成画像に対してこのプロンプトを適用する。文字の読みやすさだけを改善し、それ以外は変えない。

```
Refine only the Japanese text in this image to make it cleaner and more legible.
Keep all of the following exactly as they are — do not modify, move, resize, or redraw them:
- Layout and composition
- Character (owl) position, pose, and appearance
- Background (cork board texture)
- Sticky note positions, sizes, colors, and shapes
- Illustrations, diagrams, arrows, and decorative elements
- Color palette

Only improve:
- Japanese text clarity and readability
- Font rendering sharpness
- Text alignment within sticky notes and labels
- Title text legibility (top-left position, same font size)

Do not add, remove, or rearrange any content. Do not change the visual style.
Output at the same 2560x1440 resolution, 16:9 ratio.
```

---

## 各インフォグラフィックのコンテンツ

各セクションのコンテンツをベースプロンプトに追加して使用する。

---

### 第1回 — 1-1：ファイル管理の地獄と、Gitが解決すること

```
CONTENT FOR THIS INFOGRAPHIC:
Title (top-left): 「ファイル管理の地獄とGitの救済」

Left half — "Before Git" zone:
Sticky notes showing chaotic file names:
- 企画書_最終版.docx
- 企画書_最終版2.docx
- 企画書_最終版_田中修正.docx
- 企画書_本当に最終.docx
- 企画書_本当に最終_確認済.docx
Red warning sticky notes: 「どれが最新？」「誰が変えた？」「上書きされた！」
The owl looks confused or alarmed, holding a question mark card.

Right half — "After Git" zone:
One clean document icon pinned to the board.
Sticky notes showing: 「誰が」「いつ」「何を」「なぜ変えたか」 — all recorded.
Green checkmark sticky notes: 「過去に戻れる」「変更理由が残る」「消えない」
The owl looks satisfied, holding a Git logo card or branch diagram.

Center divider:
A bold arrow or line between the two zones. Label: 「Gitがあると…」
```

---

### 第1回 — 1-2：Gitを理解する3つの柱

```
CONTENT FOR THIS INFOGRAPHIC:
Title (top-left): 「リポジトリ・コミット・ブランチ」

Main layout — nested structure drawn on a large paper sheet pinned to the cork board:

Outermost area labeled 「リポジトリ」with a sticky note explanation: 「プロジェクト全体の倉庫。.gitフォルダに全履歴が入る」

Inside the repository area:
A horizontal line labeled 「mainブランチ」with dots along it labeled as コミット
Each commit dot has a small sticky note: 「いつ・誰が・何を・なぜ」
A branching line diverges from main, labeled 「featureブランチ」

Three sticky notes — one for each concept:
- 黄色: 「リポジトリ＝プロジェクトの倉庫」
- 青: 「コミット＝セーブポイント」
- ピンク: 「ブランチ＝作業の流れを表す線」

The owl stands beside the diagram holding a pointer stick, pointing at the nested structure.
```

---

### 第1回 — 1-3：なぜ add が必要なのか

```
CONTENT FOR THIS INFOGRAPHIC:
Title (top-left): 「3つの場所を理解する」

Main diagram — three columns drawn on a large paper sheet pinned to the board:

Column 1: 「作業ディレクトリ」
Icon of files scattered/being edited.
Sticky note: 「ファイルを編集する場所。まだGitには伝わっていない」

Arrow between column 1 and 2 labeled 「git add」

Column 2: 「ステージングエリア」
Icon of selected files lined up neatly.
Sticky note: 「次のコミットに含める変更を選ぶ場所」
Small note: 「関係ある変更だけを選べる」

Arrow between column 2 and 3 labeled 「git commit」

Column 3: 「リポジトリ（履歴）」
Icon of a sealed record / history log.
Sticky note: 「正式な履歴として記録される」

Bottom sticky note (green): 「カメラの例：散らかった部屋 → 撮影用に並べる → シャッターを切る」

The owl holds a camera prop or a checklist, standing beside column 2.
```

---

### 第1回 — 1-4：あなたのPC と GitHub

```
CONTENT FOR THIS INFOGRAPHIC:
Title (top-left): 「ローカルとリモートの関係」

Main diagram — two large zones on the cork board:

Left zone labeled 「ローカル（自分のPC）」:
Computer icon or laptop sketch.
Sticky note: 「オフラインでも操作できる作業場所」

Right zone labeled 「リモート（GitHub）」:
Cloud/server icon.
Sticky note: 「インターネット上の共有保管場所」

Arrows between the two zones:
- Top arrow left→right: 「push：変更をGitHubへ送る」（青い付箋）
- Bottom arrow right→left: 「pull：変更を手元に受け取る」（緑の付箋）
- Side arrow right→left (one-time): 「clone：最初の1回だけ複製を作る」（黄色の付箋）

Bottom note (pink sticky): 「自動では同期されない。push/pullは自分でタイミングを制御する」

The owl holds a push-pin or toggle switch, standing between the two zones.
```

---

### 第2回 — 2-1：なぜブランチを切るのか

```
CONTENT FOR THIS INFOGRAPHIC:
Title (top-left): 「mainを守るブランチ設計」

Top section — "mainに直接コミットすると…" (danger zone, red sticky notes):
- 「未完成コードが本番に入る」
- 「レビューなしで変更が通る」
- 「問題の原因特定が難しい」

Main diagram — branch flow on large paper sheet:
A horizontal main line labeled 「main（常に動く・壊れていない）」
A feature branch diverges, shows commits, then merges back to main.
Labels: 「ブランチを切る」「作業」「PR・レビュー」「マージ」

Right side — naming rules sticky notes:
- 「feature/xxx — 新機能」
- 「fix/xxx — バグ修正」
- 「docs/xxx — ドキュメント」
- 「refactor/xxx — 整理」

Bottom sticky (green): 「ブランチを切る＝分岐して作業する。mainに影響しない安全地帯を作る」

The owl wears a hard hat or holds a branch diagram card, pointing at the flow.
```

---

### 第2回 — 2-2：Pull Requestとは何か

```
CONTENT FOR THIS INFOGRAPHIC:
Title (top-left): 「PRの流れとレビューの意味」

Main flow diagram — vertical or horizontal steps on large paper:
① featureブランチでコミット
② GitHubでPR作成（タイトル・説明を書く）
③ チームメンバーがレビュー・コメント
④ 修正があれば再コミット
⑤ Approveされる
⑥ mainへマージ

Right side — three review action sticky notes:
- 緑: 「Approve — 問題なし、マージOK」
- 赤: 「Request changes — 修正が必要」
- 青: 「Comment — 意見・質問」

Bottom left — culture sticky note (yellow):
「PRの説明には"何を変えたか"より"なぜ変えたか"を書く」
「レビューはコードに対して行う。人への指摘ではない」

The owl holds a magnifying glass or approval stamp, standing beside the flow.
```

---

### 第2回 — 2-3：コンフリクトは怖くない

```
CONTENT FOR THIS INFOGRAPHIC:
Title (top-left): 「コンフリクトの仕組みと解消手順」

Top section — what is a conflict:
Sticky note: 「AさんとBさんが同じファイルの同じ行を別々に変更してマージしようとした状態」
Sticky note (reassuring, green): 「エラーではない。Gitが正直に"ここは人間が決めて"と教えてくれている」

Center — conflict marker diagram on paper sheet:
Code block showing:
  <<<<<<< HEAD
  Aさんの変更内容
  =======
  Bさんの変更内容
  >>>>>>> feature/xxx
Labels pointing to each section explaining what HEAD and feature/xxx mean.

Right side — resolution steps sticky notes:
① 「ファイルを開いてどちらを採用するか編集する」
② 「マーカー行（<<<, ===, >>>）を全て削除する」
③ 「git add → git commit」

Bottom — prevention tip (blue sticky):
「予防：こまめにmainをpullしてfeatureブランチに取り込む」

The owl looks calm and confident, holding a conflict resolution card or two sticky notes being merged together.
```

---

### 第3回 — 3-1：GitHub Actionsとは

```
CONTENT FOR THIS INFOGRAPHIC:
Title (top-left): 「コードが変わるたびに自動で動く仕組み」

Top analogy sticky note (yellow):
「PowerAutomateを知っている人へ：GitHubのPowerAutomate。構造はほぼ同じ」

Main diagram — three-layer structure on large paper:
Layer 1: 「トリガー（いつ動くか）」
Examples: 「mainへのpush」「PRの作成」「毎日定時」「手動実行」

↓ arrow

Layer 2: 「ジョブ（何をするか）」
Examples: 「テストを実行」「コードをビルド」「スキャンをかける」

↓ arrow

Layer 3: 「ステップ（個々の手順）」
Examples: 「コマンドを実行」「既製Actionを呼び出す」

Right side — use case sticky notes:
- 「PRごとにテスト自動実行」
- 「マージ後に自動デプロイ」
- 「セキュリティスキャン」

Bottom sticky (green):
「設定はYAMLファイルで書く（.github/workflows/）。JSON・YAML経験者には読みやすい」

The owl holds a gear/automation icon or a YAML scroll, looking enthusiastic.
```

---

### 第3回 — 3-2：仕組みで品質を守る3層構造

```
CONTENT FOR THIS INFOGRAPHIC:
Title (top-left): 「CodeQL・Copilot Review・Ruleset」

Main diagram — three horizontal layers on large paper sheet:

Layer 1 (top, red-toned sticky notes): 「CodeQL — セキュリティの脆弱性を検出」
Description: 「PR作成・push時に自動スキャン。SQLインジェクション・XSSなどを検出」
Badge: 「Settings → Security → Code scanning でボタンON」

Layer 2 (middle, blue-toned sticky notes): 「Copilot PR Review — AIがコード品質をレビュー」
Description: 「PRを作ると自動でコードを読んでコメント。人間のレビューを補助する」

Layer 3 (bottom, green-toned sticky notes): 「Ruleset — ブランチ運用ルールを強制」
Description: 「mainへの直pushを禁止・PR必須・承認必須をシステムで強制する」

Right side summary sticky (yellow):
「3つが揃うと…コードが変わるたびに
 セキュリティ・品質・プロセスの3層で
 自動チェックされる状態になる」

Bottom sticky (pink):
「個人の意識に頼らず、仕組みで品質を守る」

The owl wears a shield or holds a checklist with three checkmarks, looking proud.
```
