# PORTING_GUIDE.md

Android AI workflow template を新しい Android Repository に導入する手順です。

まず、GitHub の "Use this template" ボタンで新しいリポジトリを作成するか、`git clone <repo-url>` でローカルに取得してから、以下の手順を進めてください。

## 1. コピーするファイル

共通ファイル:

```text
templates/common/AGENTS.md
templates/common/CLAUDE.md
templates/common/.gitignore
templates/common/.ai/
templates/common/docs/
```

Claude Code を使う場合:

```text
templates/claude/.claude/
```

Codex を使う場合:

```text
templates/codex/.agents/
```

両方使う場合は両方コピーしてください。`.ai/instructions/roles/` は共通 SSoT なので重複管理しません。

## 2. 必須置換変数

| 変数 | 置換内容 | 例 |
|---|---|---|
| `{{APPLICATION_NAME}}` | アプリまたは Repository 名 | `ShoppingApp` |
| `{{BASE_PACKAGE}}` | Kotlin package の root | `com.example.shopping` |
| `{{BASE_PACKAGE_PATH}}` | `{{BASE_PACKAGE}}` の `.` を `/` にした path | `com/example/shopping` |
| `{{BASE_FRAGMENT_NAME}}` | 通常 Fragment の基底クラス名 | `BaseFragment` |
| `{{LOGGER_CLASS}}` | ロガークラス名 | `ProjectLogger` |
| `{{LOG_SOURCE_PROPERTY}}` | ログ出力元を表す property 名 | `logSource` |
| `{{DESIGN_SPEC_PATH}}` | 任意の既存 デザイン文書の path。デザイン文書がない場合は `N/A` | `docs/DESIGN_SYSTEM.md` / `N/A` |
| `{{WORK_BRANCH_PATTERN}}` | 標準作業 branch pattern。変更しない場合は `feature/<task-id>` | `feature/<task-id>` |
| `{{BASE_BRANCHES}}` | plan-command が自動 branch 作成できる base branch | `main` / `master` / `develop` |
| `{{DEBUG_VARIANT}}` | 標準 debug variant 名 | `Debug` |
| `{{ASSEMBLE_DEBUG_TASK}}` | debug build task | `./gradlew assembleDebug` |
| `{{UNIT_TEST_TASK}}` | unit test task | `./gradlew testDebugUnitTest` |
| `{{LINT_TASK}}` | lint task | `./gradlew lintDebug` |

## 3. DESIGN は任意

既に デザインシステム、Figma 運用、UI ガイドライン、コンポーネントカタログ がある Repository では、`docs/DESIGN.example.md` を使わないでください。`{{DESIGN_SPEC_PATH}}` には既存文書の path を入れ、AI workflow からその文書を参照させます。

デザイン文書が残っていないが既存 UI はある Repository では、`{{DESIGN_SPEC_PATH}}` を `N/A` にしてください。この場合、AI は既存画面、layout、resource、component、色定義、style/theme を判断基準として扱います。UI 判断を PR 中に明示共有したいタスクだけ `.ai/tasks/<task-id>/design.md` を作成します。

専用の デザイン文書がなく、新規 UI が多い Repository で AI に UI 仕様を明示したい場合だけ `docs/DESIGN.example.md` をコピーまたはリネームして使います。その場合は以下をプロダクトの デザインシステム に合わせて置換してください。

| 変数 | 内容 |
|---|---|
| `{{DESIGN_DIRECTION}}` | ビジュアル方針 |
| `{{UI_DENSITY}}` | 画面密度、余白感 |
| `{{DESIGN_KEYWORDS}}` | デザインを表すキーワード |
| `{{COLOR_PRIMARY}}` | 主要色 |
| `{{COLOR_ERROR}}` | エラー / 危険操作の色 |
| `{{TEXT_PRIMARY}}` | 本文テキスト色 |
| `{{TEXT_SECONDARY}}` | 補助テキスト色 |
| `{{BACKGROUND}}` | 画面背景 |
| `{{SURFACE}}` | カード / ツールバー / ダイアログの面 |
| `{{BORDER}}` | 区切り線 / 入力欄の枠線 |
| `{{TEXT_HEADING_SIZE}}` | 見出しテキストサイズ |
| `{{TEXT_HEADING_WEIGHT}}` | 見出しテキストの太さ |
| `{{TEXT_HEADING_LINE_HEIGHT}}` | 見出しテキストの行の高さ |
| `{{TEXT_BODY_SIZE}}` | 本文テキストサイズ |
| `{{TEXT_BODY_WEIGHT}}` | 本文テキストの太さ |
| `{{TEXT_BODY_LINE_HEIGHT}}` | 本文テキストの行の高さ |
| `{{TEXT_CAPTION_SIZE}}` | キャプションテキストサイズ |
| `{{TEXT_CAPTION_WEIGHT}}` | キャプションテキストの太さ |
| `{{TEXT_CAPTION_LINE_HEIGHT}}` | キャプションテキストの行の高さ |
| `{{BUTTON_RULES}}` | ボタン規約 |
| `{{INPUT_RULES}}` | 入力欄規約 |
| `{{CARD_RULES}}` | カード規約 |
| `{{LIST_RULES}}` | リスト規約 |
| `{{LOADING_DISPLAY}}` | Loading 状態の表示方針 |
| `{{SUCCESS_DISPLAY}}` | Success 状態の表示方針 |
| `{{EMPTY_DISPLAY}}` | Empty 状態の表示方針 |
| `{{ERROR_DISPLAY}}` | Error 状態の表示方針 |
| `{{OFFLINE_DISPLAY}}` | Offline 状態の表示方針 |

## 4. 最初に調整するファイル

1. `docs/PROJECT_GUIDELINES.md`
2. `docs/LOGGING.example.md` を `docs/LOGGING.md` にリネームして内容を確定
3. デザイン文書は任意。既存文書がある場合は `{{DESIGN_SPEC_PATH}}` をその path に置換する。文書がない場合は `N/A` にし、既存 UI を判断基準にする。新規 UI が多い場合だけ `docs/DESIGN.example.md` を参考に作成する。
4. `AGENTS.md` と `CLAUDE.md` のプロジェクト名
5. `.ai/instructions/roles/*.md` の標準 Gradle task と UI 技術
6. branch 運用。標準は `feature/<task-id>` です。業務の Git 運用が異なる場合は `docs/CUSTOMIZATION_GUIDE.md` の「branch 運用を変える場合」に従って変更してください。

既存 Repository にすでに `.gitignore` がある場合は、`templates/common/.gitignore` をそのまま上書きコピーせず、以下の行を既存 `.gitignore` へ追記してください。

```gitignore
# Local review outputs
.ai/tasks/*/review.md

# Legacy/local AI scratch files
.ai/*.md

# Tool-local settings
.claude/settings.local.json
```

## 5. module 構成の確認

標準は `app` / `feature` / `domain` / `data` / `core` です。移植先が異なる場合は、`docs/CUSTOMIZATION_GUIDE.md` の module 構成変更セクションに従って role と guidelines の両方を修正してください。

## 6. 動作確認

導入後に以下を確認してください。

- `/plan-command` または `$plan-command` が `.ai/tasks/<task-id>/plan.md` を作成できる
- `/implement-command` または `$implement-command` が `docs/PROJECT_GUIDELINES.md` と plan を読む
- `/review-command` または `$review-command` が `git diff HEAD` と plan を照合する
- `android-conventions` が Kotlin 編集時だけ補完レイヤーとして働く
