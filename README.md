# Android AI Workflow Repository

Android Repository に AI 駆動開発ワークフローと実装規約を導入するためのテンプレート集です。

このテンプレートは、単に AI 用の command / skill を置くだけではなく、Android 開発で AI に何を守らせるかまで一緒に移植するためのものです。

## このリポジトリの位置づけ

このリポジトリは、Android プロジェクトへ AI 駆動開発ワークフローを持ち込むための **独立した配布テンプレート** です。monorepo の一部としてではなく、単体で `git clone` または GitHub の "Use this template" で採用できます。

## 採用方法

このリポジトリには 2 段階の導入手順があります。

1. **リポジトリの取得**: GitHub の "Use this template" ボタンで新しいリポジトリを作成するか、`git clone` でローカルに取得する。
2. **テンプレートファイルのコピー**: 取得したリポジトリ内の `templates/` から必要なファイルを移植先の Android プロジェクトにコピーする。

"Use this template" で作成した新しいリポジトリはこのテンプレートリポジトリ全体の雛形です。実際に Android プロジェクトへ組み込むファイルは `templates/` 配下にあります。詳細は「導入の最短手順」を参照してください。

> **`.gitignore` / `.gitattributes` の 2 層構造**: このリポジトリの root にある `.gitignore` と `.gitattributes` はテンプレート開発リポジトリ自身のためのファイルです。採用先 Android プロジェクトへコピーする配布物は `templates/common/.gitignore` です。この 2 つは別物です。

## まず読むもの

導入前に、次の順で確認してください。

1. `README.md`: このテンプレートの目的と全体像を把握する。
2. `templates/common/docs/AI_WORKFLOW.md`: plan から review 完了までの実運用フロー、branch gate、Claude / Codex の対応関係を確認する。
3. `docs/PORTING_GUIDE.md`: 移植先 Repository へコピーするファイルと置換変数を確認する。
4. `docs/CUSTOMIZATION_GUIDE.md`: module 構成、branch 運用、Compose 移行、デザイン文書の扱いを移植先に合わせて調整する。
5. `docs/DECISIONS.md`: なぜこの構成にしているかを確認する。

## AI 駆動開発の全体像

このテンプレートの中心は `templates/common/docs/AI_WORKFLOW.md` です。

```text
plan-command -> design-command(任意) -> implement-command -> review-command
      |                    |                     |                   |
      v                    v                     v                   v
  plan.md              design.md              実装差分           review.md
```

標準フロー:

- `plan-command`: 要件から `.ai/tasks/<task-id>/plan.md` を作成する。標準ではこの command だけが作業 branch を作成または移動できる。
- `design-command`: 任意。UI 判断を AI と明示共有したい場合だけ `.ai/tasks/<task-id>/design.md` を作成する。
- `implement-command`: plan と、存在する場合は design / review に従って実装する。
- `review-command`: plan と `git diff HEAD` を照合し、`.ai/tasks/<task-id>/review.md` に `PASS` または `CHANGES_REQUESTED` を保存する。

Claude Code では `/plan-command`、Codex では `$plan-command` のように呼び出します。詳細な branch 判定、成果物の Git 管理、差し戻し時の繰り返し手順は `templates/common/docs/AI_WORKFLOW.md` を参照してください。

## 移植する構成

この Repository は、以下をセットで移植することを目的にします。

- `.ai/instructions/roles/` をロール定義の唯一の参照元にする。
- Claude Code 用の `.claude/commands/` と `.claude/agents/` を提供する。
- Codex 用の `.agents/skills/` を提供する。`*-command` が workflow 入口、`*-agent` 名が agent wrapper。
- Android 開発向け `PROJECT_GUIDELINES.md` を提供する。
- KDoc / logging / UiState / UiEvent を補完する `android-conventions` skill を提供する。
- 任意利用の design workflow と design spec テンプレートを提供する。
- 移植先で微修正するための導入・カスタマイズ手順を提供する。

## 標準 Android 構成

標準構成は次を前提にします。

```text
<android-app>/
├── app/        # アプリケーションモジュール。entry point と DI graph を統合する
├── feature/    # UI 層。Fragment / ViewModel / Adapter / UiState / UiEvent
├── domain/     # business logic 層。UseCase interface / Repository interface / Model
├── data/       # data 層。Room / Retrofit / Repository implementation / DI binding
└── core/       # 共通 UI / utility。Extension / base class / lifecycle helper / logging
```

依存方向:

```text
app -> feature -> domain <- data
app ->            domain <- data
          feature <- core
                    data <- core
```

## 導入の最短手順

1. `templates/common/` を移植先 Repository root にコピーする。
2. 使用する AI ツールに応じて `templates/claude/` または `templates/codex/` をコピーする。
3. `templates/common/docs/AI_WORKFLOW.md` を読み、標準の branch 運用をそのまま使うか判断する。
4. `docs/PORTING_GUIDE.md` の置換変数を移植先の値へ置換する。
5. `docs/PROJECT_GUIDELINES.md` を移植先の module / package / logger / UI 技術に合わせて微修正する。
6. 既存 デザインシステム がある場合は `{{DESIGN_SPEC_PATH}}` をその文書 path にする。ない場合は `N/A` にし、既存 UI を判断基準として扱う。
7. `/plan-command <task-id> <要件>` または `$plan-command <task-id> <要件>` で最初のタスクを開始する。

## 置換対象

主な置換変数は `{{APPLICATION_NAME}}`、`{{BASE_PACKAGE}}`、`{{BASE_FRAGMENT_NAME}}`、`{{LOGGER_CLASS}}`、`{{DEBUG_VARIANT}}` です。デザイン文書がない場合、`{{DESIGN_SPEC_PATH}}` は `N/A` にします。詳細は `docs/PORTING_GUIDE.md` を参照してください。
