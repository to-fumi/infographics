# コード品質ガイド

## はじめに

このドキュメントでは、私たちのリポジトリに導入するコード品質施策について説明します。  
すべての施策は **GitHub Actions（GHA）** を通じて自動で実行されるため、開発者が手動で何かを実行する必要は基本的にありません。

対象となる技術スタック: **Python / HTML / CSS / JavaScript / TypeScript / React / Vite**

---

## 1. Secret Scanning — Push Protection

### これは何？

コードの中に **誤ってコミットされたシークレット（秘密情報）** を検出し、Push自体をブロックする仕組みです。

### なぜ必要？

APIキーやパスワードが一度でもGitの履歴に入ると、後から消しても履歴には残り続けます。漏洩したキーを使って外部から不正アクセスされる事故は、業界全体で頻繁に起きています。「Pushする前に止める」ことで、そもそも事故を起こさないようにします。

### 検出されるものの例

- AWS Access Key / Secret Key
- GitHub Personal Access Token
- Slack Webhook URL
- データベース接続文字列に含まれるパスワード
- 秘密鍵ファイル（`.pem` など）の内容

### 実装方法

GHAのワークフローは不要です。GitHub の管理画面から設定を有効化するだけで動きます。

| 作業 | 担当 | 内容 |
|------|------|------|
| Secret Scanning の有効化 | Org管理者 | Org Settings → Code security and analysis → Secret scanning を有効化 |
| Push Protection の有効化 | Org管理者 | 同画面で Push protection を有効化 |

### 開発者から見た動き

- シークレットを含むコードを `git push` すると、**Pushが拒否**される
- エラーメッセージに「どのファイルの何行目にシークレットがあるか」が表示される
- シークレットを削除してからもう一度Pushすれば通る

---

## 2. SCA（Software Composition Analysis）— Trivy

### これは何？

プロジェクトが使っている **外部ライブラリ（依存パッケージ）に既知の脆弱性がないか** を自動でチェックする仕組みです。

### なぜ必要？

私たちが書くコードだけでなく、 `npm install` や `pip install` で入れるライブラリにもセキュリティの問題が見つかることがあります。実際、多くのセキュリティ事故はアプリ本体ではなく依存ライブラリの脆弱性が原因です。Trivyはこれを自動で検出してくれます。

### チェック対象

| ファイル | 対応言語 |
|----------|----------|
| `package.json` / `package-lock.json` / `yarn.lock` | JavaScript / TypeScript / React / Vite |
| `requirements.txt` / `poetry.lock` / `Pipfile.lock` | Python |

### 実装方法

GHA の Reusable Workflow として テンプレートリポジトリに作成し、各リポジトリから呼び出します。

**テンプレートリポ側（`sca-trivy.yml`）:**

```yaml
name: SCA - Trivy
on:
  workflow_call:

jobs:
  trivy-scan:
    runs-on: [self-hosted]
    steps:
      - uses: actions/checkout@v4

      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          severity: 'HIGH,CRITICAL'
          exit-code: '1'        # 脆弱性があればCIを失敗させる
          format: 'table'
```

**呼び出し側（各リポの `.github/workflows/ci.yml`）:**

```yaml
jobs:
  sca:
    uses: your-org/gha-templates/.github/workflows/sca-trivy.yml@v1
    secrets: inherit
```

### 開発者から見た動き

- PRを出すと自動でスキャンが走る
- 依存ライブラリにHIGH/CRITICALの脆弱性があると **CIが失敗** する
- 該当ライブラリをアップデートすれば解消される

---

## 3. Linter / Formatter

### これは何？

コードのスタイルや書き方のルールを **自動でチェック・統一** する仕組みです。

### なぜ必要？

チームで開発していると、人によってインデント・命名・import順などがバラバラになります。レビューで「ここスペース多い」「セミコロン付けて」みたいな指摘に時間を使うのは非効率です。Linterが自動で検出し、Formatterが自動で直すことで、レビューをロジックの議論に集中できるようにします。

### 言語ごとのツール選定

| 対象 | Linter | Formatter | 設定ファイル |
|------|--------|-----------|-------------|
| Python | **Ruff** (Lint + Format 両対応) | **Ruff** | `ruff.toml` |
| JavaScript / TypeScript / React | **ESLint** | **Prettier** | `.eslintrc.cjs` + `.prettierrc` |
| CSS | **Stylelint** | **Prettier** | `.stylelintrc.json` + `.prettierrc` |
| HTML | — | **Prettier** | `.prettierrc` |

> **Ruff を選んだ理由:** Python の従来ツール（flake8 + isort + black）を1つに統合でき、実行速度も圧倒的に速い。設定ファイルも1つで済む。
>
> **ESLint + Prettier を分けている理由:** ESLint はコードの問題検出（未使用変数、型エラーなど）、Prettier はフォーマット整形に特化。役割が違うため併用する。

### 実装方法

**テンプレートリポ側 — Python用（`lint-python.yml`）:**

```yaml
name: Lint - Python
on:
  workflow_call:
    inputs:
      python-version:
        type: string
        default: '3.12'

jobs:
  lint:
    runs-on: [self-hosted]
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: ${{ inputs.python-version }}

      - name: Install Ruff
        run: pip install ruff

      - name: Ruff check (lint)
        run: ruff check .

      - name: Ruff format check
        run: ruff format --check .
```

**テンプレートリポ側 — Frontend用（`lint-frontend.yml`）:**

```yaml
name: Lint - Frontend
on:
  workflow_call:
    inputs:
      node-version:
        type: string
        default: '20'

jobs:
  lint:
    runs-on: [self-hosted]
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: ${{ inputs.node-version }}

      - name: Install dependencies
        run: npm ci

      - name: ESLint
        run: npx eslint . --max-warnings=0

      - name: Prettier check
        run: npx prettier --check .

      - name: Stylelint
        run: npx stylelint "**/*.css"
```

**呼び出し側:**

```yaml
jobs:
  lint-python:
    uses: your-org/gha-templates/.github/workflows/lint-python.yml@v1

  lint-frontend:
    uses: your-org/gha-templates/.github/workflows/lint-frontend.yml@v1
```

### 開発者から見た動き

- PRを出すとLint/Formatチェックが自動で走る
- スタイル違反があると **CIが失敗** する
- ローカルで `ruff format .` や `npx prettier --write .` を実行すれば自動修正できる

> **ヒント:** 各リポの README にローカルでの修正コマンドを記載しておくと、初心者が迷わず対応できます。

---

## 4. テストコード

### これは何？

コードが **期待通りに動くかを自動で検証** する仕組みです。

### なぜ必要？

手動でブラウザを開いて動作確認するだけでは、変更のたびに全画面を確認するのは不可能です。テストコードがあれば、変更が既存の機能を壊していないかを **数秒〜数分で自動検証** できます。安心してコードを変更できるようになります。

### テストの種類

| 種類 | 何をテストするか | 例 |
|------|------------------|-----|
| ユニットテスト | 関数・クラス単体の動作 | 「この関数に3を渡すと9が返る」 |
| インテグレーションテスト | 複数モジュールの連携 | 「APIにリクエストを送るとDBに保存される」 |
| E2Eテスト | ユーザー操作をシミュレーション | 「ログイン画面でID/PWを入力するとダッシュボードに遷移する」 |

### 言語ごとのツール選定

| 対象 | テストフレームワーク | 備考 |
|------|---------------------|------|
| Python | **pytest** | デファクトスタンダード。シンプルに書ける |
| JavaScript / TypeScript / React | **Vitest** | Vite ベースのプロジェクトと相性が良く、高速 |
| E2E（必要に応じて） | **Playwright** | クロスブラウザ対応、CI向き |

> **Vitest を選んだ理由:** プロジェクトが Vite を使っているなら、設定をほぼ共有でき、Jest より高速に動作する。

### 実装方法

**テンプレートリポ側 — Python用（`test-python.yml`）:**

```yaml
name: Test - Python
on:
  workflow_call:
    inputs:
      python-version:
        type: string
        default: '3.12'

jobs:
  test:
    runs-on: [self-hosted]
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: ${{ inputs.python-version }}

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run tests
        run: pytest --tb=short -q
```

**テンプレートリポ側 — Frontend用（`test-frontend.yml`）:**

```yaml
name: Test - Frontend
on:
  workflow_call:
    inputs:
      node-version:
        type: string
        default: '20'

jobs:
  test:
    runs-on: [self-hosted]
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: ${{ inputs.node-version }}

      - name: Install dependencies
        run: npm ci

      - name: Run Vitest
        run: npx vitest run
```

**呼び出し側:**

```yaml
jobs:
  test-python:
    uses: your-org/gha-templates/.github/workflows/test-python.yml@v1

  test-frontend:
    uses: your-org/gha-templates/.github/workflows/test-frontend.yml@v1
```

### 開発者から見た動き

- PRを出すとテストが自動で走る
- テストが1つでも失敗すると **CIが失敗** する
- ローカルで `pytest` や `npx vitest` を実行して事前に確認できる

---

## 全体構成まとめ

### テンプレートリポの構成

```
your-org/gha-templates/
  .github/workflows/
    sca-trivy.yml          # SCA: 依存ライブラリの脆弱性チェック
    lint-python.yml        # Linter: Python (Ruff)
    lint-frontend.yml      # Linter: JS/TS/CSS (ESLint + Prettier + Stylelint)
    test-python.yml        # Test: Python (pytest)
    test-frontend.yml      # Test: Frontend (Vitest)
```

### 各リポの呼び出し例（フルスタックの場合）

```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:

jobs:
  sca:
    uses: your-org/gha-templates/.github/workflows/sca-trivy.yml@v1
    secrets: inherit

  lint-python:
    uses: your-org/gha-templates/.github/workflows/lint-python.yml@v1

  lint-frontend:
    uses: your-org/gha-templates/.github/workflows/lint-frontend.yml@v1

  test-python:
    uses: your-org/gha-templates/.github/workflows/test-python.yml@v1
    secrets: inherit

  test-frontend:
    uses: your-org/gha-templates/.github/workflows/test-frontend.yml@v1
```

### 施策一覧

| 施策 | 目的 | 実装方法 | 開発者の対応 |
|------|------|----------|-------------|
| Secret Scanning (Push Protection) | シークレット漏洩の防止 | Org設定で有効化 | シークレットをコードに含めない |
| SCA (Trivy) | 依存ライブラリの脆弱性検出 | GHA テンプレート | ライブラリのアップデート |
| Linter / Formatter | コードスタイルの統一 | GHA テンプレート | ローカルでformat実行 |
| テストコード | 動作の自動検証 | GHA テンプレート | テストコードを書く・通す |

---

## 導入ロードマップ（推奨）

| フェーズ | 施策 | 理由 |
|----------|------|------|
| Phase 1 | Secret Scanning + Push Protection | 設定だけで完了。セキュリティ効果が最も即効的 |
| Phase 2 | SCA (Trivy) | テンプレート1つで全リポに展開可能 |
| Phase 3 | Linter / Formatter | 既存コードの修正が必要になるため段階的に導入 |
| Phase 4 | テストコード | テスト文化の定着に時間がかかるため最後。ただし新規リポからは早期に開始 |
