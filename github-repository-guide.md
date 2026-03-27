# GitHub リポジトリ運用ガイド

## はじめに

このドキュメントでは、GitHub にリポジトリを作成した後の **ブランチ運用・命名規則・PR（Pull Request）の使い方・リポジトリの設定** について説明します。

前提: リポジトリは作成済みで、Initial Commit が `main` ブランチに Push されている状態からスタートします。

---

## 1. ブランチ戦略

### 全体像

```
main （本番環境 / リリース済みのコード）
 │
 ├── release/v1.0.0 （リリース準備中のコード）
 │
 └── develop （開発中のコード。次のリリースに向けた統合先）
      │
      ├── feature/add-login-form （新機能の開発）
      ├── fix/email-validation （バグ修正）
      ├── chore/update-eslint-config （機能に関係ない雑務）
      └── hotfix/critical-crash （本番の緊急修正 ※ main から直接分岐）
```

### 各ブランチの役割

#### `main` — 本番ブランチ

- **常にリリース可能な状態** を維持する
- 直接コミットは禁止。必ず PR 経由でマージする
- ここにあるコードは「お客様に届いているもの」と考える

#### `develop` — 開発統合ブランチ

- 次のリリースに向けて、完成した機能が集まる場所
- Feature ブランチや Fix ブランチはここに向けて PR を出す
- `develop` 自体にも直接コミットはしない（PR 経由を推奨）

#### `feature/*` — 新機能開発ブランチ

- `develop` から分岐し、`develop` に向けて PR を出す
- 1つの機能 = 1つの Feature ブランチ
- 機能が完成して `develop` にマージしたら削除する

#### `fix/*` — バグ修正ブランチ

- `develop` から分岐し、`develop` に向けて PR を出す
- 開発中に見つかったバグの修正に使う
- 修正が完了して `develop` にマージしたら削除する

#### `chore/*` — 雑務ブランチ

- `develop` から分岐し、`develop` に向けて PR を出す
- アプリの機能自体に影響しない変更に使う
- 例: ライブラリのアップデート、Linter 設定の変更、README の更新、CI の修正

#### `release/*` — リリース準備ブランチ

- `develop` から分岐し、リリース準備が整ったら `main` にマージする
- リリース直前の最終調整（バージョン番号の更新、最終テストなど）を行う場所
- `main` にマージした後、同じ内容を `develop` にもマージして同期する

#### `hotfix/*` — 緊急修正ブランチ

- **`main` から直接分岐** する（`develop` からではない）
- 本番で発生した致命的なバグをすぐに直すためのブランチ
- 修正後、`main` と `develop` の両方にマージする

### ブランチの流れまとめ

| ブランチ | 分岐元 | マージ先 | 用途 |
|----------|--------|----------|------|
| `main` | — | — | 本番。直接コミット禁止 |
| `develop` | `main`（初回のみ） | `main`（release 経由） | 開発統合 |
| `feature/*` | `develop` | `develop` | 新機能 |
| `fix/*` | `develop` | `develop` | バグ修正 |
| `chore/*` | `develop` | `develop` | 雑務・設定変更 |
| `release/*` | `develop` | `main` + `develop` | リリース準備 |
| `hotfix/*` | `main` | `main` + `develop` | 本番の緊急修正 |

---

## 2. ブランチ命名規則

### フォーマット

```
<種別>/<簡潔な説明（英語・ケバブケース）>
```

### ルール

- すべて **小文字** を使う
- 単語の区切りは **ハイフン `-`** を使う（アンダースコアやスペースは使わない）
- 説明は **短く・具体的に** 書く（2〜4単語が目安）
- Issue番号がある場合は先頭に付けてもよい

### 命名例

| 種別 | 例 | 説明 |
|------|-----|------|
| feature | `feature/add-login-form` | ログインフォームの追加 |
| feature | `feature/123-user-profile-page` | Issue #123 のユーザープロフィールページ |
| fix | `fix/email-validation-error` | メールバリデーションのバグ修正 |
| chore | `chore/update-dependencies` | 依存ライブラリの更新 |
| chore | `chore/add-eslint-config` | ESLint 設定の追加 |
| release | `release/v1.0.0` | バージョン 1.0.0 のリリース準備 |
| hotfix | `hotfix/fix-login-crash` | ログイン時のクラッシュの緊急修正 |

### 避けるべき命名

| NG例 | 理由 |
|------|------|
| `Feature/LoginForm` | 大文字が混在。小文字を使う |
| `feature/login_form` | アンダースコア。ハイフンを使う |
| `fix/bug` | 何のバグかわからない。具体的に書く |
| `my-branch` | 種別プレフィックスがない |
| `feature/add-login-form-and-also-fix-header-and-update-readme` | 1ブランチに複数の変更を詰め込みすぎ |

---

## 3. コミットメッセージ規則

### フォーマット

[Conventional Commits](https://www.conventionalcommits.org/) に基づいた形式を採用します。

```
<種別>: <変更内容の要約>
```

### 種別の一覧

| 種別 | 意味 | 例 |
|------|------|-----|
| `feat` | 新しい機能の追加 | `feat: ログインフォームを追加` |
| `fix` | バグの修正 | `fix: メールアドレスのバリデーションエラーを修正` |
| `docs` | ドキュメントの変更 | `docs: READMEにセットアップ手順を追加` |
| `style` | コードの見た目の変更（動作に影響なし） | `style: インデントを修正` |
| `refactor` | リファクタリング（機能追加でもバグ修正でもない） | `refactor: ユーザー認証ロジックを整理` |
| `test` | テストの追加・修正 | `test: ログイン機能のユニットテストを追加` |
| `chore` | ビルドやツールの変更 | `chore: ESLintの設定を更新` |
| `ci` | CI/CD の設定変更 | `ci: Trivy スキャンをCIに追加` |
| `perf` | パフォーマンス改善 | `perf: 画像の遅延読み込みを実装` |

### コミットメッセージのルール

- **1行目は50文字以内** を目安にする
- 種別の後にコロン `:` とスペースを入れる
- **何を変えたか** を書く（「なぜ」は PR の説明に書く）
- 日本語でも英語でもチームで統一されていれば OK

### 良い例・悪い例

| 良い例 | 悪い例 | 理由 |
|--------|--------|------|
| `feat: ユーザー検索機能を追加` | `update` | 何を変えたかわからない |
| `fix: 日付フォーマットが崩れる問題を修正` | `fix bug` | どのバグかわからない |
| `chore: prettierの設定ファイルを追加` | `色々修正した` | 種別がない。内容も不明 |
| `docs: APIエンドポイント一覧を追加` | `feat: READMEを更新` | ドキュメント変更は `docs` を使う |

---

## 4. Pull Request（PR）の運用

### PR を使う理由

1人で開発していても PR を使う意味があります。

- **CI（品質チェック）が自動で走る** — Linter、テスト、セキュリティスキャンが PR をトリガーに実行される
- **GitHub Copilot がコードレビューしてくれる** — 人間のレビュワーがいなくても、AI が問題点や改善案を指摘してくれる
- **変更の履歴が残る** — 何を・なぜ変えたかが PR 単位で整理される。後から振り返りやすい
- **`main` や `develop` を壊さない** — PR + CI で品質が保証された変更だけがマージされる

### PR のタイトル規則

コミットメッセージと同じフォーマットを使います。

```
<種別>: <変更内容の要約>
```

例: `feat: ユーザー検索機能を追加`

### PR テンプレート

リポジトリに `.github/pull_request_template.md` を作成しておくと、PR 作成時に自動で入力欄が表示されます。

```markdown
## 概要
<!-- この PR で何を変更したかを簡潔に書いてください -->


## 変更理由
<!-- なぜこの変更が必要かを書いてください -->


## 変更内容
<!-- 具体的な変更点をリストで書いてください -->
-
-

## テスト
<!-- どのようにテストしたかを書いてください -->
- [ ] ローカルで動作確認済み
- [ ] テストコードを追加 / 既存テストが通ることを確認

## スクリーンショット（UI変更がある場合）
<!-- 画面の変更がある場合は Before / After のスクリーンショットを貼ってください -->

```

### PR の流れ

```
1. develop から feature ブランチを作成
   $ git checkout develop
   $ git checkout -b feature/add-login-form

2. コードを書いて commit & push
   $ git add .
   $ git commit -m "feat: ログインフォームを追加"
   $ git push origin feature/add-login-form

3. GitHub 上で PR を作成（develop ← feature/add-login-form）

4. 自動で CI が走る（Lint、Test、SCA）

5. GitHub Copilot がレビューコメントを付ける

6. CI が通ったら Merge

7. feature ブランチを削除
```

### GitHub Copilot によるレビュー

人間のレビュワーがいない場合でも、GitHub Copilot を PR のレビュワーとして活用できます。

**設定方法:**
1. リポジトリの Settings → Copilot → Code review で有効化
2. PR が作成されると Copilot が自動でレビューコメントを付ける

**Copilot が指摘してくれること:**
- 潜在的なバグやエラーハンドリングの漏れ
- パフォーマンスの問題
- セキュリティ上の懸念
- コードの可読性に関する提案

> **注意:** Copilot のレビューは参考情報です。すべての指摘に対応する必要はありませんが、セキュリティやバグに関する指摘は必ず確認してください。

---

## 5. リポジトリ設定（Branch Protection Rules）

### なぜ設定が必要？

設定をしないと、誰でも `main` に直接 Push できてしまいます。CI のチェックを経ずに壊れたコードが本番に入るのを防ぐために、ブランチ保護ルールを設定します。

### `main` ブランチの保護設定

Settings → Branches → Add branch ruleset で設定します。

| 設定項目 | 推奨値 | 理由 |
|----------|--------|------|
| Require a pull request before merging | **有効** | 直接 Push を禁止し、必ず PR 経由にする |
| Required approvals | **0** | 1人開発が多いため。Copilot レビューで補完する |
| Require status checks to pass | **有効** | CI（Lint、Test、SCA）が通らないとマージできないようにする |
| Require branches to be up to date | **有効** | マージ前に最新の develop/main との競合がないことを保証する |
| Include administrators | **有効** | 管理者も例外なくルールに従う |
| Allow force pushes | **無効** | 履歴の強制書き換えを禁止する |
| Allow deletions | **無効** | main ブランチの削除を禁止する |

### `develop` ブランチの保護設定

| 設定項目 | 推奨値 | 理由 |
|----------|--------|------|
| Require a pull request before merging | **有効** | 直接 Push を禁止 |
| Required approvals | **0** | 1人開発でも PR + CI の流れを維持する |
| Require status checks to pass | **有効** | CI が通らないとマージできない |
| Allow force pushes | **無効** | 履歴の書き換え禁止 |

### Required Status Checks に登録するジョブ

「Require status checks to pass」を有効にしたら、どの CI ジョブが必須かを指定します。

| ジョブ名 | 対応する施策 |
|----------|-------------|
| `trivy-scan` | SCA（依存ライブラリの脆弱性チェック） |
| `lint` | Linter / Formatter チェック |
| `test` | テスト実行 |

> **注意:** ジョブ名は GHA テンプレートの `jobs:` で定義した名前と一致させる必要があります。

---

## 6. その他の推奨設定

### リポジトリの General 設定

Settings → General で以下を設定します。

| 設定項目 | 推奨値 | 理由 |
|----------|--------|------|
| Default branch | `develop` | 新しい PR のデフォルトのマージ先を develop にする |
| Allow merge commits | チームで選択 | 履歴の見え方の好み（下記参照） |
| Allow squash merging | **有効（推奨）** | PR の複数コミットを1つにまとめてマージ。履歴がきれいになる |
| Allow rebase merging | チームで選択 | 直線的な履歴にしたい場合に有効 |
| Automatically delete head branches | **有効** | マージ後に feature ブランチを自動削除。ブランチが溜まらない |

> **Squash merge を推奨する理由:** Feature ブランチ内で「WIP」「修正」「typo」のような細かいコミットがあっても、マージ時に1つのきれいなコミットにまとめられます。初心者にとってコミット履歴の管理負担が減ります。

### `.gitignore` の設定

リポジトリに含めるべきでないファイルを `.gitignore` に記載します。以下は対象スタック向けの推奨設定です。

```gitignore
# 依存パッケージ
node_modules/
__pycache__/
*.pyc
venv/
.venv/

# ビルド成果物
dist/
build/
*.egg-info/

# 環境設定（シークレットを含む可能性がある）
.env
.env.local
.env.*.local

# エディタ設定
.vscode/
.idea/
*.swp
*.swo
*~

# OS 生成ファイル
.DS_Store
Thumbs.db

# テスト・カバレッジ
coverage/
.coverage
htmlcov/
.pytest_cache/
```

### Issue テンプレート

`.github/ISSUE_TEMPLATE/` にテンプレートを用意しておくと、Issue 作成時に統一されたフォーマットで報告できます。

**バグ報告テンプレート（`.github/ISSUE_TEMPLATE/bug_report.md`）:**

```markdown
---
name: バグ報告
about: バグや不具合の報告
labels: bug
---

## バグの内容
<!-- 何が起きたかを簡潔に書いてください -->

## 再現手順
1.
2.
3.

## 期待する動作
<!-- 本来どう動くべきかを書いてください -->

## 実際の動作
<!-- 実際にどうなったかを書いてください -->

## 環境
- OS:
- ブラウザ:
- バージョン:
```

**機能リクエストテンプレート（`.github/ISSUE_TEMPLATE/feature_request.md`）:**

```markdown
---
name: 機能リクエスト
about: 新しい機能やの改善の提案
labels: enhancement
---

## 概要
<!-- どんな機能が欲しいかを簡潔に書いてください -->

## 背景・理由
<!-- なぜこの機能が必要かを書いてください -->

## 実現イメージ
<!-- どのように動作するかのイメージを書いてください -->
```

---

## 7. 新しいリポジトリを作ったときのチェックリスト

リポジトリを新規作成したら、以下を順番に設定してください。

- [ ] `.gitignore` を追加
- [ ] `develop` ブランチを `main` から作成
- [ ] Default branch を `develop` に変更
- [ ] `main` の Branch Protection Rule を設定
- [ ] `develop` の Branch Protection Rule を設定
- [ ] `.github/workflows/ci.yml` を作成（テンプレートを呼び出す）
- [ ] `.github/pull_request_template.md` を追加
- [ ] `.github/ISSUE_TEMPLATE/` にテンプレートを追加
- [ ] Linter 設定ファイルを追加（`ruff.toml`、`.eslintrc.cjs`、`.prettierrc` など）
- [ ] GitHub Copilot のコードレビューを有効化
- [ ] Secret Scanning / Push Protection が Org レベルで有効になっていることを確認
- [ ] Automatically delete head branches を有効化

---

## まとめ

| カテゴリ | ルール | 理由 |
|----------|--------|------|
| ブランチ戦略 | main / develop / feature / fix / chore / release / hotfix | 役割を明確にしてコードの流れを整理する |
| ブランチ命名 | `<種別>/<ケバブケースの説明>` | 一目で目的がわかる |
| コミットメッセージ | `<種別>: <変更内容>` | 履歴を検索・理解しやすくする |
| PR 運用 | すべての変更は PR 経由 | CI チェック + Copilot レビューを必ず通す |
| ブランチ保護 | main / develop への直接 Push 禁止 | 品質チェックを経ていないコードの混入を防ぐ |
| マージ方式 | Squash merge 推奨 | 履歴をきれいに保つ |
