# AI_WORKFLOW.md

この Repository の AI 駆動開発は `.ai/`、`.claude/`、`.agents/` の 3 層で構成します。

## 全体像

```text
Claude Code                         Codex
.claude/commands/*-command.md        .agents/skills/*-command/SKILL.md
.claude/agents/*-agent.md            .agents/skills/*-agent/SKILL.md
.claude/skills/android-conventions   .agents/skills/android-conventions
        \                              /
         \                            /
          .ai/instructions/roles/*  # Single Source of Truth
                    |
              .ai/tasks/<task-id>/
```

Codex は `.agents/skills/` の下に command と agent が同居します。ディレクトリではなく、`*-command` と `*-agent` という名前で workflow 入口と agent wrapper を区別します。

## `.ai/`

`.ai/instructions/roles/` は role 定義の Single Source of Truth です。Claude Code と Codex はどちらもこの role 定義へ収束します。

`.ai/tasks/<task-id>/` は workflow 成果物の保存先です。

| ファイル | 内容 | Git 管理 |
|---|---|---|
| `plan.md` | 設計プラン | PR 中は管理対象 |
| `design.md` | Optional な UI 仕様 | 必要な場合のみ管理対象 |
| `review.md` | レビュー結果 | ローカル扱い推奨 |

## `.claude/`

- `commands/`: `/plan-command`、`/design-command`、`/implement-command`、`/review-command`
- `agents/`: commands から呼び出す agent wrapper。各 agent は `.ai/instructions/roles/` を Read して委譲する。
- `skills/android-conventions/`: Android 規約補完 skill

## `.agents/`

- `skills/plan-command`、`design-command`、`implement-command`、`review-command`: command skill。Claude の `commands/` に相当する workflow 入口
- `skills/architect-agent`、`designer-agent`、`implementer-agent`、`implementer-ui-agent`、`reviewer-agent`: agent skill。Claude の `agents/` に相当する agent wrapper
- `skills/android-conventions`: Android 規約補完 skill

## workflow

1. `plan-command <task-id> <要件>`: Claude は `architect-agent`、Codex は `architect-agent` skill を呼び、`.ai/tasks/<task-id>/plan.md` を作成する。
2. `design-command <task-id>`: Optional。Claude は `designer-agent`、Codex は `designer-agent` skill を呼び、UI 仕様を AI と明示共有したい場合だけ `.ai/tasks/<task-id>/design.md` を作成する。既存 design system が十分なら省略してよい。
3. `implement-command <task-id>`: plan と必要に応じて design / review に従い、Claude は `implementer-agent` / `implementer-ui-agent`、Codex は同名の `*-agent` skill を呼び分けて実装する。
4. `review-command <task-id>`: Claude は `reviewer-agent`、Codex は `reviewer-agent` skill を呼び、`git diff HEAD` と plan を照合して `review.md` を作成する。

## 実運用フロー

### 0. Branch Setup

標準では、workflow は `<task-id>` と `feature/<task-id>` branch を 1:1 で対応させます。

`plan-command` だけは branch 作成を担当します。`design-command`、`implement-command`、`review-command` は既に `feature/<task-id>` 上にいることを前提にし、自動で branch を作成しません。

この branch 運用は標準案です。業務の Git 運用が異なる場合は、後述の「Branch Policy の変更フロー」に従って変更してください。

| 現在 branch | `feature/<task-id>` の存在 | 動作 | ユーザー確認 |
|---|---|---|---|
| `feature/<task-id>` | 任意 | そのまま続行 | 不要 |
| `main` / `master` / `develop` | なし | `git switch -c feature/<task-id>` で作成して続行 | 不要 |
| `main` / `master` / `develop` | あり | `git switch feature/<task-id>` で移動して続行 | 不要 |
| 無関係な branch | なし | 停止し、現在 branch と期待 branch を報告する。承認後に作成する | 必要 |
| 無関係な branch | あり | 停止し、現在 branch と期待 branch を報告する。承認後に移動する | 必要 |
| detached HEAD | 任意 | 停止し、detached HEAD と期待 branch を報告する。承認後に作成または移動する | 必要 |

未コミット変更が `git switch` を妨げる場合は強行しません。commit または stash を人間に促してください。

branch が正しく `feature/<task-id>` になった後だけ、plan 生成へ進みます。

### 1. Plan

最初に plan を作成します。

```text
Claude Code: /plan-command <task-id> <要件>
Codex:       $plan-command <task-id> <要件>
```

実行内容:

- `<task-id>` が kebab-case であることを確認する。
- 上記 Branch Setup の判定表に従い、`feature/<task-id>` branch を準備する。
- `architect-agent` に要件を渡し、`.ai/tasks/<task-id>/plan.md` を作成する。
- 対象コードは変更しない。

成果物:

```text
.ai/tasks/<task-id>/plan.md
```

`plan.md` は以後の design / implement / review の基準です。実装前に必ず存在している必要があります。

### 2. Design（Optional）

UI 仕様を AI と明示共有したい場合だけ design を作成します。

```text
Claude Code: /design-command <task-id>
Codex:       $design-command <task-id>
```

実行する判断基準:

- 画面の見た目、状態表示、アクセシビリティ判断を PR 中に明文化したい場合は実行する。
- 既存 design system が十分にあり、既存 UI に合わせるだけなら省略してよい。
- `{{DESIGN_SPEC_PATH}}` が `N/A` の場合は、既存画面、layout、resource、theme、style、component を source of truth とする。

成果物:

```text
.ai/tasks/<task-id>/design.md
```

`design.md` は optional です。存在する場合だけ implement / review の入力として扱います。

### 3. Implement

plan に従って実装します。

```text
Claude Code: /implement-command <task-id>
Codex:       $implement-command <task-id>
```

実行内容:

- 現在 branch が `feature/<task-id>` であることを確認する。
- `.ai/tasks/<task-id>/plan.md` を読む。
- `.ai/tasks/<task-id>/design.md` が存在する場合は読む。
- `.ai/tasks/<task-id>/review.md` が存在し、先頭が `VERDICT: CHANGES_REQUESTED` の場合は差し戻し対応として扱う。
- plan の変更範囲に応じて logic / UI の担当を分ける。

Claude Code の担当分け:

```text
logic only -> implementer-agent
UI only    -> implementer-ui-agent
both       -> implementer-agent -> implementer-ui-agent
```

Codex でも同じ role 定義を `.ai/instructions/roles/` から参照します。

実装ルール:

- plan の step 順に進める。
- 変更範囲は必要最小限にする。
- review 差し戻しでは指摘箇所を中心に修正する。
- `.ai/tasks/<task-id>/review.md` の判定を書き換えない。
- 実装に対応するテストを追加または更新する。
- 可能な範囲で build / test / lint を実行する。

### 4. Review

実装後に review を実行します。

```text
Claude Code: /review-command <task-id>
Codex:       $review-command <task-id>
```

reviewer-agent に渡す材料:

- `.ai/tasks/<task-id>/plan.md`
- `.ai/tasks/<task-id>/design.md` が存在する場合はその内容
- `git status --short`
- `git diff HEAD`
- 未追跡ファイルがある場合はその内容
- 関連する既存コードとテスト

reviewer-agent は対象コードを変更せず、結果だけを保存します。

成果物:

```text
.ai/tasks/<task-id>/review.md
```

`review.md` はローカルレビュー結果です。`.gitignore` で `.ai/tasks/*/review.md` を除外します。

### 5. Review Result

`review.md` の先頭は必ず次のどちらかです。

```text
VERDICT: PASS
```

```text
VERDICT: CHANGES_REQUESTED
```

#### CHANGES_REQUESTED

修正が必要です。

```text
implement-command <task-id>
review-command <task-id>
```

を繰り返します。

運用ルール:

- `review.md` は削除しない。
- `implement-command` は `review.md` の未対応指摘を読む。
- 指摘と無関係な refactoring は混ぜない。
- 再 `review-command` で `PASS` になるまで完了扱いにしない。

#### PASS

実装レビュー通過です。

PASS 後に人間が判断すること:

- `plan.md` / `design.md` を長期保存するか。
- 長期保存する場合、`docs/` または `docs/adr/` に昇格するか。
- `.ai/tasks/<task-id>/` を cleanup するか。
- `review.md` の内容を PR comment や review UI に残すか。

AI は plan / design の削除や docs への昇格を勝手に実行しません。候補と理由だけ提示し、最終判断は人間が行います。

## 成果物の Git 管理

| ファイル | 扱い |
|---|---|
| `.ai/tasks/<task-id>/plan.md` | PR 中の共有成果物。原則 Git 管理する |
| `.ai/tasks/<task-id>/design.md` | Optional。作成した場合は PR 中の共有成果物として Git 管理する |
| `.ai/tasks/<task-id>/review.md` | ローカルレビュー結果。`.gitignore` で除外する |

`VERDICT: PASS` 後、plan / design を長期保存しない場合は、PR merge 前後に cleanup してよいです。cleanup の実行判断は人間が行います。

## branch gate

- `plan-command` は Branch Setup の判定表に従い、base branch (`main` / `master` / `develop`) から `feature/<task-id>` を作成または移動してよい。
- `design-command`、`implement-command`、`review-command` は現在 branch が `feature/<task-id>` でない場合に停止する。自動作成しない。
- `design-command` 自体の導入・実行は optional。
- 無関係な branch や detached HEAD では、勝手に移動せず人間の確認を求める。

## Branch Policy の変更フロー

branch 運用は業務ごとに異なるため、`feature/<task-id>` は標準値として扱います。移植先で異なる運用を採用する場合は、最初の運用開始前に以下を変更してください。

### 変更対象

| 対象 | 変更内容 |
|---|---|
| `docs/AI_WORKFLOW.md` | Branch Setup と branch gate の期待 branch、作成条件、停止条件 |
| `.claude/commands/plan-command.md` | `/plan-command` が作成または移動する branch pattern |
| `.agents/skills/plan-command/SKILL.md` | `$plan-command` が作成または移動する branch pattern |
| `.claude/commands/design-command.md` / `implement-command.md` / `review-command.md` | branch 一致条件 |
| `.agents/skills/design-command` / `implement-command` / `review-command` | branch 一致条件 |
| `AGENTS.md` / `CLAUDE.md` | workflow の branch 前提 |

### よくある変更例

| 運用 | branch pattern | 調整方針 |
|---|---|---|
| feature branch 標準 | `feature/<task-id>` | template default のまま使う |
| ticket branch | `<ticket-id>-<summary>` | `<task-id>` を ticket id として扱うか、task-id と branch 名の対応表を docs に追加する |
| GitHub Flow | `<user>/<task-id>` または `feature/<task-id>` | base branch 名と branch pattern だけ変更する |
| trunk-based | branch 作成なし / short-lived branch | `plan-command` の自動 branch 作成を無効化し、全 workflow で現在 branch 確認だけ行う |
| release branch 作業 | `release/<version>` 派生 | base branch に `release/*` を含めるか、人間確認必須にする |

### 変更時のルール

- branch policy は `docs/AI_WORKFLOW.md` を正とし、Claude commands / agents と Codex command skills / agent skills は同じ内容に揃える。
- `plan-command` だけが branch 作成を担当する原則を変える場合は、理由を `docs/DECISIONS.md` に追記する。
- `design-command` / `implement-command` / `review-command` が自動 branch 作成する運用は避ける。実装やレビューの途中で branch が変わると差分照合が不安定になる。
- detached HEAD や無関係な branch での自動移動は避け、人間確認を挟む。
- 未コミット変更が branch 移動を妨げる場合は、commit / stash / 作業中止の判断を人間に委ねる。

### 変更後の確認

変更後、以下がすべて同じ branch policy を説明していることを確認してください。

- `docs/AI_WORKFLOW.md`
- `.claude/commands/*.md`
- `.agents/skills/*/SKILL.md`
- `AGENTS.md`
- `CLAUDE.md`

## 補完 skill の位置づけ

`android-conventions` は KDoc、logging、UiState / UiEvent の品質補完です。task-id 検証、branch 操作、plan 作成などの workflow gate は持ちません。













